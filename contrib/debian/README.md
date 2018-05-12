
Debian
====================
This directory contains files used to package h2od/h2o-qt
for Debian-based Linux systems. If you compile h2od/h2o-qt yourself, there are some useful files here.

## h2o: URI support ##


h2o-qt.desktop  (Gnome / Open Desktop)
To install:

	sudo desktop-file-install h2o-qt.desktop
	sudo update-desktop-database

If you build yourself, you will either need to modify the paths in
the .desktop file or copy or symlink your h2o-qt binary to `/usr/bin`
and the `../../share/pixmaps/h2o128.png` to `/usr/share/pixmaps`

h2o-qt.protocol (KDE)

