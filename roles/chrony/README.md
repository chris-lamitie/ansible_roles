# Chrony Role
The chrony role manages chrony as a time service on Linux systems. This should be used where hardware time stamping is not required.

## Requirements
None.

## Dependencies
This role uses *sfptpd_managed* from roles/sfptpd.

## Ansible Testing
This code has been tested with the following versions of Ansible
- Ansible-core 2.16.14

## OS Testing
The code has been tested to run from an Ansible executing node running:
- Rocky Linux 9

The code has been tested to run against servers running:
- Rocky Linux 9
- Rocky Linux 8
- CentOS 7

## Tags
chrony

## Role Variables
### manage_chrony
Type - Boolean  
Default - false  
Notes - When true, manages chronyd. Used for non PTP time low latency systems. Essentially generic Linux systems.

### chrony_time_servers
Type - List  
Default - pool 2.rocky.pool.ntp.org  
Notes - a list of pools or servers that are time sources for chronyd. Must specify "pool" or "server in the list.  
eg:
```yaml
# a pool list
chrony_time_servers:
  - "pool 2.rocky.pool.ntp.org"

# a server list
chrony_time_servers:
  - "server 10.71.3.62"
  - "server 10.71.3.10"
```

### chrony_iburst
Type - Boolean  
Default - true  
Notes - adds ' iburst' to the end of the **chrony_time_servers** list.  

### chrony_driftfile
Type - String  
Default - "/var/lib/chrony/drift"  
Notes - Location of the drift state gain/loss record  

### chrony_rtcsync
Type - Boolean  
Default - true  
Notes - Toggle automatic hardware clock sync.  

### chrony_makestep_threshold
Type - Interger  
Default - 10  
Notes - threshold for system clock slewing.

### chrony_makestep_updates
Type - Interger  
Default - 3  
Notes - The number of makestep updates to correct large time discrepancies.

### chrony_leapsectz
Type - String  
Default - "right/UTC"  
Notes - Handles leap seconds.

### chrony_noclientlog
Type - Boolean  
Default - true  
Notes - When true, disables logging of client access when using local only chrony setup.  

### chrony_logchange_threshold
Type - Interger or Decimal  
Default - 0.5  
Notes - Sets the frequency of sending messages to syslog about clock changes larger than the defined number in seconds. Monitors time adjustments.

### chrony_logdir
Type - String  
Default - "/var/log/chrony"  
Notes - Location of chrony's log file

### chrony_ntp_client_access_ip
Type - String  
Default - None  
Notes - Allows NTP client access from a network, used for time validation scripts.

## Tags
chrony
