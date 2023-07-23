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
#	UFW configuration
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
  enabled: boolean
  config:
    logging:
      level: [CRITICAL, ERROR, WARNING, NOTICE, INFO (default), DEBUG] 
      target: [STDOUT, STDERR (default), SYSLOG, SYSLOG, SYSOUT, SYSTEMD-JOURNAL, file-path]
      socket: [auto (default) | file-path] 
    socket: [file-path] defaults to '/var/run/fail2ban/fail2ban.sock'
    pidfile: [file-path] defaults to 'var/run/fail2ban/fail2ban.pid'
    allowipv6: boolean
    dbpurgeage: [integer | time abbreviation tokens (see below)] defaults to '1d'
    dbmaxmatches: [positive integer] defaults to 10

#
# Standalone software installations
#
######################################### 

# Existence of 'docker' will install docker
docker:
  # Existence of 'compose' will install compose
  - compose:
      # the version number of compose to install. [required]
      - version: [string]
```
