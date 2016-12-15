---
title: rvm compilation issue - ruby 2.3.3, openssl
date: 2016-12-15 02:25 UTC
tags:
layout: page
---

While trying to upgrade to the latest available ruby version I ran into a problem. It seemed to be a problem with rvm and after some googling I came across a decent solution.

```
$ rvm install 2.3.3
Searching for binary rubies, this might take some time.
No binary rubies available for: debian/stretch_sid/x86_64/ruby-2.3.3.
Continuing with compilation. Please read 'rvm help mount' to get more information on binary rubies.
Checking requirements for debian.
Installing requirements for debian.
Updating system - please wait
Installing required packages: libreadline6-dev - please wait
Requirements installation successful.
Installing Ruby from source to: /home/xtc/.rvm/rubies/ruby-2.3.3, this may take a while depending on your cpu(s)...
ruby-2.3.3 - #downloading ruby-2.3.3, this may take a while depending on your connection...
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 13.7M  100 13.7M    0     0  6666k      0  0:00:02  0:00:02 --:--:-- 6664k
ruby-2.3.3 - #extracting ruby-2.3.3 to /home/xtc/.rvm/src/ruby-2.3.3 - please wait
ruby-2.3.3 - #configuring - please wait
'config.log' -> '/home/xtc/.rvm/log/1481740747_ruby-2.3.3/config.log'
ruby-2.3.3 - #post-configuration - please wait
ruby-2.3.3 - #compiling - please wait
Error running '__rvm_make -j1',
showing last 15 lines of /home/xtc/.rvm/log/1481740747_ruby-2.3.3/make.log
 }
 ^
ossl_pkey_dsa.c: In function ‘ossl_dsa_is_public’:
ossl_pkey_dsa.c:274:1: warning: control reaches end of non-void function [-Wreturn-type]
 }
 ^
Makefile:301: recipe for target 'ossl_pkey_dsa.o' failed
make[2]: *** [ossl_pkey_dsa.o] Error 1
make[2]: Leaving directory '/home/xtc/.rvm/src/ruby-2.3.3/ext/openssl'
exts.mk:210: recipe for target 'ext/openssl/all' failed
make[1]: *** [ext/openssl/all] Error 2
make[1]: Leaving directory '/home/xtc/.rvm/src/ruby-2.3.3'
uncommon.mk:203: recipe for target 'build-ext' failed
make: *** [build-ext] Error 2
+__rvm_make:0> return 2
There has been an error while running make. Halting the installation.
```

By executing these two lines ruby 2.3.3 should compile without problems.

```
$ rvm pkg install openssl
$ rvm install 2.3.3 --with-openssl-dir=$HOME/.rvm/usr
```
