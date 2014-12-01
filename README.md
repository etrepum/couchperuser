couchperuser
============

couchperuser is a CouchDB daemon that ensures that a private per-user
database exists for each document in _users. These databases are
writable only by the corresponding user.

Currently this is very much purpose-built for CodeCosmos. Databases are
in the form:

  userdb-{hex encoded username}

For example, the user `bob` will have a database named `userdb-626f62`.

The reason for hex encoding is that CouchDB usernames have no restrictions,
but CouchDB databases have restrictions. Hex encoding the UTF-8
representation of the username is a transformation that's easy to
correctly implement in just about any language, especially JavaScript
and Erlang. Other encodings would be possible, but would require
additional client and server-side code to support that encoding. This
is the simplest scheme that is obviously correct.

Installation
----

Install any dependencies. `couchperuser` requires `rebar`, e.g.

    $ brew install rebar

Ensure the `plugins` directory exists, e.g.

    $ mkdir /usr/local/Cellar/couchdb/1.6.0_1/lib/couchdb/plugins

Clone (download) the repo:

    $ git clone https://github.com/etrepum/couchperuser.git

Move the plugin files:

    $ mv couchperuser /usr/local/Cellar/couchdb/1.6.0_1/lib/couchdb/plugins

Build the plugin files:

    $ cd /usr/local/Cellar/couchdb/1.6.0_1/lib/couchdb/plugins/couchperuser
    $ make

Restart couchdb

Digital Ocean "Ubuntu 14.04" Installation Instructions
Start by updating Ubuntu’s package manager:

    $ apt-get update

Next, install the tools we’ll need to compile Couch:

    $ apt-get install -y build-essential

Now install erlang and a few related dependencies:

    $ apt-get install -y erlang-base erlang-dev erlang-nox erlang-eunit

Finally, install a few libraries that CouchDB needs:

    apt-get install -y libmozjs185-dev libicu-dev libcurl4-gnutls-dev libtool

With all our dependencies met, let's download a copy of the source and have it ready.

Navigate to the directory our source will live:

    cd /usr/local/src

Grab the source:

    $ curl -O http://apache.mirrors.tds.net/couchdb/source/1.3.1/apache-couchdb-1.3.1.tar.gz


Unarchive the files:

    $ tar xvzf apache-couchdb-1.3.1.tar.gz

Navigate into our new directory:

    $ cd apache-couchdb-1.3.1

All that's left is to compile and install to our new CouchDB server. Configure our source code, then build and install it:

    $ ./configure
    $ make && make install

On Ubuntu, CouchDB likes to run as the user couchdb, so let's create it.

    $ adduser --disabled-login --disabled-password --no-create-home couched

You'll see a few prompts for things like Real Name and Room Number. You can leave these blank and hit enter or insert values as you like.

Now we need to give our new user the right permissions to access CouchDB's files:

    $ chown -R couchdb:couchdb /usr/local/var/log/couchdb /usr/local/var/lib/couchdb /usr/local/var/run/couchdb

Install CouchDB as a service and allow it to start on boot:

    $ ln -s /usr/local/etc/init.d/couchdb  /etc/init.d
    $ update-rc.d couchdb defaults

Finally, start CouchDB and relax!

    $ service couchdb start

To verify that it's running, connect to it on port 5984:

    $ curl localhost:5984

You should see a response like:

    $ curl localhost:5984 {"couchdb":"Welcome","uuid":"d79a7c37116364fcc76bcb91901f48c6","version":"1.3.1","vendor":{"name":"The Apache Software Foundation","version":"1.3.1"}}
  
By default, CouchDB is only acccessible from the VPS itself. This may be what you want, but let's assume you don't. To allow access from the web, let's change the config file. First, make a backup for safe keeping:

    $ cp /usr/local/etc/couchdb/default.ini /usr/local/etc/couchdb/default.ini.bak
  
Next, let's open up the file in an editor:

    $ nano /usr/local/etc/couchdb/default.ini
  
Look for a setting called bind_address, and change it to 0.0.0.0 - this will make CouchDB bind to all available addresses (At this time, there is no way to specify).

     [httpd] port = 5984 bind_address = 0.0.0.0

If you want CouchDB to run on a different port, you can change that setting now as well. Once you're done making changes, save the file and restart couch.

    $ service couchdb restart

CouchDB is now accessible from the web, including the built-in web interface, Futon. To access Futon, point a browser to http://your.drop.ip.here:5984/_utils and get ready to relax!

Note: if you want to access your CouchDB server from your local computer, but don't want to open it up to the world, use this ssh tunnel on your OSX or linux machine.

    $ ssh -L 5984:localhost:5984 your.drop.ip.here

You can now access your server in a browser at http://localhost:5984/_utils.


----
Install couchdb.

https://www.digitalocean.com/community/tutorials/how-to-install-couchdb-from-source-on-an-ubuntu-13-04-x64-vps

Install any dependencies. `couchperuser` requires `rebar`, e.g.

    $ apt-get install rebar

Ensure the `plugins` directory exists, e.g.

    $ mkdir /usr/local/lib/couchdb/plugins

Clone (download) the repo:

    $ git clone https://github.com/jspenc72/couchperuser.git

Move the plugin files:

    $ mv couchperuser /usr/local/lib/couchdb/plugins

Build the plugin files:

    $ cd /usr/local/lib/couchdb/plugins/couchperuser
    $ make

Restart couchdb

    $ service couchdb restart
