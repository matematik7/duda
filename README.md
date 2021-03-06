# Duda I/O: Web Services Framework
Duda is an event-driven web services framework which exposes a
friendly C API.

It mainly purpose is to interface over Monkey HTTP server to
implement fast services through asynchronous callbacks, where is
possible to map specific URL routes against local functions.

Duda do not requires extra dependencies besides libc, but depending
of the service needs, it can load on runtime packages to import
specific objects with third party features. Some packages available
are:

 * WebSocket
 * JSON
 * SQLite
 * SHA1
 * SHA256
 * Base64
 * Redis
 * In-Memory Key/Value store
 * much more!

Being implemented on top of Monkey stack, its fully compatible for
projects targetting Embedded Linux, as well it's natively made for
ARM, x86 and x86_64.

For mode technical details or API documentation please refer to the
official project site:

   http://duda.io

## Quick Start with Duda Client Manager (aka DudaC)

DudaC is a separate program that takes care of setup a development
environment for Duda web services. For more details about it please
refer to the following guide:

   http://duda.io/documentation/dudac

In order to get some services examples refer to this repository:

   https://github.com/monkey/duda-examples

Then you can play with DudaC and duda-examples/NNN.

## Duda Internals and Workflow

Before to describe how Duda works we need to describe the internals
of Monkey HTTP server.

Monkey is an event-driven web server for Linux. It's completely optimized
to obtain high performance at a low cost of system resources. It
depends of specific Linux system calls and it's architecture allow the
integration of third party components through the plugin API interface.

As described earlier, Duda is written on top of Monkey, this means that
Duda is a Monkey plugin who wraps the Monkey plugin API and expose a
friendly C API, it also take care of to hide the internal complexity of
the web server in terms of threading and asynchronous events.

Supposing that Monkey is running and have Duda plugin loaded on it, every
time that a HTTP request arrives, Monkey will trigger the Duda callbacks
to determinate if exist a handler for it. Duda on it's own side, will
verify if a web service have been registered, and if the service registered
have requested some mapped URL that matches, Duda will give control to the
web service, and this web service will use Duda API to send responses based
on what it was made for.


                         +--------------------+
                         | Monkey HTTP Server |
                         +--------------------+
                                   |
                         +-------------------+
                         | Monkey Plugin API |
                         +-------------------+
                                   |
                             +----------+
                             | Duda I/O |
                             +----------+
                                   |
              +--------------------+-------------------+
              |                    |                   |
      +---------------+    +--------------+   +--------+--------+
      | WebService #1 |    | WebSerive #2 |   |  WebService #N  |
      +---------------+    +--------------+   +-----------------+


Many web services can be registered with Duda, internally talking about the
web service, this web services can be mapped to different virtual hosts, this
provides a huge flexibility in terms of what is required to do.


## Manually setup of a web service

* Enable the duda plugin in conf/plugins.load.

*  Edit conf/plugins/duda/duda.conf configuration file and set the key
ServicesRoot with the absolute path where the services files are
located, e.g:

  [DUDA]
      ServicesRoot /home/foo/monkey/services/
      PackagesRoot /home/foo/monkey/plugins/duda/packages/

* Edit the virtual host configuration file where the service will work,
as an example edit conf/sites/default and add the following
entry:

  [WEB_SERVICE]
      Name          hello
      Enabled       on
      Root          off                                      <- optional
      DocumentRoot  /home/foo/monkey/services/hello/html/    <- optional
      ConfDir       /home/foo/monkey/services/hello/conf/    <- optional
      LogDir        /home/foo/monkey/services/hello/logs/    <- optional
      DataDir       /home/foo/monkey/services/hello/data/    <- optional

we have instructed to load service "hello", so Duda will try to locate
the service file 'hello.duda' under ServicesRoot directory and load it.

## Links

Project site: http://duda.io
Bug Tracker : http://bugs.duda.io
Mailing list: http://lists.monkey-project.com/listinfo/duda
IRC         : irc.freenode.net #monkey

## Author

Eduardo Silva P <edsiper@gmail.com>
http://edsiper.linuxchile.cl
