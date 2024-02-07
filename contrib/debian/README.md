
Debian
====================
This directory contains files used to package nicoind/nicoin-qt
for Debian-based Linux systems. If you compile nicoind/nicoin-qt yourself, there are some useful files here.

## nicoin: URI support ##


nicoin-qt.desktop  (Gnome / Open Desktop)
To install:

	sudo desktop-file-install nicoin-qt.desktop
	sudo update-desktop-database

If you build yourself, you will either need to modify the paths in
the .desktop file or copy or symlink your nicoin-qt binary to `/usr/bin`
and the `../../share/pixmaps/nicoin128.png` to `/usr/share/pixmaps`

nicoin-qt.protocol (KDE)

