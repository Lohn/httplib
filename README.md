Bash + Nginx CGI Example
================

> Note: A pervious version of this README mentioned [Python's CGIHTTPServer ver.2](http://docs.python.org/2/library/cgihttpserver.html), which is out of date. CGIHTTPServer is actually not needed for a this example at all.

As [Nginx](http://nginx.org/) only supports FastCGI out of the box,
we also need to install [fcgiwrap](https://github.com/gnosek/fcgiwrap).

On Ubuntu: `apt-get install nginx fcgiwrap`  
On Arch: `pacman -S nginx fcgiwrap`

Example Nginx config (Ubuntu: `/etc/nginx/sites-enabled/default`):
```
server {
    listen   80;
    server_name  localhost;
    access_log  /var/log/nginx/access.log;

    location / {
        root /srv/static;
        autoindex on;
        index index.html index.htm;
    }

    location ~ ^/cgi-bin {
        root /srv/cgi-bin;
        rewrite ^/cgi-bin/(.*) /$1 break;

        include fastcgi_params;
        fastcgi_pass unix:/var/run/fcgiwrap.socket;
        fastcgi_param SCRIPT_FILENAME /srv/cgi-bin$fastcgi_script_name;
    }
}
```

If necessary, change the **root** and **fastcgi_param** lines to a directory containing CGI
scripts, e.g. the `cgi-bin/` dir in this repository.

Run `sudo chmod +x /srv/cgi-bin/*` so the scripts will execute.

If you are a control freak and run `fcgiwrap` manually,
be sure to change **fastcgi_pass** accordingly. The path listed in the example
is the default in Ubuntu when using the out-of-the-box fcgiwrap setup.
bashlib 0.2

Installation of bashlib is relatively straightforward:

  # gunzip -c bashlib-0.2.tar.gz | tar xf -
  # cd bashlib-0.2
  # sh install.sh
  # cp bashlib /path/to/bashlib

Copying the bashlib script (all the other files in the directory are
extraneous once the install.sh script is run and bashlib is copied)
is all that is required to install. Maybe version 0.3 will have
a Makefile.

Using bashlib in your CGI scripts is as simple as beginning your
scripts with:

#!/bin/bash
. /path/to/bashlib

# Other stuff

Sourcing bashlib should be the first thing you do in your script;
it reads the environment and pulls out the parameters and cookies.
To get at a named parameter, call:

p=`param p`

In this case, the value of the parameter "p" will end up in p, and
can be accessed normally, through $p.

Cookies work similarly:

session=`cookie session`

will retrieve the cookie named session.

HELPING OUT

Any help is, of course, welcomed. Email me, and we can discuss what
needs to be done. Patches and feature requests are accepted.

TODO

 * Better documentation, probably a man page
 * Autoconf-based install script
 * More functions
   * Functions to generate HTML
   * Functions to set cookies
   * Create URL's
 * Better documentation
 * Add support for multivalued parameters, either via bash2's arrays
   or some other hack
 * sed-based templating system (I've started this one, and it's scary!)

AUTHOR

darren chamberlain <dlc@users.sourceforge.net>
