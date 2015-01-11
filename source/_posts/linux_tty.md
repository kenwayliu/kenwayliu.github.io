title: Linux tty secure
date: 2014-02-25 19:44:33
categories: Linux Secure
comments: true
tags:
        - 系统安全
---

#### Linux tty secure


Almost everyone (not just system administrators) are forgetful and used to leave the Linux/Unix login session open (without logging out).

 

So, how to make sure all the Linux servers will automatically logout users after idle for certain minutes?

Don’t worry. There is no tedious configuration or additional package to be installed to make this happen.

 

In fact, the simplest way is to configure the TMOUT environment variable!

 
<!--more-->
For example, this export command

 

 

export TMOUT=60

will immediately get the Linux OS to automatically logout an user after his/her login session being idle for 60 seconds or 1 minute!

 

 

The TMOUT environment variable is applied to a command line console login session only.

 

For X-window or GUI login, you can easily turn on any pretty auto-lock screen-saver, that works very much like those in Windows.

 

For testing purpose, you can set a lower limit. While login to Linux, su to another user ID and execute

 

 

export TMOUT=10

After being idle for 10 seconds, you’ll likely see this warning message appears and the su login session will be terminated or log out immediately.

 

timed out waiting for input: auto-logout

 

In order the apply TMOUT to all Linux login accounts, you can put that export command to the login scripts or login profile (.bash_profile or .profile) in respective user home directory.

 

But, the easiest way is to write the export command in the system profile instead of respective user’s profile!

 

That’s to say, you can append the export TMOUT=60 command to /etc/profile (i.e. the system profile)!

 

Bear in mind that any login user can simply overwrite this TMOUT setting!

 

For example, he or she can easily disable or extend the time-out value before auto-logout feature triggered. To disable the Linux auto-logout user feature, just set the TMOUT to zero, i.e.

 

 

export TMOUT=0

In addition, the Linux TMOUT environment variable will not effective if the user has an active or open document. For example, if the VI editor is open, the Linux auto-logout feature in command console will not working!
