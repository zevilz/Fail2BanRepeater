# Fail2BanRepeater

Fail2Ban action for ban IPs for a long time. Banned ips register in /etc/fail2ban/ip.blocklist.<name> and re-populate iptables after fail2ban/system restart. I recommend to use this action in new filters as addition to the existing filters against brute force.

Requirements
------------

- fail2ban v. 0.8.x/0.9.x

Configuring
-----------
1. Put **iptables-repeater.conf** to **/etc/fail2ban/action.d/** directory.

2. Add new filter in **/etc/fail2ban/jail.conf** or **/etc/fail2ban/jail.local** like below

```bash
[<filter_name>]
enabled  = true
port     = <port>
logpath  = <log_path>
action   = iptables-repeater[name=<name_in_chain>, port="<port>", protocol="tcp", chain="%(chain)s", actname=%(banaction)s-tcp]
           %(mta)s-whois[name=%(__name__)s, dest="%(destemail)s"]
filter   = <filter_name>
maxretry = <maxretry>
findtime = <findtime>
bantime  = <bantime>
```

Example:

```bash
[sshd-repeater]
enabled  = true
port     = 1234
logpath  = %(sshd_log)s
action   = iptables-repeater[name=sshd, port="1234", protocol="tcp", chain="%(chain)s", actname=%(banaction)s-tcp]
           %(mta)s-whois[name=%(__name__)s, dest="%(destemail)s"]
filter   = sshd
maxretry = 13
findtime = 31536000
bantime  = 31536000
```

3. Restart Fail2ban.

Checking
--------

For check filter activity (new filter must be in jails list)

```bash
fail2ban-client status
```

For view info about filter

```bash
fail2ban-client status <filter_name>
```

Example:

```bash
fail2ban-client status sshd-repeater
```

Unban IP
--------

For unban certain IP

```bash
fail2ban-client set <filter_name> unbanip <IP>
```

Example:

```bash
fail2ban-client set sshd-repeater unbanip 123.123.123.123
```

Unbanned IP automatically deleted from ip.blocklist.<name>.

Changelog
---------

- 23.07.2017 - 1.1.0 - Bug fixes. Added multiport support and action for legacy versions (0.8.x).
- 06.07.2017 - 1.0.0 - Released
