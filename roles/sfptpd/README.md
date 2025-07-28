# sfptpd role
The sfptpd role manages sfptpd on Linux systems with a solarflare card that has PTP licensing enabled in the firmware. This role manages sfptpd with PTP, or as a slave to a local clock provider, such as chronyd.

After installing via RPM, example configs and scripts (init.d or systemd) can be found in /usr/share/doc/sfptpd

Note: If you want to check the topology of sfptpd when it is running, use the following command: cat /var/lib/sfptpd/topology

## Requirements
A solarflare card with "PLUS" enablement or other license file installed in the firmware to use PTP. As of 2024-11-04, the following adapters are compatable with sfptpd version 8 & 9: X2541, X2542, X2522, X2522-25G, SFN8042, SFN8522, SFN8522M, SFN8542, SFN8722

## Role Variables
### sfptpd_managed
Type - Boolean  
Default - false  
Notes - When true, manages sfpdpd for low latency systems.

### sfptpd_packages
Type - List  
Default - sfptpd  
Notes - List of packages to install related to sfptpd.  

### sfptpd_nic  
Type - String  
Default - None  
Notes - The nic/interface/device name for PTP time.  

### sfptpd_affinity  
Type - String  
Default - None  
Notes - Set which CPU(s) to run sfptpd on. e.g. limit CPU selection to cores 3 and 4: "3,4". Used in sfptpd.service file.  

### sfptpd_mode  
Type - String  
Default - "chrony"  
Notes - "mode" to run sfptpd, either slaved to chrony, or "active" to manage system/hardware clock.  

## Dependencies
sfptpd rpm packages are available in system repos. This role does not manage yum/dnf repos.

## Tags
sfptpd
