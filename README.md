# Manjaro-seikkailut

Hauska seikkailu. Omat kokemukset ja turhautumiset kasattuna.
  
Tekstin tärkeimmät osat:
-
- Lue [Arch Linux](https://wiki.archlinux.org/title/Arch_Linux) -wikiä. Sielä saattaa olla joskus vanhentunutta tietoa ja "väärää", mutta esim. [kuinka saadaan thumbnail](https://wiki.archlinux.org/title/File_manager_functionality#Thumbnail_previews) -kuvakkeet näkymään resurssienhallinnassa.
- Tee päiväkirjaa muutoksista mitä oot tehny laitteelle (txt tiedosto)
  - Helpottaa ongelmanratkaisuissa. Myös selkeyttää mitä on menny vahingossa asentamaan ja missä mahdolliset systemd -konfliktit
- Et v*ttu käytä Flatpak jos pamac/pacman/AUR on käytössä. Turha pintapuoleinen softa joka aiheuttaa hiusten menetystä.
  - LACT ei toimi Flatpak, koska sillei ole oikeuksia. Mielummin buildaat sen.
  - GPU Screen Recorder sama homma.
  - Näille on ohjeet, muistaakseni GPU Screen Recorder varoittaa Logeissa, että jos on asentanu Flatpakin ennemmin, täytyy muistaa poistaa ne systemd configit ja tehdä tarvittavat muutokset kuten daemon-reload
- Tarkista Linux kernel ja näyttisajurit.
- Huomio, onko X11 vai Wayland käytössä
  - ITSE KÄYTÄN WAYLANDia KOSKA SE TOIMII AMD:LLÄ AINAKI HENK.KOHT. PAREMMIN JA ON VIILEEMPI 😎😎😎 X11 on Nvidia vaihtoehto. Piruuusti ongelmia ainakin Wayland + Nvidia läppäril... 
- Tulee mieleen joskus lisää...

Selvittämättömät ongelmat: 
- 
- Sleep mode ei toimi? Ehkä BIOS ongelma
  - Sleep -> Hard reset
  - Sleep -> Login Screen -> Hard reset
  - Hibernation toimii? Mutta ei niin käytännöllinen

# SISÄLTÖ
- [Manjaro-seikkailut](#manjaro-seikkailut)
- [Gaming](https://github.com/SaKaarle/Manjaro-seikkailut/blob/main/Gaming/Readme.md)
- [Partition](#partition)
- [Network asetukset](#network-asetukset)
- [Kernel asetukset](#kernel-asetukset)
- [GRUB boot settings](#grub-boot-settings)
- [Environment variables](#environment-variables)
- [Kovalevy / SSD / Partitionit](#kovalevy--ssd--partitionit)
- [FILEMANAGERS PCmanFM Dolphin mitä lie](#filemanagers-pcmanfm-dolphin-mitä-lie)
- [Keychron näppis asetukset/ongelmat](#keychron-näppis-asetuksetongelmat)
  - [KEYCHRON SETTINGS](#keychron-settings)
  - [KEYCHRON ON OHJAIN JA SE PILAA ELÄMÄN](#keychron-on-ohjain-ja-se-pilaa-elämän)
- [Sovellus suositukset](#sovellus-suositukset)
- [GAMING](#gaming)
  - [Discord](#discord)
- [ONGELMA TILANTEET](#ongelma-tilanteet)
- [FIREFOX](#Firefox)
- [FreeCAD](#FreeCAD)
- [Docker SearXNG Setup](#docker-searxng-setup)
  
## Partition
  
Helppoo ku lasten hakkaaminen:
  
Luot: 
- BOOT/EFI: 300 MiB. filesystem: `fat32` sen Mount `/boot/efi` ja tägiksi vaan boot
- SWAP: 1GiB - 100?GiB. filesystem: `linuxswap` tägi swap se on siinä. Mä laitoin kovalla kädellä noin 50GB joka saatto olla liikaa ja ei taida edes toimia? Läppärillä on 8GB annettuna.
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
  
`split_lock_detect=off` jostain syystä ongelma ja täytyy olla OFF että kone toimii eikä kaatuile. [Level1Techs](https://forum.level1techs.com/t/9070-and-9070-xt-setup-notes-for-linux/227038)
  
`kate /etc/default/grub`
  
```
# GRUB boot loader configuration

GRUB_DEFAULT=saved
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR='Manjaro'
GRUB_CMDLINE_LINUX_DEFAULT='quiet splash resume=UUID=4c564bec-d05d-486e-8565-eda1a3cfddc2 udev.log_priority=3' # TAI GRUB_CMDLINE_LINUX="amdgpu.ppfeaturemask=0xffffffff amdgpu.dc=1 amdgpu.runpm=0 amdgpu.dcdebugmask=0x10 amdgpu.dcdebugmask=0x12 amdgpu.dcdebugmask=0x400 split_lock_detect=off". Vähentää mahd. näyttiksen kaatumista kernelin tasolla. AMDGPU:lla jokin ongelma
GRUB_CMDLINE_LINUX="amdgpu.dcdebugmask=0x400 split_lock_detect=off"

...
```
  
Toinen mahdollinen fikis:
` GRUB_CMDLINE_LINUX="amdgpu.ppfeaturemask=0xffffffff amdgpu.dc=1 amdgpu.runpm=0 amdgpu.dcdebugmask=0x10 amdgpu.dcdebugmask=0x12 `
  
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

## 2025 joulu ##

#
# This file is parsed by pam_env module
#
# Syntax: simple "KEY=VAL" pairs on separate lines
#

# WINEARCH=win32 WINEPREFIX=~/.wine winecfg

MANGOHUD=1
# MANGOHUD_DLSYM=1
LD_PRELOAD=/usr/lib/mangohud/libMangoHud.so
GTK_USE_PORTAL=1
MOZ_ENABLE_WAYLAND=1
KWIN_DRM_NO_DIRECT_SCANOUT=1
WLR_SCENE_DISABLE_DIRECT_SCANOUT=1
AMD_VULKAN_ICD=RADV
MESA_SHADER_CACHE_MAX_SIZE=20G
# WINEPREFIX=~/.wine
# WINEARCH=win64 WINEPREFIX=~/.wine winecfg


```

## Kovalevy / SSD / Partitionit
  
Hyvät selitykset ja ohjeet jos tuut jostain Windowsista ja sullon NTFS kovoja: 
https://github.com/ValveSoftware/Proton/wiki/Using-a-NTFS-disk-with-Linux-and-Windows
  
Käytä mielummin EXT4 tai vastaavaa. Stabiilisuus ongelmat NTFS-3 kanssa. NTFSplus on tulossa ja testaamatta.
  
```
lsblk -o NAME,FSTYPE,UUID,MOUNTPOINTS                                                                                    ✔ 

NAME        FSTYPE UUID                                 MOUNTPOINTS
sda                                                     
└─sda1      ext4   37bd1f24-f3ca-4f21-82c2-625243a3bf6c /media/hdd2tb
sdb                                                     
└─sdb1      ext4   28c3b6b9-618f-46b7-8e43-9a19142808bd /media/hdd4tb
sdc                                                     
└─sdc1      ext4   f078da2f-158e-4037-84ba-bc950c2b50c4 /media/ssdpny
nvme1n1                                                 
├─nvme1n1p1 swap   4c564bec-d05d-486e-8565-eda1a3cfddc2 [SWAP]
├─nvme1n1p2 vfat   1028-C658                            /boot/efi
└─nvme1n1p3 ext4   0db1ac5d-1b1f-48ad-9e71-bf802bf20501 /
nvme0n1                                                 
├─nvme0n1p1                                             
├─nvme0n1p2 ntfs   E8FE4705FE46CC0E                     /media/winos
├─nvme0n1p3 ntfs   EA22ABBB22AB8B61                     
└─nvme0n1p4 ext4   ed6607b7-1260-4031-9ef2-d51abcf8b9a3 /media/winsteam
```
  
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
UUID=E8FE4705FE46CC0E                     /media/winos     ntfs-3g defaults,uid=1000,gid=1000,rw,user,exec,nofail,umask=000 0 2
#UUID=35F24537031CD2D1                     /media/ssdpny    ntfs-3g defaults,uid=1000,gid=1000,rw,user,exec,nofail,umask=000 0 2
UUID=f078da2f-158e-4037-84ba-bc950c2b50c4 /media/ssdpny    ext4 defaults,nofail 0 2
#UUID=527549F625B1DD0D                     /media/hdd2tb    ntfs-3g defaults,uid=1000,gid=1000,rw,user,exec,nofail,umask=000 0 2
UUID=37bd1f24-f3ca-4f21-82c2-625243a3bf6c /media/hdd2tb    ext4 defaults,nofail 0 2
#UUID=265C14915C145DBD                     /media/hdd4tb    ntfs-3g defaults,uid=1000,gid=1000,rw,user,exec,nofail,umask=000 0 2
UUID=28c3b6b9-618f-46b7-8e43-9a19142808bd /media/hdd4tb    ext4 defaults,nofail 0 2
#UUID=0852C72352C7147A                     /media/winsteam  ntfs-3g defaults,uid=1000,gid=1000,rw,user,exec,nofail,umask=000 0 2
UUID=ed6607b7-1260-4031-9ef2-d51abcf8b9a3 /media/winsteam  ext4 defaults,nofail 0 2
#UUID=B83485AD34856F66                     /media/nvme      ntfs-3g defaults,uid=1000,gid=1000,rw,user,exec,nofail,umask=000 0 2
tmpfs                                     /tmp           tmpfs   defaults,noatime,mode=1777 0 0

```

## FILEMANAGERS PCmanFM Dolphin mitä lie
  
`sudo pacman -S xdg-desktop-portal xdg-desktop-portal-kde`
  
Tällä pitäis saada Firefoxilla ja PCmanFM:llä toimimaan paremmin file save file openingit? Jos käyttää Dolphinia, niin en oo varma tartteeko tätä asentaa.
  
## Keychron näppis asetukset/ongelmat
### KEYCHRON SETTINGS
Etsi Keychron ->
```
sudo dmesg | grep -i "keychron" \
sudo dmesg | grep hidraw \
sudo dmesg | grep hiddev

```
  
Löydä se "hiddev" -> `hid-generic 0003:3434:0353.0005: hiddev97,hidraw5: USB HID v1.11 Device [Keychron Keychron V5] on usb-0000:0c:00.3-2/input1`
  
`lsusb`
  
Anna sille muokkausoikeudet, että pystyt Chromium -selaimella muokkaamaan asetuksia:
  
`sudo chmod a+rw /dev/hidraw5`
  
### KEYCHRON ON OHJAIN JA SE PILAA ELÄMÄN
  
Search:
```
lsusb
```
```
dmesg | grep -i "keychron"
lsmod | grep joy
```
Make edits to file:
```
kate /lib/udev/rules.d/99-keychron-blacklist.rules
SUBSYSTEM=="input", ATTRS{idVendor}=="3434", ATTRS{idProduct}=="0353", KERNEL=="event*", ENV{ID_INPUT_JOYSTICK}="0"
```
`udevadm test /dev/input/event7` tai event5 tai mikä lie lukee Gamepad asetuksissa.

`sudo udevadm control --reload-rules` ja `sudo udevadm trigger`
^^^^^
  
*Boom gone. Katos ku pieru saharaan. Ei oo enää System Settingsissä ohjaimena Keychronia.*


## Sovellus suositukset
  
Lista omista ehdottoman lenppareista lemppari sovelluksista ilman pahempaa syytä:
  
- Fsearch ja Catfish
  - Fsearch löytää paremmin kaikki
  - Catfish löytää tekstin sisältä paremmin kaikki. Hyvä olla molemmat valmiina asennettuna.
- Kate = Notepad++
- Gparted
- Lutris
- LACT (AMD GPU overclock tool)
- GPU Screen Recorder (Shadow Play UI:lla)
- MangoHud + Goverlay
  - MangoHudil hyvä hudi ofc ja Goverlayllä helppo conffata ja saa parhaiten FPS_Limitin conffattua
- ProtonUpQT
- Pavucontrol
  - Ois kiva tietää kunnon volume control tai tällänen et voi säätää kunnol asetuksii mut tää on kans iha ookoo ☺️

  
## GAMING
  
Se tärkein: Pelaaminen. Lutrisin asennat niin saat "System (10.7(Staging))" jolla pystytään esim asentamaan Lutrisin sisäisesti Epic Games Launcherin.
Wine32 on tärkeää kans asentaa ja sille löytyy hyvät ohjeet:

https://github.com/lutris/docs/blob/master/WineDependencies.md
  
Lainaten rajusti:
  
Arch/EndeavourOS/Manjaro/Other Arch derivatives

Enable multilib:
```
To enable multilib repository, uncomment the [multilib] section in /etc/pacman.conf
[multilib]
Include = /etc/pacman.d/mirrorlist
```
Sitten päivittä paketit ja asennat wine komponentit: `sudo pacman -Syu`
  
```
sudo pacman -S wine-staging

sudo pacman -S --needed --asdeps giflib lib32-giflib gnutls lib32-gnutls v4l-utils lib32-v4l-utils libpulse \
lib32-libpulse alsa-plugins lib32-alsa-plugins alsa-lib lib32-alsa-lib sqlite lib32-sqlite libxcomposite \
lib32-libxcomposite ocl-icd lib32-ocl-icd libva lib32-libva gtk3 lib32-gtk3 gst-plugins-base-libs \
lib32-gst-plugins-base-libs vulkan-icd-loader lib32-vulkan-icd-loader sdl2-compat lib32-sdl2-compat

```
  
Asiaan tullut mahdollinen muutos koska Wine on tyhmä ja devit päätti lopettaa melkeen kokonaan 32-bit yhteensopivuuden, niin Wine32 paketti pitää asentaa jos haluaa uudemmat 32-bit supportit ja pelata esimerkiksi Deus Ex tai muita 32-bit pelejä Winellä.
  
```
https://aur.archlinux.org/packages/wine32

yay -S wine32

```
  
### Discord
  
Asenna se pacmanilla. Thas it
  
`sh -c "$(curl -sS https://raw.githubusercontent.com/Vendicated/VencordInstaller/main/install.sh)"`
  
## ONGELMA TILANTEET
  
Launchaa sovellus tai muu terminaalin kautta. Esimerkki debuggauksesta:

 INFO drg_mod_integration: writing logs to `/home/saku/.local/share/drg-mod-integration/drg-mod-integration.log`
interface 'wl_surface' has no event 2
  
Netistä löyty jotain ongelmia Waylandin ja X11 kanssa. Vastauksena on WINIT_UNIX_BACKEND=x11 jotta voidaan avata drg-mod-integration:

->->
kate /.zshrc
->->
```
# Use powerline
USE_POWERLINE="true"
# Has weird character width
# Example:
#    is not a diamond
export WINIT_UNIX_BACKEND=x11
HAS_WIDECHARS="false"
# Source manjaro-zsh-configuration
if [[ -e /usr/share/zsh/manjaro-zsh-config ]]; then
  source /usr/share/zsh/manjaro-zsh-config
fi
# Use manjaro zsh prompt
if [[ -e /usr/share/zsh/manjaro-zsh-prompt ]]; then
  source /usr/share/zsh/manjaro-zsh-prompt
fi
```
Sinne lisäät vaikka pohjalle:
"export WINIT_UNIX_BACKEND=x11"

Tallenna, `source /.zshrc` terminaaliin ja avaat terminaalin kautta `./drg-mod-integration` ja ei tuu mitää virheitä ja softa aukeaa.
Tai sitten bash skripti:
  
`kate ./paskaperse.sh`
  
```
#!/bin/bash
export WINIT_UNIX_BACKEND=x11
./drg_mod_integration
```

## FIREFOX
  
[Firefox Arch Wiki](https://wiki.archlinux.org/title/Firefox/Tweaks#Disable_WebRTC_audio_post_processing)
  
Teams tai muu selaimella käytettävä mikrofoni sovellus vaatii tämän, ellet halua kuulevia korvia tuhota täysin:
  
URL: `about:config` ja yks kerralla: 
  
```
media.getusermedia.audio.processing.aec.enabled 
media.getusermedia.audio.processing.agc.enabled
media.getusermedia.audio.processing.noise.enabled
media.getusermedia.audio.processing.hpf.enabled
```

## FreeCAD
  
Wayland on buginen paska ja jäätyy ja on hidas. Täytyy käynnistää: `QT_QPA_PLATFORM=xcb` avulla, eli:
  
```

QT_QPA_PLATFORM=xcb freecad

```

## Docker SearXNG Setup 
  
Asenna NPM. Asenna SearXNG Container. Tai installer step by step. Ohjeet myöhemmin.
  
[Docs Searxng | Installation docker](https://docs.searxng.org/admin/installation-docker.html#installation-container)
  
Container on helppo ja parempi jos ei oo servukonetta johonka rakentaa scripti

[Step-by-step](https://docs.searxng.org/admin/installation-searxng.html) prosessi tai [Searxng-git](https://aur.archlinux.org/packages/searxng-git) Aur package asennus servukoneelle. Vaatii konfigurointia. Tarkemmat tiedot myöhemmin:
  
-> `kate /etc/searxng/settings.yml` ->
  
```
use_default_settings: true
server:
    bind_address: "192.168.178.103"
    secret_key: "c" # <- piiloitettu info
    limiter: true
    base_url: "http://192.168.178.103:8888/"
valkey:
  url: unix:///run/valkey/valkey.sock?password=yourpassword&db=1 # <- Vaiha salasana
checker:
    scheduling:
    start_after: [300, 1800]  # delay to start the first run of the checker
    every: [86400, 90000]     # how often the checker runs

```
Valkey conffaus:

`kate /etc/valkey.conf` -> `requirepass your_strong_password_here`

  Docker configurointi ja käynnistys:

```
mkdir -p ~/searxng/config ~/searxng/data
cd ~/searxng
```
  
```
docker run --name searxng -d \
  -p 8888:8080 \
  -v "$HOME/searxng/config:/etc/searxng" \
  -v "$HOME/searxng/data:/var/cache/searxng" \
  docker.io/searxng/searxng:latest
```
"This follows the official install instructions: create config & data folders and mount them to /etc/searxng and /var/cache/searxng."

`kate /home/sakurazer/searxng/config/settings.yml` | `kate ~/searxng/config/settings.yml`

Tämä muokkaus ->
  
```

search:
  formats:
    - html
    - json

```
  
```

❯ docker ps -a
CONTAINER ID   IMAGE                    COMMAND                  CREATED      STATUS                  PORTS     NAMES
0ca6ea77c0e1   searxng/searxng:latest   "/usr/local/searxng/…"   7 days ago   Exited (0) 7 days ago             searxng

❯ docker start searxng
searxng

❯ docker container list
CONTAINER ID   IMAGE                    COMMAND                  CREATED      STATUS         PORTS                                         NAMES
0ca6ea77c0e1   searxng/searxng:latest   "/usr/local/searxng/…"   7 days ago   Up 2 minutes   0.0.0.0:8888->8080/tcp, [::]:8888->8080/tcp   searxng

```
  
Docker RUN: `docker start searxng`
  
LM STUDIO MCP.JSON tiedosto:
  
  
```
{
  "mcpServers": {
    "searxng": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-searxng"
      ],
      "env": {
        "SEARXNG_URL": "http://localhost:8888"
      }
    }
  }
}
```
