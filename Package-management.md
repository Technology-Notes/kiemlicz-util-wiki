#Packages
Typically packages are nothing more that some kind of archive.
##DEB
_AR_ archive with following top-level files included:
 * debian-binary
 * control.tar.gz
 * data.tar.gz (since Debian 8: data.tar.xz)

##RPM
todo

#APT/Aptitude
Highest priority number means highest priority of package.
 
Show available/installed package version:  
`aptitude versions the_package` or `dpkg-query -l | grep the_package`

Upgrade/downgrade only one specific package:  
`apt-get install --only-upgrade the_package`

Remove dangling packages:  
`apt-get autoremove --purge`

Remove old cached packages:  
`aptitude autoclean`

Remove cached packages:  
`aptitude clean` 
##Preferences
Mechanism that determines which version of the package will be installed (consult: `man apt_preferences`).

Used to pick **package version** when _apt list files_ (`/etc/apt/sources.list` or `/etc/apt/sources.list.d/*.list`) contains references to more than one distribution eg. contains both stable and testing repositories. 
 1. Normally without using apt_preferences the apt will pick the package coming from the first entry in `sources.list`.
 2. If no preferences exists or contains no entry for given package then the package priority is the priority of distro. If preference exists for given package then its value is used.
 3. To “force” usage of some other distro, use `-t` flag (specify target release), eg. 
`apt-get install -t experimental some-package`. If `-t` is given then check man `apt_preferences` for algorithm that assign priority for package. 
It roughly specifies:
    - If target release contains in release files _“NotAutomatic: yes”_ but not _“ButAutomaticUpgrades: yes”_. Then the priority is 1
    - If package is installed or if target release’s release file contains _“NotAutomatic: yes”_ and _“ButAutomaticUpgrades: yes”_. Then the priority is 100
    - If the package is not installed and do not belong to the target release. Then the priority is 500
    - If the package is not installed and belong to the target release. Then the priority is 990.
 4. Installs version with highest priority (highest number).

Please mind that if you have configured package pinning and e.g. your stable is configured to have priority of 995 (or anything greater than 990) then `-t` will have **no effect**.

You can always verify with `apt-cache policy -t target-release package` which exact version of package is going to be installed

###Preferences files
Located in `/etc/apt/preferences.d`  
Parsed in alphanumeric ascending order  
Need to obey convention: 
 1. No filename extension or `.pref`
 2. Filename chars allowed: alphanumeric, hyphen, underscore and period

The file itself contains records separated by blank lines.  
Preference refers to the mentioned package and the mentioned package only (doesn't affect its dependencies). This is one of the reasons why this mechanism is kind of "discouraged".

#References
1. https://wiki.debian.org/AptPreferences