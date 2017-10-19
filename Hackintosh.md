# Prepare Bootable USB Drive

[Source 1](https://www.tonymacx86.com/threads/how-to-create-a-macos-high-sierra-public-beta-installation-usb.225520/),
[Source 2](https://www.tonymacx86.com/threads/guide-booting-the-os-x-installer-on-laptops-with-clover.148093/)

1. Insert the USB drive
2. Open a Terminal
3. Determine the USB drive
4. Format the USB drive
```
sudo diskutil eraseDisk JHFS+ USB disk1`
```
5. Create the Install Media
```
sudo /Applications/Install\ macOS\ High\ Sierra.app/Contents/Resources/createinstallmedia \
--volume /Volumes/USB --applicationpath  /Applications/Install\ macOS\ High\ Sierra.app --nointeraction
```
6. Download latest [Clover](http://sourceforge.net/projects/cloverefiboot/)
7. Open Clover package installer, choose the Install Location „Install macOS High Sierra“
8. Customize and check
```
v Installation for UEFI-Motherboards
v Install Clover in the ESP
- Themes -> v Black Green
- Drivers64UEFI -> v OsxAptioFix2Drv-64
```
9. Install Clover
10. Replace config.plist
```
mv /Volumes/ESP/EFI/CLOVER/config.plist /Volumes/ESP/EFI/CLOVER/config-original.plist
cp Downloads/config-sample.plist /Volumes/ESP/EFI/CLOVER/config.plist
```
11. Copy apfs.efi
```
hdiutil attach /Applications/Install\ macOS\ High\ Sierra.app/Contents/SharedSupport/BaseSystem.dmg
cp /Volumes/OS\ X\ Base\ System/usr/standalone/i386/apfs.efi /Volumes/ESP/EFI/CLOVER/drivers64UEFI/
hdiutil detach /Volumes/OS\ X\ Base\ System/
```
12. Copy some required kexts
```
cp -av Downloads/FakeSMC.kext /Volumes/ESP/EFI/CLOVER/kexts/Other/
cp -av Downloads/RealtekRTL8111.kext /Volumes/ESP/EFI/CLOVER/kexts/Other/
```
13. Umount everything
```
diskutil umount /Volumes/ESP
diskutil umount /Volumes/Install\ macOS\ High\ Sierra
```
   
# Install High Sierra

1. Disable CPU Graphics in BIOS
2. Always Boot from USB Drive
...

# Post-Install High Sierra

1. follow the Steps 6. - 9. from Prepare Bootable USB Drive
   additional in step 8.
```
v Install RC Scripts on Target volume
```
2. Copy config.plist, apfs.efi, FakeSMC.kext, RealtekRTL8111.kext from USB Drives EFI Partition
   to the Target Volumes EFI partition

# Install NVIDIAs alternate Graphics Drivers

[Source](https://www.tonymacx86.com/threads/nvidia-releases-alternate-graphics-drivers-for-macos-high-sierra-10-13-0-378-10-10-10-15.225522/)

1. Download [Driver](https://images.nvidia.com/mac/pkg/378/WebDriver-378.10.10.10.15.117.pkg)
2. Modify EFI config.plist 
```
Enable SIP by changing CsrActiveConfig from 0x3 to 0x0
```
3. Reboot
4. Install NVDIA WebDriver and approve the kext installation under Security Settings, but don’t reboot yet
5. Touch the two major kext directories
```
sudo touch /Library/Extensions/ /System/Library/Extensions/
```
6. Modify EFI config.plist 
```
Disable SIP again by changing CsrActiveConfig back from 0x0 to 0x3
Change Boot Parameters replace nv_disable=1 with nvda_drv=1
```
7. Reboot and Enable CPU Graphics in BIOS

# Install Realtek Audio Drivers

[Source](https://github.com/toleda/audio_CloverALC)

1. Download [CloverALC](https://github.com/toleda/audio_CloverALC/blob/master/audio_cloverALC-130.command.zip)
2. Mount EFI partition
3. Run audio_cloverALC-130.command and approve under Security Settings
4. Answer the scripts questions
```
Confirm Realtek ALC887: y
Patch … HDMI audio: n
Clover Audio ID Injection: n
```
5. Reboot
6. Sound Settings -> Output -> Internal Speakers
