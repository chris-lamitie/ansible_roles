# hugepages
The hugepages role installs and manages various huge page management methods.  

Further reading: https://docs.kernel.org/admin-guide/mm/hugetlbpage.html and https://docs.kernel.org/admin-guide/mm/transhuge.html

## Requirements
None.

## Dependencies
None.

## Tags
**hugepages** - Runs all parts of this role.  

## Ansible Testing
This code has been tested with the following versions of Ansible
- Ansible-core 2.16.14

## OS Testing
The code has been tested to run from an Ansible executing node running:
- Rocky Linux 9

The code has been tested to run against servers running:
- Rocky Linux 9
- CentOS 7

## Role Variables
### Boolean toggles
**hugepages_managed**  
Type - Boolean  
Default - False  
Notes - Enables the role when true.  

**hugepages_sysctl**  
Type - Boolean  
Default - false  
Notes - Enables global hugepages via sysctl. Uses the default hugepages value depending on CPU architecture. On an x86 system, the default is 2M.  

**hugepages_grub**  
Type - Boolean  
Default - false  
Notes - Enables grub chagnes for huge pages Kernel parameters. See **hugepages_grub_arguments** for more information. Check if other grub management outside of this role is also Kernel cmdline paramaters, compatability issues could occur. Don't forget to reboot after this role makes Kernel changes.  

**hugepages_numa_nodes**  
Type - Boolean  
Default - false  
Notes - Enables hugepages_numa_nodes.yml for management of hugepages per NUMA node via writing to the /sys filesystem.  

**hugepages_thp_managed**  
Type - Boolean  
Default - false  
Notes - Enables management of *Transparent HugePages (THP)* on the system when true. THP is a Linux kernel feature that automatically backs virtual memory with huge pages, without requiring any changes to the application code. THP is a bit of a mixed bag, there are times when you want it, and times when you don't. Enabling this and setting the THP variables to 'never' will disable THP on a system. However, this role allows for changing THP if desired. Further reading: https://www.netdata.cloud/blog/understanding-huge-pages/ and https://docs.kernel.org/admin-guide/mm/transhuge.html  

### Other variables
**hugepages_count**  
Type - String  
Default - 0  
Notes - The value of how many hugepages to reserve. Used in *tasks/hugepages_sysctl.yml*. If you want to reserve some numbers per NUMA node, see *hugepages_numa_nodes* and *hugepages_numa_node_page_settings*.  

**hugepages_group_name**  
Type - String  
Default - empty  
Notes - The name of the huge pages system group. Used in *tasks/hugepages_group.yml*  

**hugepages_grub_arguments**  
Type - String
Default - empty
Notes - A string of arguments that are passed to grubby to insert into the Kernel's cmdline parameters. See https://docs.kernel.org/admin-guide/mm/hugetlbpage.html

**hugepages_numa_node_page_settings**  
Type - Dictionary of Lists  
Default - empty  
Notes - This is a list of NUMA nodes and some number of pages to reserve on that node. This is specific to CPU cores and should not be used with *hugepages_count*. This list is used in a stand-alone block, most often when *hugepages_size* is '2M', but also will be used in the *hugetlb-reserve-pages.sh* delivered in this role when *hugepages_size* is "1G". **REMINDER**: Know what nodes exist on the host before using this variable. Use: *ls /sys/devices/system/node/* on the host and see how many 'node' directories exist.

|Key|Type|Default|Notes|
|---|:-:|---|---|
|node|String|None|The NUMA node where a certain number of pages should be reserved.|
|number_of_pages|Interger|None|The number of pages to reserve on the NUMA node.|

e.g.:
```yaml
hugepages_numa_node_page_settings:
  - node: node0
    number_of_pages: 20
  - node: node2
    number_of_pages: 40
```

**hugepages_size**  
Type - String  
Default - 2M  
Notes - Set the size of huge pages on the system. On x86_64 systems this can be one of two values "2M" (aka huge pages), "1G" (aka gigantic pages). This value is used in multiple blocks, see hugepages_el9.yml for use. Also of note, when using "1G", this sets up a Red Hat recomended system unit file and script the system service file uses. That script uses values from *hugepages_numa_node_page_settings* variable. So, if you use "1G" besure to set *hugepages_numa_node_page_settings* as well.

**hugepages_thp_enabled**  
Type - String  
Default - empty  
Notes - This will set the value for */sys/kernel/mm/transparent_hugepage/enabled*. The options for this value are 'always' (the default), 'never', and 'madvise'. Use 'never' to disable THP.  

**hugepages_thp_defrag**  
Type - String  
Default - empty  
Notes -  This will set the value for */sys/kernel/mm/transparent_hugepage/defrag*. The options for this value are 'always' 'defer' 'defer+madvise' 'madvise' (the default), and 'never'. Use 'never' to disable THP degfragging.  
