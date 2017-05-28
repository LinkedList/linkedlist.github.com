---
layout: post
title: "Ubuntu 14.04 and mod_lua"
description: "Setting up mod_lua in Ubuntu 14.04"
category: 
tags: [ubuntu, apache, mod_lua, lua]
---
I found out about a problem with apache2 and mod_lua today.
When trying to enable mod_lua in apache

    a2enmod lua

and restarting apache service

    service apache2 restart

it returned a error

    apache2: Syntax error on line 140 of /etc/apache2/apache2.conf: Syntax error on line 1 of /etc/apache2/mods-enabled/lua.load: Cannot load /usr/lib/apache2/modules/mod_lua.so into server: /usr/lib/apache2/modules/mod_lua.so: cannot open shared object file: No such file or directory

Basically apache can't find mod_lua in modules folder.
Thanks to a kind help from [Humbedooh](https://github.com/Humbedooh) I was able to successfully compile mod_lua myself.
Here are the steps needed to make mod_lua work.

1. Clone svn repository of lua module

        svn co https://svn.apache.org/repos/asf/httpd/httpd/branches/2.4.x/modules/lua/

2. Find <code>lua.h</code> on your installation
    
        find / -name lua.h

   For me it was in <code>/usr/include/lua5.2/</code>

3. cd into the cloned repo

        cd lua

4. and run this command to compile mod_lua yourself (remember to put your <code>lua.h</code> location there)

        apxs -I/usr/include/lua5.2 -cia mod_lua.c lua_*.c -lm -llua5.2

5. and if everything went well the last thing is to restart apache

        service apache2 restart

   and lua should be enabled

Refer to [modlua.org](http://modlua.org) for more info about using mod_lua.
