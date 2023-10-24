# ansible-base-server
Common base actions needed on servers

### Host configuration file

The following are the options available with the task:

```yaml
#
# Operating System specific values
#
###########################################

ubuntu_version_name: non empty string <required>
systemctl:
  # sets the system swappiness level, i.e the aggressiveness 
  # at which the kernel may swap memory. 0 means least aggressive
  # while 100 is maximum aggressiveness.
  swappiness: [int]

#
#   OpenSSH configuration   
#
###########################################
ssh_users: 
  # single (or list of) user(s) that should have ssh access
  - [string]
# the ssh port to listen to [0, 65535]
ssh_port: [int] 

#
#   User configuration
#
###########################################

disable_root_login: boolean
user_groups: array of groups to create
  - id:	integer
    name: non empty string <required if creating group>

users:	array of users to create
  - id: integer
    name: non empty string <required>
    comment: non empty string
    group:
      id: integer	<required if group is defined>
      name: non empty string <required if group is defined>
    groups:
      - hrafn
      - sudo
    password: non empty string. Defaults to '!' (locked user)
    sudoer_file_nopasswd: boolean. Default false
    shell: [/bin/bash, /bin/zsh]
    disable_login: boolean. Default false
    create_home: boolean. Default true
    ssh:
      authorized_keys: array of non empty strings


#
#   UFW configuration
#
###########################################

# Existence of 'ufw' will install it.
ufw:
  state: [enabled, disabled, reloaded, reset]
  logging: [on, off, low, medium, high, full]
  defaults:
    incoming: [deny, allow, reject]
    outgoing: [deny, allow, reject]
  incoming:
    - comment: non empty string <required>
      policy: [allow, limit, deny, reject] <required>
      interface: non empty string
      delete: boolean. Removes rule if exists
      from_ip: ipv4/ipv6 number, defaults to 'any'
      to_port: integer between [0, 65535] <required>
      protocol: [any (default), tcp, udp, ipv6, esp, ah, gre, igmp]
  outgoing:
    - comment: non empty string	<required>
      policy: [allow, limit, deny, reject] <required>
      interface: non empty string
      delete: boolean. Removes rule if exists
      to_ip: ipv4/v6 number, defaults to 'any'
      to_port: integer between 0 and 65535 <required>
      protocol: [any (default), tcp, udp, ipv6, esp, ah, gre, igmp]

#
# fail2ban configuration
#
#########################################

# Existence of 'fail2ban' will install it.
fail2ban:
  # Indicates the state change that should be applied via systemd at the end of fail2ban configuration
  state: [started, reloaded, restarted, stopped]
  # If present, will cause a configuration file to be configured at '/etc/fail2ban/fail2ban.local'
  config:
    # Indicates if a backup should be made of the configuration file that might already exist.
    # Defaults to 'false'
    backup: [boolean]
    # If true, indicates that the file should be generated from a template path (referenced
    # file needs to be inside the /templates directory).
    # If false, then a direct file copy will be performed from the files path (referenced file 
    # needs to be inside /files directory).
    is_template: [boolean]
    # The file path relative to the type directory (templates/files).
    path: [string]
  # If present, will cause a jail file to be configured at '/etc/fail2ban/jail.local'
  jail:
    # Indicates if a backup should be made of any jail file that might already exist.
    # Defaults to 'false'.
    backup: [boolean]
    # If true, indicates that the file should be generated from a template path (referenced
    # file needs to be inside the /templates directory).
    # If false, then a direct file copy will be performed from the files path (referenced file 
    # needs to be inside /files directory).
    is_template: [boolean]
    # The file path relative to the type directory (templates/files).
    path: [string]
  # If present, will configure individual jails at '/etc/fail2ban/jail.d/name.local'
  jails:
    # (required) the name of the file without ending
    name: [string]
    # Indicates if a backup should be made of any jail files that might already 
    # exist with the given name. Defaults to false.
    backup: [boolean]
    # If true, indicates that the file should be generated from a template path (referenced
    # file needs to be inside the /templates directory).
    # If false, then a direct file copy will be performed from the files path (referenced file 
    # needs to be inside /files directory).
    is_template: [boolean]
    # The file path relative to the type directory (templates/files)
    path: [string]
  # If present, will configure individual jails at '/etc/fail2ban/filters/name.conf'
  filters:
    # (required) the name of the file without ending
    name: [string]
    # Indicates if a backup should be made of any jail files that might already 
    # exist with the given name. Defaults to false.
    backup: [boolean]
    # If true, indicates that the file should be generated from a template path (referenced
    # file needs to be inside the /templates directory).
    # If false, then a direct file copy will be performed from the files path (referenced file 
    # needs to be inside /files directory).
    is_template: [boolean]
    # The file path relative to the type directory (templates/files)
    path: [string]

#
#       Docker installation
#
######################################### 

# Existence of 'docker' will install docker
docker:
  # Existence of 'compose' will install compose
  compose:
      # the version number of compose to install. [required]
      version: [string]

#
#       logrotate configuration
#
#########################################

# The following variables are defined in the dependency 'robertdebock/ansible-role-logrotate'

# What the default frequency should be. If missing, defaults to weekly
logrotate_frequency: [daily, weekly, monthly]
# How many files to keep. If missing, defaults to 4
logrotate_keep: [int]
# If compression should be used. If missing, defaults to yes
logrotate_compress: [boolean]
# Individual log rotate entries
logrotate_entries:
    # (required) the name used for the logrotate file
  - name: [string]
    # (required) logrotate supported path to log file/s
    path: [string] 
    # overrides the default frequency value for this entry only
    frequency: [daily, weekly, monthly]
    # overrides the default logrotate keep value for this entry only.
    keep: [int]
    # overrides the default logrotate compress value for this entry only.
    compress: [boolean]
    # copy the original log file without modifying it.
    # see: https://man.archlinux.org/man/logrotate.8.en#copy
    copylog: [boolean]
    # truncate the original file to zero after creating a copy.
    # see: https://man.archlinux.org/man/logrotate.8.en#copytruncate
    copytruncate: [boolean]
    # postpone compression of the previous log file to the next rotation cycle.
    # see: https://man.archlinux.org/man/logrotate.8.en#delaycompress
    delaycompress: [boolean]
    # use yesterdays date rather than today when creating the 'dateext' extension
    # see: https://man.archlinux.org/man/logrotate.8.en#dateyesterday
    dateyesterday: [boolean]
    # command to run after log file rotation
    postrotate: [string]
    # immediatly after rotation the log file is created.
    # see: https://man.archlinux.org/man/logrotate.8.en#create
    create: [boolean]
    # if the log file is missing, go on to the next one without issuing an error message.
    missingok: [boolean]
    # if the log file is missing, issue an error. This is default.
    # see: https://man.archlinux.org/man/logrotate.8.en#notifempty
    nomissingok: [boolean]
    # the chmod to use when using 'create'
    create_mode: [string]
    # the user owner of the file when using 'create'
    create_user: [string]
    # the group owner of the file when using 'create'
    create_group: [string]
    # archive old versions of log files adding a date extension rather than numbers.
    # see: https://man.archlinux.org/man/logrotate.8.en#dateext
    dateext: [boolean]
    # the dateformat to use when using 'dateext'
    # see: https://man.archlinux.org/man/logrotate.8.en#dateformat
    dateformat: [string]
    # the size at which logs are rotate if they surpass it, but not until the next rotation 'frequency' is reached.
    # see: https://man.archlinux.org/man/logrotate.8.en#minsize
    minsize: [string]
    # do not rotate the log if it is empty.
    # see: https://man.archlinux.org/man/logrotate.8.en#notifempty
    notifempty: [boolean]
    # if true then scripts matched in rules are only run once even if they are defined for multiple rules.
    # see: https://man.archlinux.org/man/logrotate.8.en#sharedscripts
    sharedscripts: [boolean]

```
### Dependencies

#### [robertdebock/ansible-role-logrotate](https://github.com/robertdebock/ansible-role-logrotate)

### License

[Apache 2.0](https://github.com/hrafnthor/ansible-base-server/blob/main/LICENSE)

### Author

[Hrafn Thorvaldsson](https://github.com/hrafnthor)
