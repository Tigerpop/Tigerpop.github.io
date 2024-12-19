---
layout: posts
title: 让某些sudo命令不用输入密码
date: 2024-9-29 14:40:24
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "Linux"





---



​		注意把# User privilege specification 放到最下面，因为 用户、组等等 逻辑会出现覆盖，放最下面就能覆盖掉之前组的逻辑。

​		以下是 让 sudo reboot 不用输入密码的案例。

`sudo vim /etc/sudoers`

```sh

#
# This file MUST be edited with the 'visudo' command as root.
#
# Please consider adding local content in /etc/sudoers.d/ instead of
# directly modifying this file.
#
# See the man page for details on how to write a sudoers file.
#
Defaults        env_reset
Defaults        mail_badpass
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"

# Host alias specification

# User alias specification

# Cmnd alias specification

# User privilege specification
#root   ALL=(ALL:ALL) ALL

# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# See sudoers(5) for more information on "#include" directives:

# User privilege specification
root   ALL=(ALL:ALL) ALL
jlsf ALL=(ALL) NOPASSWD: /sbin/reboot
jlsf ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart *
jlsf ALL=(ALL) NOPASSWD: /bin/ls


#includedir /etc/sudoers.d

```



