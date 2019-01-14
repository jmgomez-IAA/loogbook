---
layout: post
title: An Introduction to Atmel Arm Cortex M4: ATSAM4S
summary: This introduction to the ATSAM4S series of Microchip arm microcontrollers will help you get started programming without using ASF. 
---
# Compiladores

En general existen dos versiones de compiladores, las cuales se diferencian por su campo de aplicación. 

1.[GNU Toolchain for Embedded Processors](https://developer.arm.com/open-source/gnu-toolchain/gnu-rm) 
- Bare-metal development
- Support for Arm Cortex-R and Cortex-M families
- GCC, binutils, GDB and newlib
- Freely available from Arm
- Community support
- GNU Toolchain for Embedded Processors

2.[GNU Toolchain for Cortex-A Family by Linaro] (http://www.linaro.org/downloads/) 
- Application and Linux kernel development
- Support for Arm Cortex-A family
- Monthly updates to QEMU, GDB, and various versions of GCC
- Freely available from Linaro
- Community support

No debemos emplear el GNU ARM Embedded Processor para construir aplicaciones GNU/Linux, por que los ejecutable no funcionaran en otro sitio que en bare metal. Tampoco debemos emplear Linaro GNU for Cortex-A toolchain para realizar aplicacioens bare metal.

# Instalar compiladores
Por ahora, vamos a centrar nuestro trabajo en el mundo Bare Metal. Por lo tanto, debemos instalarnos GNU Toolchain for Embedded Processors. Vamos a emplear compiladores pre-construidos por facilidad, y existen dos opciones de instalación:
- [GNU MCU Eclipse Family](https://gnu-mcu-eclipse.github.io/toolchain/arm/install/)
- [ARM Developer](https://developer.arm.com/open-source/gnu-toolchain)

##GNU MCU Eclipse ARM Embedded GCC 
The GNU/Linux versions of GNU MCU Eclipse ARM Embedded GCC are packed as .tgz archives. Download the latest version named like:

    gnu-mcu-eclipse-arm-none-eabi-gcc-7.2.1-1.1-20180401-0515-centos64.tgz
    gnu-mcu-eclipse-arm-none-eabi-gcc-7.2.1-1.1-20180401-0515-centos32.tgz

As the name implies, the binaries were created on CentOS, but can be executed on most recent GNU/Linux distributions (they were tested on Debian, Ubuntu, Manjaro, SuSE and Fedora). Select the -centos64 file for 64-bit machines and the -centos32 file for 32-bit machines.

To install the toolchain, unpack the archive and copy it to /${HOME}/opt/gnu-mcu-eclipse/arm-none-eabi-gcc/:

$ mkdir -p "${HOME}"/opt
$ cd "${HOME}"/opt
$ tar xf ~/Downloads/gnu-mcu-eclipse-arm-none-eabi-gcc-7.2.1-1.1-20180401-0515-debian64.tgz
$ chmod -R -w "${HOME}"/opt/gnu-mcu-eclipse/arm-none-eabi-gcc/7.2.1-1.1-20180401-0515

    Note: although perfectly possible to install the toolchain in any folder, it is highly recommended to use this path, since the GNU MCU Eclipse plug-ins can automatically identify the toolchain if installed in this location.

Test if the compiler is functional; use the actual install path:

$ "${HOME}"/opt/gnu-mcu-eclipse/arm-none-eabi-gcc/7.2.1-1.1-20180401-0515/bin/arm-none-eabi-gcc --version
arm-none-eabi-gcc (GNU MCU Eclipse ARM Embedded GCC, 64-bit)


Incluimos en el path nuestro compilador GCC
export PATH=${PATH}:/srv/share/Workspace/ARM/resources/gnu-mcu-eclipse/arm-none-eabi-gcc/7.2.1-1.1-20180401-0515/bin

## GNU ARM Embedded Toolchain

Podemos descargar una imagen pre-compilada o bien crear la nuestra a partir del código fuente:
https://developer.arm.com/open-source/gnu-toolchain/gnu-rm


https://developer.arm.com/open-source/gnu-toolchain/gnu-rm/downloads

tar xjf gcc-arm-none-eabi-7-2017-q4-major-linux.tar.bz2


## GNU ARM Microchip Embedded Toolchain
No deja de ser una version de las GNU.

http://www.microchip.com/mplab/avr-support/avr-and-arm-toolchains-(c-compilers)


http://ww1.microchip.com/downloads/en/DeviceDoc/arm-gnu-toolchain-5.3.1.487-readme.pdf


