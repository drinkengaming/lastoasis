# Chat Snitch

These are various logging configurations to support sending in-game chat messages to an external collaboration service (discord, slack, etc).

## Dependencies

### General
- SSH Console access
- Superuser prviledges (su/sudo)

### OS
Tested on the following distributions so far:
- RHEL8
- RHEL9
- CentOS Stream 8
- CentOS Stream 9
- Ubuntu 20.04 LTS
- Ubuntu 21.04

### Packages
- syslog-ng-3.36  (or newer)
- syslog-ng-devel
- syslog-ng-http
  
(refer to your distribution documentation to install packages onto your system)

## Components
- filters.conf - a list of filters used to find the chat text in the log file
- parsers.conf - a list of parsers to find the right snippet of text in the log line
- lastoasis.conf - the overal message routing.

## Installation

- In /etc/syslog-ng/, create a conf.d directory
  
`mkdir -p /etc/syslog-ng/conf.d`

- Verify or add the following line at the bottom of /etc/syslog-ng/syslog-ng.conf

`@include "/etc/syslog-ng/conf.d/*.conf"`

- Add all three files (`filters.conf`, `parsers.conf`, `lastoasis.conf`) into `/etc/syslog/conf.d/`

- Restart syslog-ng
  `(sudo) systemctl start syslog-ng`
