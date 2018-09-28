# eos-sdk-ip-monitor

## Switch Setup
1. Copy `IpMon` and `profileIP` to `/mnt/flash/` on the switch
2. Run the following command on the switch: 
```
bash /mnt/flash/./profileBFD
```
3. In EOS config mode perform the following steps to get the agent started:
```
config
daemon IpMon
exec /mnt/flash/IpMon
no shutdown
```
4. By default, the agent has the following default values in terms of polling:
- Polling interval = 10 seconds
- Trigger threshold = 3 consecutive failed attempts

To modify the default behavior, use the following commands to override the defaults:
```
config
daemon IpMon
option poll value {time_in_seconds}
option threshold value {number_of_failures}
option email value {email_address}
```
**`time_in_seconds` how much time the agent should wait until it tries to poll the IP addresses*

***`number_of_failurer` how many failures should occur consecutively before an action is triggered*

****`email_address` the email address for the agent to send an alert if the threshold has been reached*

****For email functionality to work, a working SMTP server needs to be setup on the switch to send emails*

4. In order for this agent to monitor IP addresses, the following commands will need to be taken:
```
config
daemon IpMon
option {device_name} value {ip_of_device}
```
**`device_name` needs to be a unique identifier for each remote switch/device*

**`ip_of_device` needs to be a valid IPv4 address for the remote device for monitoring*

***To see what unique peer identifiers have been created, enter `show daemon IpMon`*

Example
```
config
daemon IpMon
option cvx-01 value 192.168.50.16
```
5. To make this agent persist a reboot/power-cycle.  We will need to create a `rc.eos` file in the `/mnt/flash` directory of the switch.  The contents for `rc.eos` are as follows:
```
#!/bin/bash
/mnt/flash/./profileIpMon
```
