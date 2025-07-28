# local_accounts
This role creates and maintains local accounts and their passwords on Linux systems. This is achieved by combining additional_users & default_users variables.  
**default_users** are universal and should go on every system.  
**additional_users** are situational and should be used in inventory group_vars for specific groups. i.e. 'webservers' vs 'databases'  

Additional reading: 
https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html
https://docs.ansible.com/ansible/latest/collections/ansible/builtin/group_module.html

## Requirements
None.  

## Role Variables
### local_accounts_managed
Type - Boolean  
Default - false  
Notes - Toggles role active when true  

### root_password
Type - String  
Notes - Stored in Ansible Vault, used for defining root password.  

### local_accounts_additional_users & local_accounts_default_users 
Type - List of Dictionaries  
Used with ansible.builtin.user module  

|Key|Type|Default|Notes|
|---|:-:|---|---|
|username|String|None|Username of the user to manage|
|uid|Interger|omit|User id of the account. If omitted, the next number in sequence after 1000 is used.|
|gid|Interger|omit|Group id of the account. If omitted, the next number in sequence after 1000 is used.|
|password|String|"!!"|The password for the user. "!!" means lock the account. "*" means no password set.|
|shell|String|/bin/bash|The shell for the user|
|group|String|omit|Sets the default group for the user if desired. i.e.: 'username.users' If omitted the module will use the username and uid for group nname and gid. i.e.: 'username.username'.|
|groups|String|omit|The additional groups for the user. If used, must also set 'append: true' parameter.|
|append|bool|false|Add user to groups noted or remove from all groups except stated.|
|home_dir|String|/home/<username>|The path to the homedir for the user.|
|create_home|bool|true|Should ansible manage the home dir and create it on user create.|
|comment|String|omit|Optionally sets the description of user account.|
|manage_bashrc|bool|false|Toggles the account for an Ansible managed bashrc file. If omitted, value will be false.|
|bashrc_lines|list|empty|A list of lines that are added to the account's .bashrc template. This can be replaced with a varaiable. See example below.|
|desktop|string|none|The name of the desired default graphical ui env for the user. A list of options can be found on any gdm system at /usr/share/xsessions/|

eg:
```yaml
local_accounts_additional_users:
  # full variable example
  - username: test_user_1
    group: test_user_1 # this should match the user name.
    groups: test_group_1 # used for other groups than this account. 
    uid: 3001 # the uid of the user acount
    gid: 3001 # the gid of the user account
    shell: "/bin/bash" 
    home_dir: /home/test_user_1
    comment: test1 account. Did not have hashed password to start
    password: "{{ test1_password }}"
    append: true
    manage_bashrc: true
    bashrc_lines:
      - 'alias t1="echo test_user_1"'
    desktop: gnome-classic
  # mixed use variable example
  - username: test_user_2
    group: test_user_2
    groups: test_group_1,test_group_2
    uid: 3002
    gid: 3002
    shell: "/bin/bash"
    home_dir: /home/test_user_2
    password: "{{ test2_password_hashed }}"
    comment: test_user_2 account. Has hashed password to start
    append: true
    manage_bashrc: true
    bashrc_lines: "{{ test_bashrc_lines }}"
    desktop: cinnamon
  # minimal variable example
  - username: test_user_3

test_bashrc_lines:
  - "alias ll='ls -la'"
  - 'alias test-cmd="echo test-command"'
```

### local_accounts_additional_groups & local_accounts_default_groups
Type - List of Dictionaries  
Used with ansible.builtin.group module  

|Key|Type|Notes|
|---|:-:|---|
|name|String|Name of the group|
|gid|String|Group id of the group|

eg:
```yaml
local_accounts_additional_groups:
  - group_name: demo
    gid: 2000
```

### local_accounts_additional_group_members
Type - List of Dictionaries  
Used to specify additional user accounts to be mebers of groups where the users are not local acounts. Such as LDAP or Active Directory. Uses usermod command.  

|Key|Type|Notes|
|---|:-:|---|
|username|String|Username to be members of groups|
|groups|String|The groups the user should be a member of. If more than one, make a comma separated list|

eg:
```yaml
local_accounts_additional_group_members:
  - username: user1
    groups: demo1
  - username: user2
    groups: demo1,demo2
```

### local_accounts_additional_ssh_keys & local_accounts_default_ssh_keys
Type - List of Dictionaries  
Used with ansible.builtin.group module  

|Key|Type|Default|Notes|
|---|:-:|---|---|
|username|String|**Required**|The account hame getting authorized ssh keys|
|authorized_keys|List|**Required**|The keys of a group, this is a list so more than one key can be provieded if desired.|
|key_type|String|rsa|This is the type of private/public key used. Defaults to rsa, but ed25519 should be used where possible.|
|private_key|String|none|The contents of the private key file. This should be stored in a secure vault.|
|public_key|String|none|The contents of the private key file. This can be stored in the clear or in a vault.|
|state|String|present|If the keys should or should (present) not (absent) be in the file.|
|path|Path|omit|Alternate path to the authorized_keys file. When unset, this value defaults to ~/.ssh/authorized_keys.|
|manage_dir|Boolean|true|Whether this module should manage the directory of the authorized key file. If set to true, the module will create the directory, as well as set the owner and permissions of an existing directory. Be sure to set manage_dir=false if you are using an alternate directory for authorized_keys, as set with path, since you could lock yourself out of SSH access.|

eg:
```yaml
admin_user_account_keys:
  - username: "test1" # full var example
    manage_dir: true
    state: present
    path:  /etc/ssh/authorized_keys/test1
    authorized_keys:
      - "{{ public_keys.test1 }}"
    key_type: "ed25519"
    private_key: "{{ test1_private_ssh_key }}"
    public_key: "{{ public_keys.test1 }}" 
  - username: "test2" # only required example, also list
    authorized_keys:
      - "{{ public_keys.test2 }}"
      - "{{ public_keys.other_key }}"
```

### local_accounts_bashrc_template
Type - file
Name and/or location of a bashrc file. Used via template task in tasks/main.yml

## Dependencies
None

## Tags
**local_accounts** - Runs all parts of this role.  
**local_users** - Runs only user and group creation/management.  
**ssh_keys** - Runs only ssh key and config creation/management.  
**bashrc** - Runs only bashrc creation/management.  
