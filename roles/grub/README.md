# grub
The grub role manages grub configuration on RHEL/Rocky/CentOS.

This role will manage grub via grubby or classic grub2-mkconfig commands. Since RHEL/CentOS7 and forward, grubby is the preferred method, but the classic method exists for edge cases where grubby is not preferred.

Do not mix these two methods, if you do, there will be confusing config file issues that you will have to manually sort out. That is not fun.

Note, see: https://www.gnu.org/software/grub/manual/grub/grub.html#Configuration for grub2 configuration documentation.

## Requirements
None.

## Dependencies
None

## Tags
grub

## Ansible Testing
This code has been tested with the following versions of Ansible
- Ansible-core 2.16.14

## OS Testing
The code has been tested to run from an Ansible executing node running:
- Rocky Linux 9

The code has been tested to run against servers running:
- Rocky Linux 9
- CentOS7

## Role Variables
### grub_managed
Type - Boolean  
Default - false  
Notes - Toggles the role on or off.

### grub_timeout: 
Type - Integer  
Default - 5  
Notes - Sets the timeout for the GRUB menu in seconds. Used in grub.j2.  

### grub_timeout_style: 
Type - String  
Default - "menu"  
Notes - Sets the timeout style for the GRUB menu. Used in grub.j2.  

### grub_distributor
Type - String  
Default - "\$(sed 's, release .*$,,g' /etc/system-release)"  
Notes - Stores and uses base distribution name. Double quotes must be escaped with '\'.  Used in grub.j2.  

### grub_default
Type - String  
Default - "saved"  
Notes - Sets the default boot entry. "saved" tells GRUB to boot the most recently used kernel by default. Used in grub.j2.  

### grub_disable_submenu
Type - Boolean  
Default - true  
Notes - Disables any submenus in the GRUB menu when true. Used in grub.j2.  

### grub_terminal_output
Type - String  
Default - "console"  
Notes - Specifies the output destination for GRUB messages during boot. Used in grub.j2.  

### grub_cmdline_linux
Type - String  
Default - "crashkernel=auto ipv6.disable=1 consoleblank=0"  
Notes - Defines kernel boot options passed to the Linux kernel. Used in grub.j2 and in 'grubby' portions of this role.   

### grub_disable_recovery
Type - Boolean  
Default - true  
Notes - Disables the GRUB recovery menu. This menu allows access to recovery tools in case of boot issues. Used in grub.j2.  

### grub_enable_blscfg
Type - Boolean  
Default - true  
Notes - Enables BLSCFG (bless configuration) support in GRUB. This feature allows managing the boot loader for UEFI (Unified Extensible Firmware Interface) systems. Enabling it ensures GRUB can interact with UEFI firmware for booting.

### grub_mkconfig_command
Type - String  
Default - "/sbin/grub2-mkconfig -o {{ grub_cfg_file }}"  
Notes - Generate a GRUB 2 configuration file.  'grub_cfg_file' is defined in tasks/grub.yml after detecting if the host is using BIOS or EFI boot methods.  

### grub_manual_config
Type - Boolean  
Default - false
Notes - Toggles the blocks in tasks/grub.yml so one can choose the grubby or classic grub management approaches. The default is 'false' because using grubby is recomended over grub2-mkconfig since RHEL 8 was relased.   
