---
title: Dovecot/Postfix - Initial Server Greeting Failure
date: 2016-12-02 00:08 UTC
tags: dovecot postfix server
layout: page
---

I ran into a problem suddenly after upgrading my mail server. Thunderbird would timeout while logging in without any error. Sending email appeared to work, but I was no longer receiving mail or able to login. After debugging SSL and authentication I dug through the logs.

```
Nov 19 20:58:20 andromeda postfix/lmtp[6823]: B4F8B40416: to=<kirk@parodybit.net>, relay=andromeda.parodybit.net[private/dovecot-lmtp], delay=105492, delays=105492/0.02/0.01/0, dsn=4.4.2, status=deferred (lost connection with andromeda.parodybit.net[private/dovecot-lmtp] while receiving the initial server greeting)
```

Postfix says it lost connection with Dovecot "while receiving the initial server greeting". I wasn't entirely sure what that meant and spent some time googling to see if others had a common failure point. This didn't result in any helpful results.

I enabled verbose logging at every available point on Dovecot's end to see what the issue was. Eventually I caught this exception:

```
[Dovecot] Nov 19 23:12:08 imap-login: Fatal: Invalid ssl_protocols setting: Unknown protocol 'SSLv2'
```

After grepping through configuration I found my SSL configuration file:

```
ssl      = required
ssl_cert = </etc/ssl/parodybit.net.crt
ssl_key  = </etc/ssl/parodybit.net.key
ssl_protocols = !SSLv3 !SSLv2
ssl_cipher_list=ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA
ssl_prefer_server_ciphers = yes
ssl_dh_parameters_length = 2048
```

A few of these options were recommended on various sites to neuter issues like POODLE (heh) and overall ensure maximum security. The fourth line causes the issue, and simply removing this line solved my problem.

I haven't found a suitable replacement at the moment but I would hope any clients at this point in time do not try to negotiate using these bad protocol versions.
