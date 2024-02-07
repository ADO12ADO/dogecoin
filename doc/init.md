Sample init scripts and service configuration for nicoind
==========================================================

Sample scripts and configuration files for systemd, Upstart and OpenRC
can be found in the contrib/init folder.

    contrib/init/nicoind.service:    systemd service unit configuration
    contrib/init/nicoind.openrc:     OpenRC compatible SysV style init script
    contrib/init/nicoind.openrcconf: OpenRC conf.d file
    contrib/init/nicoind.conf:       Upstart service configuration file
    contrib/init/nicoind.init:       CentOS compatible SysV style init script

1. Service User
---------------------------------

All three Linux startup configurations assume the existence of a "nicoin" user
and group.  They must be created before attempting to use these scripts.
The OS X configuration assumes nicoind will be set up for the current user.

2. Configuration
---------------------------------

At a bare minimum, nicoind requires that the rpcpassword setting be set
when running as a daemon.  If the configuration file does not exist or this
setting is not set, nicoind will shutdown promptly after startup.

This password does not have to be remembered or typed as it is mostly used
as a fixed token that nicoind and client programs read from the configuration
file, however it is recommended that a strong and secure password be used
as this password is security critical to securing the wallet should the
wallet be enabled.

If nicoind is run with the "-server" flag (set by default), and no rpcpassword is set,
it will use a special cookie file for authentication. The cookie is generated with random
content when the daemon starts, and deleted when it exits. Read access to this file
controls who can access it through RPC.

By default the cookie is stored in the data directory, but it's location can be overridden
with the option '-rpccookiefile'.

This allows for running nicoind without having to do any manual configuration.

`conf`, `pid`, and `wallet` accept relative paths which are interpreted as
relative to the data directory. `wallet` *only* supports relative paths.

For an example configuration file that describes the configuration settings,
see `contrib/debian/examples/nicoin.conf`.

3. Paths
---------------------------------

3a) Linux

All three configurations assume several paths that might need to be adjusted.

Binary:              `/usr/bin/nicoind`  
Configuration file:  `/etc/nicoin/nicoin.conf`  
Data directory:      `/var/lib/nicoind`  
PID file:            `/var/run/nicoind/nicoind.pid` (OpenRC and Upstart) or `/var/lib/nicoind/nicoind.pid` (systemd)  
Lock file:           `/var/lock/subsys/nicoind` (CentOS)  

The configuration file, PID directory (if applicable) and data directory
should all be owned by the nicoin user and group.  It is advised for security
reasons to make the configuration file and data directory only readable by the
nicoin user and group.  Access to nicoin-cli and other nicoind rpc clients
can then be controlled by group membership.

3b) Mac OS X

Binary:              `/usr/local/bin/nicoind`  
Configuration file:  `~/Library/Application Support/Nicoin/nicoin.conf`  
Data directory:      `~/Library/Application Support/Nicoin`
Lock file:           `~/Library/Application Support/Nicoin/.lock`

4. Installing Service Configuration
-----------------------------------

4a) systemd

Installing this .service file consists of just copying it to
/usr/lib/systemd/system directory, followed by the command
`systemctl daemon-reload` in order to update running systemd configuration.

To test, run `systemctl start nicoind` and to enable for system startup run
`systemctl enable nicoind`

4b) OpenRC

Rename nicoind.openrc to nicoind and drop it in /etc/init.d.  Double
check ownership and permissions and make it executable.  Test it with
`/etc/init.d/nicoind start` and configure it to run on startup with
`rc-update add nicoind`

4c) Upstart (for Debian/Ubuntu based distributions)

Drop nicoind.conf in /etc/init.  Test by running `service nicoind start`
it will automatically start on reboot.

NOTE: This script is incompatible with CentOS 5 and Amazon Linux 2014 as they
use old versions of Upstart and do not supply the start-stop-daemon utility.

4d) CentOS

Copy nicoind.init to /etc/init.d/nicoind. Test by running `service nicoind start`.

Using this script, you can adjust the path and flags to the nicoind program by
setting the NICOIND and FLAGS environment variables in the file
/etc/sysconfig/nicoind. You can also use the DAEMONOPTS environment variable here.

4e) Mac OS X

Copy org.nicoin.nicoind.plist into ~/Library/LaunchAgents. Load the launch agent by
running `launchctl load ~/Library/LaunchAgents/org.nicoin.nicoind.plist`.

This Launch Agent will cause nicoind to start whenever the user logs in.

NOTE: This approach is intended for those wanting to run nicoind as the current user.
You will need to modify org.nicoin.nicoind.plist if you intend to use it as a
Launch Daemon with a dedicated nicoin user.

5. Auto-respawn
-----------------------------------

Auto respawning is currently only configured for Upstart and systemd.
Reasonable defaults have been chosen but YMMV.
