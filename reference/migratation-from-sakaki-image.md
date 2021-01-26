# Migratation from Sakaki's image

This assumes that you're using the latest available Sakaki's image.


## Overlays

You need to start by updating the genpi-overlay and sakaki-tools overlay locations to the correct ones.

File /etc/portage/repos.conf/genpi64.conf:
```
[genpi64]

location = /var/db/repos/genpi64
sync-type = git
sync-uri = https://github.com/GenPi64/genpi64-overlay.git
priority = 100
auto-sync = yes
```

File /etc/portage/repos.conf/sakaki-tools.conf:
```
[genpi-tools]

location = /var/db/repos/sakaki-tools
sync-type = git
sync-uri = https://github.com/GenPi64/genpi-tools.git
priority = 50
auto-sync = yes
```

You will also need to use the main gentoo tree.

File /etc/portage/repos.conf/gentoo.conf:
```
[DEFAULT]
main-repo = gentoo

[gentoo]
location = /var/db/repos/gentoo
sync-type = rsync
sync-uri = rsync://rsync.gentoo.org/gentoo-portage
auto-sync = yes
```

Please run `rm -r /var/db/repos/*` and then `emerge --sync` before continuing when you have edited those files.

## Profiles and meta package

Select the new profile: `eselect profile set genpi64:default/linux/arm64/17.0/genpi64` or `eselect profile set genpi64:default/linux/arm64/17.0/genpi64/desktop`.  
Unmerge the obsolate meta packages: `emerge --unmerge dev-embedded/rpi-64bit-meta dev-embedded/rpi3-64bit-meta`.  

Add the set files:
```
mkdir -p /etc/portage/sets
cd /etc/portage/sets
wget https://raw.githubusercontent.com/GenPi64/Build.Dist/main/config/sets/standard
wget https://raw.githubusercontent.com/GenPi64/Build.Dist/main/config/sets/pi4
wget https://raw.githubusercontent.com/GenPi64/Build.Dist/main/config/sets/pi4desktop
```

Add appropriate sets to your world_sets file:
```
echo "@standard" >> /var/lib/portage/world_sets"
echo "@pi4" >> /var/lib/portage/world_sets"
echo "@pi4desktop" >> /var/lib/portage/world_sets"
```
NB: Omit desktop one, if you're not using that profile.


After doing all of that, update the system fully: `emerge -avg --update --newuse --deep @world`.

