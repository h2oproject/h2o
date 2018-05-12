Sample init scripts and service configuration for h2od
==========================================================

Sample scripts and configuration files for systemd, Upstart and OpenRC
can be found in the contrib/init folder.

    contrib/init/h2od.service:    systemd service unit configuration
    contrib/init/h2od.openrc:     OpenRC compatible SysV style init script
    contrib/init/h2od.openrcconf: OpenRC conf.d file
    contrib/init/h2od.conf:       Upstart service configuration file
    contrib/init/h2od.init:       CentOS compatible SysV style init script

1. Service User
---------------------------------

All three Linux startup configurations assume the existence of a "h2ocore" user
and group.  They must be created before attempting to use these scripts.
The OS X configuration assumes h2od will be set up for the current user.

2. Configuration
---------------------------------

At a bare minimum, h2od requires that the rpcpassword setting be set
when running as a daemon.  If the configuration file does not exist or this
setting is not set, h2od will shutdown promptly after startup.

This password does not have to be remembered or typed as it is mostly used
as a fixed token that h2od and client programs read from the configuration
file, however it is recommended that a strong and secure password be used
as this password is security critical to securing the wallet should the
wallet be enabled.

If h2od is run with the "-server" flag (set by default), and no rpcpassword is set,
it will use a special cookie file for authentication. The cookie is generated with random
content when the daemon starts, and deleted when it exits. Read access to this file
controls who can access it through RPC.

By default the cookie is stored in the data directory, but it's location can be overridden
with the option '-rpccookiefile'.

This allows for running h2od without having to do any manual configuration.

`conf`, `pid`, and `wallet` accept relative paths which are interpreted as
relative to the data directory. `wallet` *only* supports relative paths.

For an example configuration file that describes the configuration settings,
see `contrib/debian/examples/h2o.conf`.

3. Paths
---------------------------------

3a) Linux

All three configurations assume several paths that might need to be adjusted.

Binary:              `/usr/bin/h2od`  
Configuration file:  `/etc/h2ocore/h2o.conf`  
Data directory:      `/var/lib/h2od`  
PID file:            `/var/run/h2od/h2od.pid` (OpenRC and Upstart) or `/var/lib/h2od/h2od.pid` (systemd)  
Lock file:           `/var/lock/subsys/h2od` (CentOS)  

The configuration file, PID directory (if applicable) and data directory
should all be owned by the h2ocore user and group.  It is advised for security
reasons to make the configuration file and data directory only readable by the
h2ocore user and group.  Access to h2o-cli and other h2od rpc clients
can then be controlled by group membership.

3b) Mac OS X

Binary:              `/usr/local/bin/h2od`  
Configuration file:  `~/Library/Application Support/H2OCore/h2o.conf`  
Data directory:      `~/Library/Application Support/H2OCore`
Lock file:           `~/Library/Application Support/H2OCore/.lock`

4. Installing Service Configuration
-----------------------------------

4a) systemd

Installing this .service file consists of just copying it to
/usr/lib/systemd/system directory, followed by the command
`systemctl daemon-reload` in order to update running systemd configuration.

To test, run `systemctl start h2od` and to enable for system startup run
`systemctl enable h2od`

4b) OpenRC

Rename h2od.openrc to h2od and drop it in /etc/init.d.  Double
check ownership and permissions and make it executable.  Test it with
`/etc/init.d/h2od start` and configure it to run on startup with
`rc-update add h2od`

4c) Upstart (for Debian/Ubuntu based distributions)

Drop h2od.conf in /etc/init.  Test by running `service h2od start`
it will automatically start on reboot.

NOTE: This script is incompatible with CentOS 5 and Amazon Linux 2014 as they
use old versions of Upstart and do not supply the start-stop-daemon utility.

4d) CentOS

Copy h2od.init to /etc/init.d/h2od. Test by running `service h2od start`.

Using this script, you can adjust the path and flags to the h2od program by
setting the ULTD and FLAGS environment variables in the file
/etc/sysconfig/h2od. You can also use the DAEMONOPTS environment variable here.

4e) Mac OS X

Copy org.h2o.h2od.plist into ~/Library/LaunchAgents. Load the launch agent by
running `launchctl load ~/Library/LaunchAgents/org.h2o.h2od.plist`.

This Launch Agent will cause h2od to start whenever the user logs in.

NOTE: This approach is intended for those wanting to run h2od as the current user.
You will need to modify org.h2o.h2od.plist if you intend to use it as a
Launch Daemon with a dedicated h2ocore user.

5. Auto-respawn
-----------------------------------

Auto respawning is currently only configured for Upstart and systemd.
Reasonable defaults have been chosen but YMMV.
