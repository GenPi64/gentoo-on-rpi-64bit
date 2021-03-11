# Migratation from Sakaki's image

This assumes that you're using the latest available Sakaki's image.

## Binhost

Please make sure that your /etc/portage/make.conf does not have a binhost set at all.

Create a new file /etc/portage/binrepos.conf:
```
[genpi64-main-binhost]
priority = 99
sync-uri = https://packages.genpi64.com/

[genpi64-europe-binhost]
priority = 98
sync-uri = https://fi.packages.genpi64.com/
```

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



After doing all of that, update the system fully: `emerge -avg --update --newuse --deep @world`.

