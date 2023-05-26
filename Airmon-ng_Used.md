---
layout: default
---
[Back](./)

## Disrupt IP Camera
In this test I wanted to see if Blink cameras are vulnerable to jamming and cause them to not record. The diagram of how the Deauth attack will take place is blow. Once we find out the Mac address of the Sync Module we can send deauth packets at it using Airmon-ng and for the durration of the attack non of t he cameras will be able to record or save its recordings. 
![deauth](./assets/BlinkInfra.png)


