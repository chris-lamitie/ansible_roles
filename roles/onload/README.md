# onload
The onload role manages high performance user-level network stack, AKA 'Kernel Bypass'. Called 'onload', AKA OpenOnload, EnterpriseOnload. 

## Requirements
Onload drivers are accessable.

## Role Variables
**onload_managed**  
Type - Boolean  
Default - false  
Notes - Toggles role on when true.  

**onload_kmod**  
Type - Boolean  
Default - true   
Notes - Toggles role to install onload with kmod packages.  

**onload_dkms**  
Type - Boolean  
Default - false  
Notes - Toggles role to install onload with dkms packages.  

**onload_packages**  
Type - List  
Default - see onload/defaults/main.yml  
Notes - list of drivers and utility packages for onload.  

**onload_kmod_prereqs**  
Type - List  
Default - None.  
Notes - List of prereq packages before installing onload-kmod packages.  

**onload_dkms_prereqs**  
Type - List  
Default - None.  
Notes - List of prereq packages before installing onload-dkms packages.  

**onload_solarflare_gpgkeys**  
Type - List
Default - see onload/defaults/main.yml  
Notes - List of SolarFlare RPM signing keys to install on the host so SolarFlare compiled RPMS can be installed, such as 'sfutils'  

## Dependencies
------------
None.

## Tags
**onload** - Runs all parts of this role.  
