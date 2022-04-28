# Toolchains
## Anatomy of a toolchain
### Contents of a toolchain
1. binutils -> linker + assembler + etc.
2. C compiler -> compiles C to assembly
3. C Library -> API for interfacing with the OS

### 1. binutils
ld - linker
as - assembler
gdb
ar - archiver to create static library files

### 3. C Library
1. libc - the standard C library (printf, etc) always linked by default by gcc
2. libm - math library
3. libpthread - posix threads implementation
4. librt - async I/O, shared memory, etc

to link libraries 2 to 4, pass -l{library name without lib}

### What's a toolchain for tho ?
Compiling your program code into executables for running on the target device.


---

## Types of Compilers
### Native compiler
Here the compiler runs on the same hardware that it produces code for.

### Cross
Here the compiler runs on the host hardware (which is normally faster) and compiles code for a (not necessarily) different hardware architecture of the target.

### So which one might we prefer ?
Cross. Even if the arch is same on host and target, having isolated environments for both helps in reducing potential problems.

### Toolchain naming convention
Tuple of
1. CPU arch - ARM / RISC and little / big endian (el / eb)
2. Vendor - provider of the toolchain (buildroot, poky, unknown)
3. Kernel - linux / windows
4. OS - userspace / c library component, ex, GNU / musl and the ABI used (eabihf)

ex: x86-unknown-linux-gnu

---

## CPU Architectures
### Differentiating components of a CPU
1. CPU Arch - ARM / x86 / RISC / etc.
2. Little / big endian - MSB order
3. Floating point support - hardware support for FP ops
3. Application Binary Interface - how function params are passed, where return values stored, etc

---

## Obtaining Your Toolchain
3 options -
1. Build it yourself, from scratch - cumbersome, but fully configurable
2. Get a readymade one from a vendor - easy, but not configurable
3. Use one provided by build tools like yocto - good mix

### 1. Building From Scratch Process
1. **PASS 1** Build binutils with host gcc
2. **PASS 1** Build minimal gcc with core c (just libc)
3. **PASS 1** Build glibc (entire c library - threads, libm, etc)
4. **PASS 2** Build full binutils
5. **PASS 2** Build full gcc

---

## Types of Linking
### Static Linking
Here, the code and functions of a library used by a program are pulled from that library and included in the program code.
Use this when the target system is minimal and may not have the requisite libraries.
Extension: `.a`

>**Howto link** `gcc -static `
>**Howto ** create static lib `ar rc {output_name} {object_files,}`

### Dynamic Linking
Only a reference to the library is included in the program code. This requires a runtime linker (ld) that resolves the reference.
Extension: `.so`

>**Howto** create dynamic lib `gcc -fPic -c {c_files}; gcc -shared -o \ {output_name} {object_files,}`

### Typical files in a library's folder
- lib{xx}.a - static library file
- lib{xx}.so -> lib{xx}.so.X.X.X - symbolic link, used by gcc for dynamic linking the library
- lib{xx}.so.X -> lib{xx}.so.X.X.X - symbolic link used by the runtime linker
- lib{xx}.so.X.X.X - actual shared library

---

# Cross Compiling
## Build Systems
### Automake
3 machines are understood by Automake
1. Build - the machine that builds the package
2. Host - the machine that the package runs on (blank for native / toolchain tuple for cross compiling)
3. Target - USED ONLY WHEN BUILDING COMPILERS