# Manjaro-seikkailut

Hauska seikkailu. Omat kokemukset ja turhautumiset kasattuna.

Tekstin tärkeimmät osat:
- Tee päiväkirjaa muutoksista mitä oot tehny laitteelle (txt tiedosto)
  - Helpottaa ongelmanratkaisuissa. Myös selkeyttää mitä on menny vahingossa asentamaan ja missä mahdolliset systemd -konfliktit
- Et v*ttu käytä Flatpak jos pamac/pacman/AUR on käytössä. Turha pintapuoleinen softa joka aiheuttaa hiusten menetystä.
  - LACT ei toimi Flatpak, koska sillei ole oikeuksia. Mielummin buildaat sen.
  - GPU Screen Recorder sama homma.
  - Näille on ohjeet, muistaakseni GPU Screen Recorder varoittaa Logeissa, että jos on asentanu Flatpakin ennemmin, täytyy muistaa poistaa ne systemd configit ja tehdä tarvittavat muutokset kuten daemon-reload
- Tarkista Linux kernel ja näyttisajurit.
- Huomio, onko X11 vai Wayland käytössä
  - ITSE KÄYTÄN WAYLANDia KOSKA SE TOIMII AMD:LLÄ AINAKI HENK.KOHT. PAREMMIN JA ON VIILEEMPI 😎😎😎 X11 on Nvidia vaihtoehto. Vitusti ongelmia ainakin Wayland + Nvidia läppäril... 
- Tulee mieleen joskus lisää...
  
## Partition
  
Helppoo ku lasten hakkaaminen:
  
Luot: 
- BOOT/EFI: 300 MiB. filesystem: `fat32` sen Mount `/boot/efi` ja tägiksi vaan boot
- SWAP: 1GiB - 100?GiB. filesystem: `linuxswap` tägi swap se on siinä. Mä laitoin kovalla kädellä noin 50GB
- ROOT: Loput tai tarpeeksi GiB. filesystem: `btrfs` tai `ext4` sijainti `/` ja vaik tägiks "Windows11" tai jotain muuta kivaa
  - Ite laitoin ext4 koska btrfs on "uudempi" ja "hitaampi". Mut YMMV.
  
## Network asetukset

VMP NetworkManager ja turhat checkupit jotka disconnectaa kerran tuntiin tai jotain vastaavaa:
  
https://wiki.archlinux.org/title/NetworkManager#Checking_connectivity
eli suomeksi:
`CTRL+ALT+T`:`touch /etc/NetworkManager/conf.d/20-connectivity.conf`

`nano /etc/NetworkManager/conf.d/20-connectivity.conf` tai `kate /etc/NetworkManager/conf.d/20-connectivity.conf`

```
[connectivity]
enabled=false
```
  
## Kernel asetukset
  
Helppo:
  
List: `sudo mhwd-kernel -l`
  
List Installed: `mhwd-kernel -li`

Itse asennus kernelille 6.14: `sudo mhwd-kernel -i linux614`

## GRUB boot settings

`kate /etc/default/grub`

```
# GRUB boot loader configuration

GRUB_DEFAULT=saved
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR='Manjaro'
GRUB_CMDLINE_LINUX_DEFAULT='quiet splash resume=UUID=4c564bec-d05d-486e-8565-eda1a3cfddc2 udev.log_priority=3'
GRUB_CMDLINE_LINUX="amdgpu.dcdebugmask=0x400 split_lock_detect=off"

...
```

## Environment variables
  
`kate /etc/environment`
  
Tonne esim: MangoHud=1 tjsp jos on sellanen asennettuna.
  
```
#
# This file is parsed by pam_env module
#
# Syntax: simple "KEY=VAL" pairs on separate lines
#

#WINEARCH=win32 WINEPREFIX=~/.wine winecfg

MANGOHUD=1
MANGOHUD_DLSYM=1
LD_PRELOAD=/usr/lib/mangohud/libMangoHud.so
GTK_USE_PORTAL=1
MOZ_ENABLE_WAYLAND=1
KWIN_DRM_NO_DIRECT_SCANOUT=1
#WINEPREFIX=~/.wine
#WINEARCH=win64

```

## Kovalevy / SSD / Partitionit
  
