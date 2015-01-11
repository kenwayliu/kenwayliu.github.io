title: Mac add route
date: 2014-03-11 10:44:33
categories: Linux Tools
comments: true
tags:
        - Mac添加路由

---

And because i feel lucky today, (0n a Friday the 13th, that looks kind of odd), here’s another how-to. This is how I added a static route on some Xserves.

```ruby
Simple solution: route -n add 10.0.0.0/8 10.16.3.254
```
Permanent solution: Add a startup item (to avoid doing the above command every reboot)

cd /System/Library/StartupItems
mkdir AddRoutes
cd AddRoutes

Create a file called AddRoutes (note: same as the folder name)
vi AddRoutes

<!--more-->
```ruby
#!/bin/sh

# Set static routing tables

. /etc/rc.common

StartService ()
{
if [ "${ADDROUTES:=-NO-}" = "-YES-" ]; then
ConsoleMessage “Adding Static Routing Table”
sudo route -nv add 10.0.0.0/8 10.16.3.254
fi
}

StopService ()
{
return 0
}

StopService ()
{
return 0
}

RestartService ()
{
return 0
}

```
RunService “$1″


Then create a file StartupParameters.plist

```ruby
{
Description = “Add static routing tables”;
Provides = (”AddRoutes”);
Requires = (”Network”);
OrderPreference = “None”;
}
```

Then change permissions:

```ruby
chmod 755 AddRoutes StartupParameters.plist
```

Reboot your computer. Verify with netstat -nr
