Unicorn
=======

**A Rack HTTP server for fast clients and Unix**

Unicorn is an HTTP server for Rack applications designed to only serve
fast clients on low-latency, high-bandwidth connections and take
advantage of features in Unix/Unix-like kernels. Slow clients should
only be served by placing a reverse proxy capable of fully buffering
both the the request and response in between Unicorn and slow clients.

## Features

* Designed for Rack, Unix, fast clients, and ease-of-debugging. We
  cut out everything that is better supported by the operating system,
  [nginx](http://nginx.net/) or [Rack](http://rack.rubyforge.org/).
* Compatible with both Ruby 1.8 and 1.9. Rubinius support is in-progress.
* Process management: Unicorn will reap and restart workers that
  die from broken apps. There is no need to manage multiple processes
  or ports yourself. Unicorn can spawn and manage any number of
  worker processes you choose to scale to your backend.
* Load balancing is done entirely by the operating system kernel.
  Requests never pile up behind a busy worker process.
* Does not care if your application is thread-safe or not, workers
  all run within their own isolated address space and only serve one
  client at a time for maximum robustness.
* Supports all Rack applications, along with pre-Rack versions of
  Ruby on Rails via a Rack wrapper.
* Built-in reopening of all log files in your application via
  ```USR1``` signal. This allows ```logrotate``` to rotate files atomically and
  quickly via rename instead of the racy and slow copytruncate method.
  Unicorn also takes steps to ensure multi-line log entries from one
  request all stay within the same file.
* nginx-style binary upgrades without losing connections.
  You can upgrade Unicorn, your entire application, libraries
  and even your Ruby interpreter without dropping clients.
* ```before_fork``` and ```after_fork``` hooks in case your application
  has special needs when dealing with forked processes. These
  should not be needed when the ```preload_app``` directive is
  false (the default).
* Can be used with copy-on-write-friendly memory management
  to save memory (by setting ```preload_app``` to true).
* Able to listen on multiple interfaces including UNIX sockets,
  each worker process can also bind to a private port via the
  ```after_fork``` hook for easy debugging.
* Simple and easy Ruby DSL for configuration.
* Decodes chunked transfers on-the-fly, thus allowing upload progress
  notification to be implemented as well as being able to tunnel
  arbitrary stream-based protocols over HTTP.

## Installation

The library consists of a C extension so you'll need a C compiler
and Ruby development libraries/headers.

### Via RubyGems.org
```
$ gem install unicorn
```

### Via the Mongrel Project Page
1. Download the tarball from RubyForge [here](http://rubyforge.org/frs/?group_id=1306)
1. Unpack it
1. Run ```setup.rb```

## Usage

### Running unicorn

#### For non-Rails Rack applications

In ```APP_ROOT```, run:

```
$ unicorn
```

#### For Rails 1.2+

In ```RAILS_ROOT```, run:

```
$ unicorn_rails
```

Unicorn will bind to all interfaces on TCP port 8080 by default.
You may use the ```--listen/-l``` switch to bind to a different
address:port or a UNIX socket.

## Configuration

Unicorn will look for the ```config.ru``` file used by rackup in ```APP_ROOT```.

For deployments, it can use a config file for Unicorn-specific options
specified by the ```--config-file/-c``` command-line switch. See
```Unicorn::Configurator``` for the syntax of the Unicorn-specific options.
The default settings are designed for maximum out-of-the-box
compatibility with existing applications.

Most command-line options for other Rack applications (above) are also
supported.

Run `unicorn -h` or `unicorn_rails -h` to see command-line
options.

## Contributing

All feedback (bug reports, user/development dicussion, patches, pull
requests) go to the mailing list/newsgroup. See [the ISSUES document](ISSUES) for
information on the [mailing list](mailto:mongrel-unicorn@rubyforge.org).

### Snapshots & Source (may be unstable)
* via cgit : bogomips.org *([Git](git://bogomips.org/unicorn.git)/[HTTP](http://bogomips.org/unicorn.git))*
* via gitweb : repo.or.cz *([Git](git://repo.or.cz/unicorn.git)/[HTTP](http://repo.or.cz/w/unicorn.git))*

For the latest on Unicorn releases, you may also finger us at
unicorn@bogomips.org or check our [NEWS page](http://unicorn.bogomips.org/NEWS.html) (and subscribe to [our Atom feed](http://unicorn.bogomips.org/NEWS.atom.xml)).


See [the HACKING guide](HACKING) on how to contribute and build pre-release gems
from git.

## License

Copyright 2009 by all contributors (see logs in git). It is based on Mongrel 1.1.5 (Copyright 2007 Zed A. Shaw & contributors).

Unicorn is licensed under *your choice* of the GPLv2 or later (GPLv3+ preferred), or Ruby (1.8)-specific terms. See [the included LICENSE file](LICENSE) for details.

**Unicorn is 100% Free Software**

### Disclaimer

There is NO WARRANTY whatsoever if anything goes wrong, but
[let us know](ISSUES) and we'll try our best to fix it.

Unicorn is designed to only serve fast clients either on the local host
or a fast LAN. See the [PHILOSOPHY](PHILOSOPHY) and [DESIGN](DESIGN) documents for more details
regarding this.

