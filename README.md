# Hackintosh HP Z440 macOS High Sierra 10.13

## My specs
* Intel Xeon E5-1650 v3 3.5 GHz
* 2 * 8 GB RAM
* NVIDIA Quadro M4000 8 GB
* M2 PCIE SSD disk


## What is working
* Sleep (patching DSDT might be required for you, see below)
* USB 3
* Sound
* Ethernet
* Graphics
* FaceTime and iMessage


## What is not working
Nothing I'm aware of.


## How to use
Copy the CLOVER folder to your EFI partition. You must generate new SMBIOS and UUID as I've cleared mine out. Use MacPro6,1 in Clover Configurator.

```
	As a Hackintosher you should always want to generate your own unique information for your hackintosh so that you're not linked to someone else hackintosh or an existing Mac.

	For all these steps you will need to edit your config.plist. When messing around with the SBMIOS settings it's recommended create a backup first before proceeding:
	Mount your EFI partition with Clover Configurator
	Navigate to /Volumes/EFI/EFI/Clover/kexts/Other
	Backup your config.plist by making a duplicate copy of it and renaming the backup something else i.e. config-smbios-backup.plist1. Invalid Serial Number
	Right-click config.plist and open with Clover Configurator
	Click SMBIOS on left side
	Copy the Serial Number
	Go to checkcoverage.apple.com
	Paste Serial Number and click Continue
	If it comes back as valid you will need to generate a new serial number until you get a error or invalid serial number
	Go back to Clover Configurator SMBIOS section
	Repeat Generate New and paste to checkcoverage.apple.com until you get an Invalid Serial Number
	2. Board Serial Number

	Now that you have your own invalid serial number you need to replace the Board Serial Number with one containing your Invalid Serial Number along with 5 random digits. Since they can be any digits I keep the last five digits of the Board Serial Number and replace the first 12 with the Serial Number
	Copy Serial Number
	Replace the first 12 digits of Board Serial Number with the copied Serial Number
	3. smUUID
	Open Terminal
	Generate a unique string by entering uuidgen into Terminal
	Copy & Paste the generated UUID value from Terminal into SmUUID in Clover Configurator -> SMBIOS
	Save config.plist
	Restart Hackintosh
	4. ROM
	Select RTVariables in Clover Configurator
	Set ROM value to UseMacAddr0
	5. MLB

	RTVaraibles -> MLB can be left blank once the steps are followed. You can also use one off a valid Mac that you own if you wish, but it is not required or experiment with an MLB generator if you wish, but again following the steps above you shouldn't have any issues with simply leaving MLB blank.

	BooterConfig
	Set RTVariables -> BooterConfig to 0x28 for Intel for a AMD/Ryzen machine leave it at the value it's already set at.
	CsrActiveConfig
	CsrActiveConfig or SIP should be left in the disabled state i.e. 0x67 for more information see: Enable & Disable System Integrity Protection (SIP) on a Hackintosh
```




## Patch the DSDT
It's fully described [here](https://www.tonymacx86.com/threads/guide-patching-laptop-dsdt-ssdts.152573/) how to patch the DSDT. **NOTE: You can NOT use my patched DSDT.aml or anyone's else. You must do it yourself!** Short story:

* Boot into Clover. Press F4 and then boot into macOS.
* Clone iasl to a folder of your choice (`git clone https://github.com/RehabMan/Intel-iasl iasl`).
* `make && sudo make install` to build and put the binary at the default path for binaries.
* Download the latest MaciASL from [here](https://bitbucket.org/RehabMan/os-x-maciasl-patchmatic/downloads/).
* Mount the EFI partition and copy over the .aml files in `EFI/CLOVER/ACPI/origin` that start with DSDT or SSDT to a new folder somewhere.
* Run `iasl -da -dl DSDT.aml SSDT*.aml` in that directory.
* Open MaciASL and open the outputted DSDT.dsl.
* Apply patches. In my case, I wanted proper sleep (it was waking up by USB events):
	```
	into_all method label _PRW  remove_entry;
	into_all all code_regex Name.*_PRW.*\n.*\n.*\n.*\n.*\}\) remove_matched;
	```
* Save as ACPI Machine Language Binary to `EFI/CLOVER/ACPI/patched/DSDT.aml`.
* Reboot.

You might find other issues that you want to patch. Good luck!
