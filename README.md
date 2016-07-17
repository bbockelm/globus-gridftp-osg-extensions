# OSG Extensions for the Globus GridFTP server
OSG-specific extensions for the Globus GridFTP server

This Globus DSI module layers on top of the existing `file` DSI in order to add OSG-specific commands.

The initial use case is adding a space usage command so remote users can query for their usage information.

## Configuring the module

To configure the extension module, add the following line to a new file in `/etc/gridftp.d`:

```
load_dsi_module osg
```

This will cause the extension module to be used; this module layers on top of the `file` module.

## Site Usage
The site usage extension allows the GridFTP server to provide information about usage of space in the server.  The sysadmin must provide a script that, given a space name, returns the number of bytes used.

The semantics of this is left purposely undefined: the "space name" may refer to a directory (`/path/to/some/files`) or some other site construct ("all data written by user ABC").  Further, "usage" is similarly ill-defined when it comes to various storage backends such as tape systems.  It is expected the site works with its user community to define this.

To configure, in a configuration file in `/etc/gridftp.d`, add an environment variable defining the site usage script:
```
$OSG_SITE_USAGE_SCRIPT /usr/bin/my_site_usage.sh
```

The script `my_site_usage.sh` above should expect a single argument (the space name) and return a single integer (number of bytes used).

With this enabled, one can test the server's response using `uberftp`:
```
$ uberftp gridftp.example.com
220 gridftp.example.com GridFTP Server 10.4 (gcc64, 1463669416-85) [Globus Toolkit 6.0] ready.
230 User johndoe logged in.
UberFTP (2.8)> quote site usage /path
250 USAGE 80286
```

In the above session, `quote` indicates we are using a command that `uberftp` is not familiar with.

We can also test the command by hand; the following is equivalent to the above remote session:
```
$ /usr/bin/my_site_usage.sh /path
80286
```
