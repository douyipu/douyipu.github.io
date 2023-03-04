---
layout: post
title: Login Debian 11.6.0 As Root Account Automatically
date: 2023-03-04 11:12:00
description: A brief note.
tags: Linux
giscus_comments: true
---

<br/>

- First open a terminal and type `su` then your root password that you created when installing your Debian 11.

- Install Emacs with `apt install emacs`.

- Stay in root terminal and type `emacs /etc/gdm3/daemon.conf`. This command opens the file `daemon.conf` in Emacs. 

- Before:

{% highlight conf %}
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
{% endhighlight %}

- After:

{% highlight conf %}
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
{% endhighlight %}

- Open file `/etc/pam.d/gdm-password`, `/etc/pam.d/gdm-autologin` and `/etc/pam.d/gdm-fingerprint`.

- Before:

{% highlight conf %}
#%PAM-1.0
auth    requisite       pam_nologin.so
auth	required	pam_succeed_if.so user != root quiet_success
{% endhighlight %}

- After:

{% highlight conf %}
#%PAM-1.0
auth    requisite       pam_nologin.so
#auth	required	pam_succeed_if.so user != root quiet_success
{% endhighlight %}

- Reboot.
