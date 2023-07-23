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
  swappiness: integer [0 to 100]

#
#   OpenSSH configuration   
#
###########################################
ssh_users: non empty string
ssh_port: integer [0, 65535]

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

fail2ban:
  # Indicates the state change that should be applied via systemd at the end of fail2ban configuration
  state: [started, reloaded, restarted, stopped]
  # If present, will cause a configuration file to be configured at '/etc/fail2ban/fail2ban.local'
  config:
    # Indicates if a backup should be made of the configuration file that might already exist.
    # Defaults to 'false'
    backup: [boolean]
    # If true, indicates that the file should be generated from the templates path.
    # If false, then a direct file copy will be performed from the files path.
    is_template: [boolean]
    # The file path relative to the type directory (templates/files).
    path: [string]
  # If present, will cause a jail file to be configured at '/etc/fail2ban/jail.local'
  jail:
    # Indicates if a backup should be made of any jail file that might already exist.
    # Defaults to 'false'.
    backup: true
    # If true, indicates that the file should be generated from the templates path.
    # If false, then a direct file copy will be performed from the files path.
    is_template: true
    # The file path relative to the type directory (templates/files).
    path: [string]
#
# Standalone software installations
#
######################################### 
docker: [boolean]

```