Hyvät selitykset ja ohjeet jos tuut jostain Windowsista ja sullon NTFS kovoja: 
https://github.com/ValveSoftware/Proton/wiki/Using-a-NTFS-disk-with-Linux-and-Windows
  
Konsole: `kate /etc/fstab`
->

```
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a device; this may
# be used with UUID= as a more robust way to name devices that works even if
# disks are added and removed. See fstab(5).
#
# <file system>             <mount point>  <type>  <options>  <dump>  <pass>
UUID=1028-C658                            /boot/efi      vfat    defaults,umask=0077 0 2
UUID=4c564bec-d05d-486e-8565-eda1a3cfddc2 swap           swap    defaults   0 0
UUID=0db1ac5d-1b1f-48ad-9e71-bf802bf20501 /              ext4    defaults   0 1
UUID=FCBE07DCBE078DF8                     /media/winos     ntfs-3g defaults,uid=1000,gid=1000,rw,user,exec,nofail,umask=000 0 2
UUID=6E8886E08886A5E5                     /media/ssdpny    ntfs-3g defaults,uid=1000,gid=1000,rw,user,exec,nofail,umask=000 0 2
UUID=527549F625B1DD0D                     /media/hdd2tb    ntfs-3g defaults,uid=1000,gid=1000,rw,user,exec,nofail,umask=000 0 2
UUID=265C14915C145DBD                     /media/hdd4tb    ntfs-3g defaults,uid=1000,gid=1000,rw,user,exec,nofail,umask=000 0 2
#UUID=B83485AD34856F66                     /media/nvme      ntfs-3g defaults,uid=1000,gid=1000,rw,user,exec,nofail,umask=000 0 2
tmpfs                                     /tmp           tmpfs   defaults,noatime,mode=1777 0 0

```

## FILEMANAGERS PCmanFM Dolphin mitä lie
  
sudo pacman -S xdg-desktop-portal xdg-desktop-portal-kde
  
Tällä pitäis saada Firefoxilla ja PCmanFM:llä toimimaan paremmin file save file openingit? Jos käyttää Dolphinia, niin en oo varma tartteeko tätä asentaa.
  
## Keychron näppis asetukset/ongelmat
KEYCHRON SETTINGS
Etsi Keychron ->
```
sudo dmesg | grep -i "keychron" /
sudo dmesg | grep hidraw
```

Löydä se-> `hid-generic 0003:3434:0353.0005: hiddev97,hidraw5: USB HID v1.11 Device [Keychron Keychron V5] on usb-0000:0c:00.3-2/input1`

?lsusb
Anna sille muokkausoikeudet, että pystyt Chromium -selaimella muokkaamaan asetuksia:

`sudo chmod a+rw /dev/hidraw5`
  
## GAMING
  
Se tärkein: Pelaaminen. Lutrisin asennat niin saat "System (10.7(Staging))" jolla pystytään esim asentamaan Lutrisin sisäisesti Epic Games Launcherin.
Wine32 on tärkeää kans asentaa ja sille löytyy hyvät ohjeet:

https://github.com/lutris/docs/blob/master/WineDependencies.md
  
Lainaten rajusti:
  
Arch/EndeavourOS/Manjaro/Other Arch derivatives

Enable multilib:

To enable multilib repository, uncomment the [multilib] section in /etc/pacman.conf

/etc/pacman.conf
--------------------------------------------------------------------------------------
[multilib]
Include = /etc/pacman.d/mirrorlist

Sitten päivittä paketit ja asennat wine komponentit:
  
`sudo pacman -Syu` 
 
```
sudo pacman -S wine-staging

sudo pacman -S --needed --asdeps giflib lib32-giflib gnutls lib32-gnutls v4l-utils lib32-v4l-utils libpulse \
lib32-libpulse alsa-plugins lib32-alsa-plugins alsa-lib lib32-alsa-lib sqlite lib32-sqlite libxcomposite \
lib32-libxcomposite ocl-icd lib32-ocl-icd libva lib32-libva gtk3 lib32-gtk3 gst-plugins-base-libs \
lib32-gst-plugins-base-libs vulkan-icd-loader lib32-vulkan-icd-loader sdl2-compat lib32-sdl2-compat
```

### Discord
  
Asenna se pacmanilla. Thas it
  
`sh -c "$(curl -sS https://raw.githubusercontent.com/Vendicated/VencordInstaller/main/install.sh)"`

