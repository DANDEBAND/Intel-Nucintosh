# Intel-Nucintosh 2023 (Update 29/03/2023)
# Intel NUC8IxBEx Hackintosh

[ITALIANO]
Questo è un repository rapido per i modelli Intel NUC di ottava generazione Coffee Lake.
Ho usato varie fonti (vedi crediti) per costruire il mio EFI e ho fatto alcuni test.
Dovrebbe lasciarti con una build stabile e affidabile ma, come sempre, queste cose non sono mai davvero finite.
Compatibile con macOS Mojave, Catalina, Big Sur, Monterey, Ventura e Sequoia.

[ENGLISH]
This is a quick repository for the 8th Gen Coffee Lake Intel NUC models.
I used various sources (see credits) to build my EFI and did some testing.
It should leave you with a stable and reliable build but, as always, these things are never really over.
Compatible with macOS Mojave, Catalina, Big Sur, Monterey, Ventura and Sequoia.

![macOS Sequoia](https://github.com/DANDEBAND/Intel-Nucintosh/blob/main/More/images/Sequoia.jpg?raw=true)
Funziona | Working: macOS Sequoia 15.3:
Download & info EFI folder [here](https://github.com/DANDEBAND/Intel-Nucintosh/releases)

## Dettagli | Details
* Works with macOS *Monterey* and *Ventura*
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
* [Update Bios Intel](#update-bios-intel)
* [Intel wifi/bt](#intel-bluetooth-and-wifi)
* [Broadcom Wireless/Bletooth](#Broadcom-Wireless-and-Bluetooth)
* [Everything](#Everything)
* [Credits](#credits)
  
## Installation
+ Update to the latest (0092) BIOS -> load BIOS defaults -> click advanced and change;
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

## Updating
Updating is easy, first copy the MLB/ROM/SystemSerialNumber/SystemUUID values from your current config to a text file then delete the whole EFI folder and replace it with the latest release/clone from this repo. Copy your PlatformInfo fields from the text file into the new config. Unless you made other changes this is all thats needed.

## Update Bios Intel
Aggiornamento del BIOS [BECFL357]
Questo record di download contiene opzioni per l'aggiornamento del BIOS di Kit Intel® NUC NUC8i7BE, NUC8i5BE e NUC8i3BE.
[here](https://www.intel.it/content/www/it/it/download/743906/bios-update-becfl357.html?wapkw=nuc8i7beh).

## Intel bluetooth and wifi
+ Wifi works and can be managed using native tools, speeds are still slow but connections are stable
+ Bluetooth works for HID devices such as mouse, keyboard and audio stuff but connections are flaky. It may also not wake up from sleep properly

## Broadcom Wireless and Bluetooth

Apple/3rd party bluetooth and wifi

For both 1st and 3rd party you will need a supported wifi/bluetooth combo card and an adapter (see below) to convert it to M key. As far as I know compatible M key combo cards don't exist.

3rd party cards will need these kexts: AirportBrcmFixup + BrcmPatchRAM, read the instructions on the repo's and you'll be up and running in no time. I've tested the very affordable DW1820A in many machines including the NUC and it works great. For some cards you may need to create an entry under devices in the config that disables ASPM, this only needed if you have issues with sleep.

1st party is my preferred option. Grab an Apple 6+12 pin to m.2 M-key converter card and go native with something like the BCM94360CS2. Please note the number of antenna connectors. Some have more than 2, so you'll have to add some antenna's and maybe even mod your case. Though there is some room under the plastic lid, it can fit internal antennas like this. The lid can be removed with some strategic force and there's a hole to route the wires trough too. I would use those and leave the standard antenna's connected to the Intel module. They're very cheap and the antenna connectors on the Intel module are very fragile.

One big plus of going native is that you gain HID-proxy. This means that when there is no OS running the Airport card will proxy any paired HID bluetooth devices to the machine as usb devices. This means you can enter the BIOS or boot menu using the bluetooth keyboard and mouse. This is not a feature you will find on many other cards, including the the one Intel put in here. Even expensive bluetooth cards often can not do this. But Apple has added it even in the cheap BCM943224PCIEBT2 Airport card.

Speaking of the $10 BCM943224PCIEBT2, I've personally tested that card and it still works fine in Catalina by setting Kernel -> Patch -> 0 to true. Big Sur will need the patch disabled and AirportBrcmFixup added with boot flags brcmfx-driver=2 brcmfx-country=#a instead. For Monterey you will need to patch the installer which will disable SIP and isn't recommended. You can also add your card as a device in the configs DeviceProperties section and set the options there, for example;

```
<key>PciRoot(0x0)/Pci(0x1C,0x4)/Pci(0x0,0x0)</key>
 	<dict>
 		<key>AAPL,slot-name</key>
 		<string>Internal@0,28,4/0,0</string>
		<key>device_type</key>
 		<string>Network controller</string>
 		<key>model</key>
 		<string>Apple Airport BCM43224 802.11a/b/g/n</string>
 		<key>brcmfx-country</key>
 		<string>#a</string>
 		<key>brcmfx-driver</key>
 		<string>2</string>
 	</dict>
```
Make sure you check if the PciRoot/slot-name paths are correct, you can find them in IOreg or Hackintool. Also make sure the AirPortBrcm4360_Injector.kext plug-in that will be added if you use the ProperTree snapshot command is disabled. It is part of AirportBrcmFixup but can cause Monterey boot-up to stall and wifi not working properly (shows as disabled).

Some sellers on AliExpress have converter cards that already have the small 1.25mm pitch jst connector on it. It connects to one of the two internal usb ports. I use one without issues in my NUC. They usually list them as NUC8 compatible and cost a bit more than other converter cards.

Those other cards (and 3rd party ones) do not come with this connector so you'd have to make your own. My cheaper eBay card came with a cable with standard internal usb header and a cable without any plugs so you can attach your own. Check the listing carefully before ordering. Also make sure it converts to M key and once you have it that the spacing pillar is in the correct position. Don't short the poor Airport out.

The two internal usb ports are already mapped in the USBPorts.kext, if you made your own map you'll need to make a new map if you use the internal usb headers
When using a 1st or 3rd party combo card you need to disable both bluetooth and wifi in the BIOS and also remove any Intel related bluetooth and wifi kexts
You will also need to remove the config block for HS07 used by the onboard Intel wifi/bt card (this was HS10 in previous usb portmaps) from Info.plist inside USBPorts.kext, without this step bluetooth won't work (properly) after sleep. On 1st party cards it gets "stuck" in HID-proxy mode; bluetooth mouse and keyboard may still work but not optimally and laggy.
You'll also want to set your region to #a as it allows for full 80mhz channel width on ac cards. It might not be 100% legal depending on where you live. I've used this method on a few DW1820A cards and the speed increase was pretty amazing. This method may also apply when using real Apple cards, you will need add AirportBrcmFixup on 1st party cards. To change the region simply add the following boot flag brcmfx-country=#a. Make sure your router also supports 80mhz channel width and before doing anything hold down alt while clicking the wifi icon to check the current channel width.

One last thing to remember is that waking the machine from sleep using bluetooth devices will not work. This is due to power being cut to the module. The module does start itself up very fast. By the time my screen wakes up my bluetooth devices are already reconnected. There is way to bypass this but it includes either modding your adapter card or making your own. I've asked some sellers on AliExpress to produce this card but didn't have any luck. If you can make it or know a seller who's willing to make it please let me know.

## Everything
+ Where possible further optimisations and ThunderBolt hot-plug support

## Credits
+ https://github.com/acidanthera
+ https://github.com/OpenIntelWireless
+ https://dortania.github.io/OpenCore-Install-Guide/config.plist/coffee-lake.html
+ https://github.com/dortania/OpenCore-Legacy-Patcher/releases
+ https://dortania.github.io/builds
+ Many [random](https://github.com/Rashed97/Intel-NUC-DSDT-Patch/commit/47476815b52f8e4c97e8f85df158c9ab1b6ecedd) repos [with](https://github.com/honglov3/NUC8I7BEH) information [and](https://github.com/sarkrui/NUC8i7BEH-Hackintosh-Build) research [that](https://github.com/mbarbierato/Intel-NUC8i3BEH) I've [forgotten](https://github.com/honglov3/NUC8I7BEH) about.
