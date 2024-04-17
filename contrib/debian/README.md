
Debian
====================
This directory contains files used to package peppapowd/peppapow-qt
for Debian-based Linux systems. If you compile peppapowd/peppapow-qt yourself, there are some useful files here.

## peppapow: URI support ##


peppapow-qt.desktop  (Gnome / Open Desktop)
To install:

	sudo desktop-file-install peppapow-qt.desktop
	sudo update-desktop-database

If you build yourself, you will either need to modify the paths in
the .desktop file or copy or symlink your peppapow-qt binary to `/usr/bin`
and the `../../share/pixmaps/peppapow128.png` to `/usr/share/pixmaps`

peppapow-qt.protocol (KDE)

