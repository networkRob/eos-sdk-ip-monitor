# EOS SDK IP Monitor - Python

#### Version 1.4
Introduced a .swix install for the Python version of the agent.

#### Version 1.3
##### Features
- Added logging outputs that shows when optional parameter options are updated/changed.  Showing previous value to new value.
```
Oct  1 12:28:29 veos-rtr-01 myIP-MON: %myIP-6-LOG: VRF value changed from default to ns-MGMT
```
- Added functionality to reset optional parameters to default by performing a `no` command. Below are examples for resetting each optional parameter to default:
```
config
daemon IpMon
no poll
no threshold
no email
no vrf
no srouce
end
```

##### Fixes
- Corrected an issue where if an IP address was inputed incorrectly for a device, updating the IP address wouldn't take the new IP.
- Corrected a logging issue where it would produe a `failed over X times` even though the failed count didn't reach that mark

#### Version 1.2
- Corrected an incorrect setting of the email parameter.  It was causing email functionality to fail.

#### Version 1.1
- Corrected an issue where the script only monitored the first device/IP address entered in, and ignores the remaining entered ones

#### Version 1.0
Inital release of the code.
