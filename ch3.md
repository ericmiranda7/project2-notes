# Bootloaders
## Purpose Of A Bootloader
- Primary purpose
	1. Get the system loaded into a reasonable minimum state
	2. Load the kernel
- Tertiary
	- Provide maintenance mode for updating boot configs, add new boot images, diagnostics, etc

## Boot Sequence
![[Pasted image 20220428225735.png]]
### Phase 1 - ROM Code
SoC Contents
The ROM code is essentially at the reset vector of the CPU, ie, the first instruction that is run by the CPU when it powers on. This is a JUMP instruction to some preprogrammed location in secondary flash memory or MMC chips.
This code initialises the bootloader and loads it into the SRAM.
If the SRAM is too small to load the bootloader, a secondary program loader (SPL) is loaded into SRAM.
At the end of this phase, ROM code makes a jump to SPL

### Phase 2 - Secondary Program Loader
The SPL initialises memory controllers, etc so it can read secondary memory and load a TPL into DRAM
At the end of this phase, SPL jumps to TPL

### Phase 3 - Tertiary Program Loader
![[Pasted image 20220428231042.png]]
This is a fully fledged bootloader such as U-Boot.
The bootloader loads the kernel image and optionally an initramfs and FDT into the DRAM.
At the end of this phase, the bootloader hands over control to the kernel and removes itself from memory.