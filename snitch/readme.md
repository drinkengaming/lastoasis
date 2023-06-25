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

- Verify syslog-ng is running

`systemctl status syslog-ng`
```
● syslog-ng.service - System Logger Daemon
   Loaded: loaded (/usr/lib/systemd/system/syslog-ng.service; enabled; vendor preset: enabled)
   Active: active (running) since Fri 2023-06-13 15:54:55 PDT; 1h 44min ago
     Docs: man:syslog-ng(8)
 Main PID: 49660 (syslog-ng)
    Tasks: 4 (limit: 203445)
   Memory: 7.5M
   CGroup: /system.slice/syslog-ng.service
           └─49660 /usr/sbin/syslog-ng -F -p /var/run/syslogd.pid
```

## Monitoring

You can use `syslog-ng-stats` to see how things are going

For Discord:

`/usr/sbin/syslog-ng-ctl stats | grep d_discord`

```
dst.http;d_discord#0;http,https://discord.com/api/webhooks/1111111111111111111/ABCDEFGHIJ-0AB-ABCD-abcdefghijklmnopqrstuvwxyz12345678901234567890;a;batch_size_max;96
dst.http;d_discord#0;http,https://discord.com/api/webhooks/1111111111111111111/ABCDEFGHIJ-0AB-ABCD-abcdefghijklmnopqrstuvwxyz12345678901234567890;a;batch_size_avg;94
dst.http;d_discord#0;http,https://discord.com/api/webhooks/1111111111111111111/ABCDEFGHIJ-0AB-ABCD-abcdefghijklmnopqrstuvwxyz12345678901234567890;a;eps_last_1h;5
dst.http;d_discord#0;http,https://discord.com/api/webhooks/1111111111111111111/ABCDEFGHIJ-0AB-ABCD-abcdefghijklmnopqrstuvwxyz12345678901234567890;a;dropped;1
dst.http;d_discord#0;http,https://discord.com/api/webhooks/1111111111111111111/ABCDEFGHIJ-0AB-ABCD-abcdefghijklmnopqrstuvwxyz12345678901234567890;a;processed;35428
dst.http;d_discord#0;http,https://discord.com/api/webhooks/1111111111111111111/ABCDEFGHIJ-0AB-ABCD-abcdefghijklmnopqrstuvwxyz12345678901234567890;a;queued;0
dst.http;d_discord#0;http,https://discord.com/api/webhooks/1111111111111111111/ABCDEFGHIJ-0AB-ABCD-abcdefghijklmnopqrstuvwxyz12345678901234567890;a;memory_usage;0
dst.http;d_discord#0;http,https://discord.com/api/webhooks/1111111111111111111/ABCDEFGHIJ-0AB-ABCD-abcdefghijklmnopqrstuvwxyz12345678901234567890;a;written;35428
dst.http;d_discord#0;http,https://discord.com/api/webhooks/1111111111111111111/ABCDEFGHIJ-0AB-ABCD-abcdefghijklmnopqrstuvwxyz12345678901234567890;a;eps_last_24h;8
dst.http;d_discord#0;http,https://discord.com/api/webhooks/1111111111111111111/ABCDEFGHIJ-0AB-ABCD-abcdefghijklmnopqrstuvwxyz12345678901234567890;a;msg_size_max;96
```

For Slack:

`/usr/sbin/syslog-ng-ctl stats | grep d_discord`

```
dst.http;d_slack#0;http,https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX;a;eps_last_1h;5
dst.http;d_slack#0;http,https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX;a;eps_since_start;0
dst.http;d_slack#0;http,https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX;a;batch_size_max;536
dst.http;d_slack#0;http,https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX;a;batch_size_avg;532
dst.http;d_slack#0;http,https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX;a;eps_last_24h;8
dst.http;d_slack#0;http,https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX;a;dropped;1
dst.http;d_slack#0;http,https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX;a;processed;35428
dst.http;d_slack#0;http,https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX;a;queued;0
dst.http;d_slack#0;http,https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX;a;memory_usage;0
dst.http;d_slack#0;http,https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX;a;written;35428
dst.http;d_slack#0;http,https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX;a;msg_size_max;518
dst.http;d_slack#0;http,https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX;a;msg_size_avg;514
```
### What does all this mean?

The common ones:

|Metric|Description|Restrictions|
|--|--|--|
|processed|The number of messages that successfully reached their destination driver.||
|queued|The number of messages passed to the message queue of the destination driver, waiting to be sent to the destination.||
|dropped|The number of dropped messages — syslog-ng could not send the messages to the destination and the output buffer got full, so messages were dropped by the destination driver, or syslog-ng dropped the message for some other reason (for example, a parsing error).||
|written|The net number of messages successfully delivered to the destination calculated from the above counters: written = processed - queued - dropped.||

