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
- Tulee mieleen joskus lisää...

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
