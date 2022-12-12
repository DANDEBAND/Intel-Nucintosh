# Intel-Nucintosh 2022
# Intel NUC8IxBEx Hackintosh

[ITALIANO]
Questo è un repository rapido per i modelli Intel NUC di ottava generazione Coffee Lake.
Ho usato varie fonti (vedi crediti) per costruire il mio EFI e ho fatto alcuni test.
Dovrebbe lasciarti con una build stabile e affidabile ma, come sempre, queste cose non sono mai davvero finite.
Compatibile con macOS Mojave, Catalina, Big Sur, Monterey e Ventura.

[ENGLISH]
This is a quick repository for the 8th Gen Coffee Lake Intel NUC models.
I used various sources (see credits) to build my EFI and did some testing.
It should leave you with a stable and reliable build but, as always, these things are never really over.
Compatible with macOS Mojave, Catalina, Big Sur, Monterey and Ventura.

![macOS Ventura](https://github.com/DANDEBAND/Intel-Nucintosh/blob/Images/ventura.png?raw=true)
Funziona | Working: macOS Ventura 13.0.1:
Download & info EFI folder [here](https://github.com/DANDEBAND/Intel-Nucintosh/issues/3)

## Dettagli | Details
* Works with macOS *Big Sur* and *Monterey*
* OpenCore bootloader with the following kexts:
  - Lilu
  - VirtualSMC
  - WhateverGreen
  - AppleALC
  - IntelMausi
  - NVMeFix
  - CPUFriend
  - BlueToolFixup -- fixes bluetooth in Monterey 
  - OpenIntelWireless for Intel bluetooth and wifi
  
## Index
* [Installazione | Installation](#installation)
* [Post install](#post-install)
* [Updating](#updating)
* [Intel wifi/bt](#intel-bluetooth-and-wifi)
* [Everything](#Everything)
* [Credits](#credits)
  
## Installation
+ Update to the latest (0090) BIOS -> load BIOS defaults -> click advanced and change;
```
Devices -> USB -> Port Device Charging Mode: off
Devices -> USB -> USB Legacy -> Disabled
Security -> Thunderbolt Security Level: Legacy Mode
Power -> Wake on LAN from S4/S5: Stay Off
Boot -> Boot Configuration -> Network Boot: Disable
Boot -> Secure Boot -> Disable
```
+ Download macOS from the App Store and create a USB installer with *[createinstallmedia](https://support.apple.com/en-us/HT201372)* on macOS (real mac/hack or vm) or use [gibMacOS](https://github.com/corpnewt/gibMacOS)\*
+ Download the EFI folder [here](https://github.com/DANDEBAND/Intel-Nucintosh/releases)
+ Edit config.plist with [Plistedit Pro](https://www.fatcatsoftware.com/plisteditpro/): Download
+ Edit config.plist with [OpenCore Configuration](https://mackie100projects.altervista.org/download-opencore-configurator/): Download
```
PlatformInfo -> Generic -> MLB
PlatformInfo -> Generic -> ROM
PlatformInfo -> Generic -> SystemSerialNumber
PlatformInfo -> Generic -> SystemUUID
```
\* Installers made with GibMacOS on Windows and Linux require a working internet connection as it uses the recovery image only, it then downloads the full installer from Apple. The *createinstallmedia* script makes an offline installer.

> Note: OpenCore doesn't always select the correct partition in the menu when installing. You will only boot into the installer once, do your formatting and have the installer copy all it needs to the internal disk. From that point onwards always select the internal disk from the menu. The name might change during the installation, but it shouyld be easy to spot as it won't have an "external" label.

## Post install
- Check if TRIM is enabled, If not run ```sudo trimforce enable``` to enable it
- Disable ```NVMeFix.kext``` if you don't have an NVMe drive
- Don't forget to copy the EFI folder from the installer's EFI partition to the internal disk's EFI partition. This is needed to boot from the internal disk. You can use [EFI Agent](https://github.com/headkaze/EFI-Agent) to easily mount EFI partition.

Finally make sure sleep works properly. You can skip some of these but it will make your machine wake up from time to time. Same as real Macs.
```
sudo pmset standby 0
sudo pmset autopoweroff 0 
sudo pmset proximitywake 0
sudo pmset powernap 0 
sudo pmset tcpkeepalive 0
sudo pmset womp 0
sudo pmset hibernatemode 0
```
The first two and last need to be ```0``` the rest can be left on if you want.

- Proximity wake can wake your machine when an iDevice is near
- Power Nap will wake up the system from time to time to check mail, make Time Machine backups, etc, etc
- Disabling TCP keep alive has resolved periodic wake events after setting up iCloud, just disabling Find My wasn't enough.
- Womp is wake on lan, which is disabled in the BIOS as it (going by other people's experience) might cause issues. I never use WOL, if you do use WOL please try enabling it in the BIOS and leave this setting on, the issues might have been due to bugs that haven been solved by now. Let me know if it works or not.
- Hibernate is sometimes set to 3 in my testing. It could be possible to get hibernation to work by using [HibernationFixup](https://github.com/acidanthera/HibernationFixup) but I haven't tested it. I'm fine with normal sleep.

With hibernation disabled you can delete the sleepimage file and create an empty folder in its place so macOS can't create it again, this saves some space and is optional.
```
sudo rm /var/vm/sleepimage
sudo mkdir /var/vm/sleepimage
```
 
At this point you should enable FileVault to encrypt your disk. The config is setup to support this and it works flawlessly.

To get a nicer boot experience you can remove the verbose boot flag ```-v```in the config and also set ```ShowPicker``` to false. This will show the Apple logo and not show any OpenCore menu or verbose booting. To get the OpenCore picker/menu to show hold down the *alt* key when booting.

That's all!

> Tip: Once everything works and you installed and configured all your stuff, create a bootable clone of your system with a trial version of *Carbon Copy Cloner* or *Superduper!*. Don't forget to copy your EFI folder to the clone's EFI partition. First time? Follow my little guide [here](https://github.com/zearp/OptiHack/blob/master/text/CLONE_IT.md).

## Updating
Updating is easy, first copy the MLB/ROM/SystemSerialNumber/SystemUUID values from your current config to a text file then delete the whole EFI folder and replace it with the latest release/clone from this repo. Copy your PlatformInfo fields from the text file into the new config. Unless you made other changes this is all thats needed.

## Intel bluetooth and wifi
+ Wifi works and can be managed using native tools, speeds are still slow but connections are stable
+ Bluetooth works for HID devices such as mouse, keyboard and audio stuff but connections are flaky. It may also not wake up from sleep properly

## Everything
+ Where possible further optimisations and ThunderBolt hot-plug support

## Credits
+ https://github.com/acidanthera
+ https://github.com/OpenIntelWireless
+ https://dortania.github.io/OpenCore-Install-Guide/config.plist/coffee-lake.html
+ https://github.com/osy
+ Many [random](https://github.com/Rashed97/Intel-NUC-DSDT-Patch/commit/47476815b52f8e4c97e8f85df158c9ab1b6ecedd) repos [with](https://github.com/honglov3/NUC8I7BEH) information [and](https://github.com/sarkrui/NUC8i7BEH-Hackintosh-Build) research [that](https://github.com/mbarbierato/Intel-NUC8i3BEH) I've [forgotten](https://github.com/honglov3/NUC8I7BEH) about.
