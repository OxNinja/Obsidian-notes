#cisco #network
## Install
To install packet tracer on an Arch based system one need to log into the https://www.netacad.com website.
The steps are:
1. clone the AUR `packettracer`
2. copy the `packettracer.deb` in the directory
3. and `makepkg -isc`
## Basics
**Straight** cable for different device types
**Cross** cable for same device types

| device   | type |
|----------|------|
| computer | 1    |
| server   | 1    |
| router   | 2    |
| switch   | 2    |

Cable speed: Gigabit-Ethernet > Fast-Ethernet > Ethernet
## Conventions
For better practices it is preferable to follow one convention and stick to it the whole process.
Here is one possible convention for networking:
* use `x.x.x.254` IP for routers, or the highest available in the sub-net
* use `x.x.x.1` IP for computers, or the lowest available in the sub-net
* use the lowest interface available with the following recommendations:
	* use the _best_ interface for big links
		* use Gigabit-Ethernet for **router to router** for example instead of **computer to switch**
	* **lowest** interface number priority to left of network
	* **highest** interface number for outgoing connections (ex: Gigabit-Ethernet 0/4 for WAN)
## General config
use `?` when lost in the console for a list of available commands and help on the current on-going command
## Router
`Would you like to enter the initial configuration dialog? [yes/no]: no`
`enable` == `sudo`
	`show ip route` show the dynamic and static routes of the router
	`conf t` to config the router
		`hostname <name>` change the name of the router
		`enable secret <password>` change the config password
		`int <interface> <id>` to config an interface
			`no shutdown` == `ip link set up dev int0`
			`ip addr <IP> <net-mask>` 
## Switch
`enable`
	`conf t`
		`hostname`
		``
## References
https://www.cisco.com/c/en/us/td/docs/routers/access/800M/software/800MSCG/routconf.html
