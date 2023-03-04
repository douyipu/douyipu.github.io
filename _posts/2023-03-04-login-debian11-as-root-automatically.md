---
layout: post
title: Login Debian 11.6.0 As Root Account Automatically
date: 2023-3-4 11:12:00-0400
description: 
tags: Linux
giscus_comments: true
---

<br/>

1. First open a terminal and type `su` then your root password that you created when installing your Debian 11.

2. Install Emacs with `apt install emacs`.

3. Stay in root terminal and type `emacs /etc/gdm3/daemon.conf`. This command opens the file `daemon.conf` in Emacs. 

Before:

```
[daemon]
# Uncomment the line below to force the login screen to use Xorg
#WaylandEnable=false

# Enabling automatic login
# AutomaticLoginEnable=true
# AutomaticLogin=user1

# Enabling timed login
#  TimedLoginEnable = true
#  TimedLogin = user1
#  TimedLoginDelay = 10

AutomaticLoginEnable=False
AutomaticLogin=douyipu

[security]

```
After:

```
[daemon]
# Uncomment the line below to force the login screen to use Xorg
#WaylandEnable=false

# Enabling automatic login
AutomaticLoginEnable=true
AutomaticLogin=root

# Enabling timed login
#  TimedLoginEnable = true
#  TimedLogin = user1
#  TimedLoginDelay = 10

#  AutomaticLoginEnable=False
#  AutomaticLogin=douyipu

[security]
AllowRoot=true
```

4. Open file `/etc/pam.d/gdm-password`, `/etc/pam.d/gdm-autologin` and
`/etc/pam.d/gdm-fingerprint`.

Before:

```
#%PAM-1.0
auth    requisite       pam_nologin.so
auth	required	pam_succeed_if.so user != root quiet_success
```

After:

```
#%PAM-1.0
auth    requisite       pam_nologin.so
#auth	required	pam_succeed_if.so user != root quiet_success
```

5. Reboot.
