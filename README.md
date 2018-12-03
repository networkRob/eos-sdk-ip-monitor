# EOS SDK IP Monitor

## Switch Setup
### Install
1. Copy `IpMon-x.x.x-x.swix` to `/mnt/flash/` on the switch or to the `flash:` directory.
2. Copy and install he `.swix` file to the extensions directory from within EOS.  Below command output shows the copy and install process for the extension.
```
7280-rtr-01#copy flash:IpMon-1.4.0-1.swix extensions:
Copy completed successfully.
7280-rtr-01#show extensions
Name                         Version/Release      Status      Extension
---------------------------- -------------------- ----------- ---------
IpMon-1.4.0-1.swix           1.4.0/1              A, NI       1
TerminAttr-1.5.0-1.swix      v1.5.0/1             A, I        24


A: available | NA: not available | I: installed | NI: not installed | F: forced
7280-rtr-01#extension IpMon-1.4.0-1.swix
7280-rtr-01#show extensions
Name                         Version/Release      Status      Extension
---------------------------- -------------------- ----------- ---------
IpMon-1.4.0-1.swix           1.4.0/1              A, I        1
TerminAttr-1.5.0-1.swix      v1.5.0/1             A, I        24


A: available | NA: not available | I: installed | NI: not installed | F: forced
```
3. In order for the extension to be installed on-boot, enter the following command:
```
7280-rtr-01#copy extensions: boot-extensions
```

### IP Monitor Agent Configuration
1. In EOS config mode perform the following commands for basic functionality (see step #4 for further customization):
```
config
daemon IpMon
exec /usr/bin/IpMon
no shutdown
```
2. By default, the agent has the following default values in terms of polling:
- Polling interval = 10 seconds
- Trigger threshold = 3 consecutive failed attempts
- VRF = default (VRF used on the switch by default)

To modify the default behavior, use the following commands to override the defaults:
```
config
daemon IpMon
option poll value {time_in_seconds}
option threshold value {number_of_failures}
option email value {email_address}
option vrf value {vrf_name}
option source value {source_intf}
```
**`time_in_seconds` **(optional)** how much time the agent should wait until it tries to poll the IP addresses*

***`number_of_failures` **(optional)** how many failures should occur consecutively before an action is triggered*

****`email_address` **(required for email alerting)** the email address for the agent to send an alert if the threshold has been reached*

****For email functionality to work, a working SMTP server needs to be setup on the switch to send emails*

*****`vrf_name` **(optional)** the name of the VRF that the pings should originate from (VRF name is case-sensitive)*

******`source_intf` **(optional)** interface to source ping from. ie `et44, et49_1, ma1, vlan100` See conversion list below for interface mappings*

##### Interface Mappings
- et44 --> Ethernet44
- et49_1 --> Ethernet49/1
- ma1 --> Management1
- vlan100 --> Vlan100

3. In order for this agent to monitor IP addresses, the following commands will need to be taken:
```
config
daemon IpMon
option {device_name} value {ip_of_device}
```
**`device_name` needs to be a unique identifier for each remote switch/device*

**`ip_of_device` needs to be a valid IPv4 address for the remote device for monitoring*

***To see what unique peer identifiers have been created, enter `show daemon IpMon`*

Example of a full `daemon IpMon` config would look like with all parameters specified
```
config
daemon IpMon
option cvx-01 value 192.168.50.16
option poll value 15
option threshold value 5
option email value test@domain.com
option vrf value MGMT
option source value vlan100
no shutdown
```

#### Sample output of `show daemon IpMon`
```
Arista#show daemon IpMon
Agent: IpMon (running with PID 11656)
Uptime: 0:00:04 (Start time: Fri Sep 28 11:10:44 2018)
Configuration:
Option       Value
------------ -------------
cvx-01       192.168.50.16
vrf          MGMT

Status:
Data                              Value
--------------------------------- ------------------------
cvx-01 has been DOWN since:       Fri Sep 28 11:09:28 2018
Arista#
Arista#show daemon IpMon
Agent: IpMon (running with PID 11656)
Uptime: 0:01:41 (Start time: Fri Sep 28 11:10:44 2018)
Configuration:
Option       Value
------------ -------------
cvx-01       192.168.50.16
vrf          MGMT

Status:
Data                            Value
------------------------------- ------------------------
cvx-01 has been up since:       Fri Sep 28 11:12:23 2018
```

#### Signs of an Error
In the below log output, a tell-tale sign that there is an error are repeated `IPMON Agent Initialized` logs:
```
Sep 28 11:33:25 veos-rtr-01 myIP-MON: %AGENT-6-INITIALIZED: Agent 'IpMon-IpMon' initialized; pid=14232
Sep 28 11:33:25 veos-rtr-01 myIP-MON: %myIP-6-LOG: IPMON Agent Initialized
Sep 28 11:33:27 veos-rtr-01 myIP-MON: %AGENT-6-INITIALIZED: Agent 'IpMon-IpMon' initialized; pid=14246
Sep 28 11:33:27 veos-rtr-01 myIP-MON: %myIP-6-LOG: IPMON Agent Initialized
Sep 28 11:33:31 veos-rtr-01 myIP-MON: %AGENT-6-INITIALIZED: Agent 'IpMon-IpMon' initialized; pid=14262
Sep 28 11:33:31 veos-rtr-01 myIP-MON: %myIP-6-LOG: IPMON Agent Initialized
Sep 28 11:33:34 veos-rtr-01 myIP-MON: %AGENT-6-INITIALIZED: Agent 'IpMon-IpMon' initialized; pid=14278
Sep 28 11:33:34 veos-rtr-01 myIP-MON: %myIP-6-LOG: IPMON Agent Initialized
```

If this occurs, check to make sure any optional configuration parameters are correct.