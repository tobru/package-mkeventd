# Rake task for FPM-package mkeventd

Create a Debian/Ubuntu package of mkeventd using FPM and rake.

"The Check MK Event Console"
by Matthias Kettner

Source: http://mathias-kettner.de/checkmk_mkeventd.html

# Requirements

* Rake

# Includes

* upstart job
* defaults file

# Usage

* rake clean: remove all created dirs
* rake prepare[1.2.1]: downloads and prepares as-stats version 1.2.1
* rake build: creates debian package

