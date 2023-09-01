# archzfs.iso
How to build an arch installer with ZFS kernel modules.

## archiso
Install archiso using pacman.
```
pacman -S archiso
```


## Copy relevant files a working directory
Make a new directory called archlive using mkdir.
```
mkdir archlive
```
Copy the releng directory from /usr/share/archiso/configs to the archlive directory using cp.
```
cp -pr /usr/share/archiso/configs/releng archlive
```
Change directory into archlive/releng using cd
```
cd archlive/releng
```


## Setting up pacman with archzfs.com
Download the archzfs.gpg key from http://archzfs.com/archzfs.gpg using curl.
```
curl -O http://archzfs.com/archzfs.gpg
```
Add the key to pacman and locally sign with different key using pacman-key. (as per 02/09/2023)
For more information see the archzfs wiki.
```
pacman-key -a archzfs.gpg
pacman-key --lsign-key DDF7DB817396A49B2A2723F7403BD972F75D9D76
```
Remove the archzfs.gpg key using rm.
```
rm archzfs.gpg
```


## Changes to pacman.conf
Edit pacman.conf using nano.
```
nano pacman.conf
```
Add the following to the server list near the bottom:
```
[archzfs]
SigLevel = Optional TrustAll
Server = https://archzfs.com/$repo/$arch
```


## Changes to packages.x86_64
Edit packages.x86_64 using nano.
```
nano packages.x86_64
```
Add the following two packages to the list:
```
linux-headers
archzfs-linux
```


## Syncing Linux kernel with archzfs-linux
The linux install image can technically be made at this point. However, the following steps are to ensure that the install image works if the zfs-linux package is not up-to-date with the linux kernel.
Edit pacman.conf using nano.
```
nano pacman.conf
```
Add the following under [core], [extra] and [multilib]:
```
SigLevel = PackageRequired
Server = https://archive.archlinux.org/repos/2023/08/26/$repo/os/$arch
```
The date in "https://archive.archlinux.org/repos/2023/08/26/$repo/os/$arch" refers to the latest known linux kernel version that is compatible with archzfs-linux.
Check archzfs.com/archzfs/x86_64 for the correct date.


## Extra additions that might help
If you have slow internet, I strongly consider adding the DisableDownloadTimeout flag to the misc options section in pacman.conf.
Additionally, I recommend reducing the parallel downloads from 5 to 1-4 (I chose 3 personally), to limit spreading bandwidth usage acros multiple packages.


## Building archzfs.iso
Build the linux installation image using mkarchiso.
```
mkarchiso -v ./
```
This will start building the image within the current directory (~/archlive/releng).


# Finished!
Once the installation image has finished building, copy/burn it's contents onto a flash drive, and you're good to go!
