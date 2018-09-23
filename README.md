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