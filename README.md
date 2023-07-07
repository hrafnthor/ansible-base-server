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
    password: non empty string <required>
    sudoer_file_nopasswd: boolean
    shell: [/bin/bash, /bin/zsh]
    disable_login: boolean
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

```
