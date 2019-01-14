---
layout: post
title: An Introduction to Atmel Arm Cortex M4: ATSAM4S II
subtitle: Primer Programa
summary: En este segundo post vamos a estudiar como compilar nuestro primer programa.
---
# Hello Arm World!!!
Este va a ser nuestro programa de ejemplo. Por lo general cuando empezamos a programar nuestro primer programa suele ser un HelloWorld!!!

En el caso de los sistemas empotrados, no disponemos de una pantalla donde mostrar este mensajito. Así que, vamos a empear haciendo un programa loop infinito.


Nos preparamos nuestro directorio habitual de trabajo:
{% highlight Bash %}
mkdir -p ex0/src/system/start/
cd ex0
touch Makefile
touch src/system/start/ex0.cpp
{% endhighlight %}

## El código fuente
Editamos nuestro fichero ex0.cpp con nuestro editor favorito y añadimos el contenido:
{% highlight C %}

extern "C" 
void exit(int v)
{
    while (true) {}
}

int main(int argc, const char** argv)
{
    static_cast<void>(argc);
    static_cast<void>(argv);

    while (true) {};
    return 0;
}

{% endhighlight %}

## Compilando
Compilamos el codigo fuente del programa y generamos el codigo objeto.

]$ arm-none-eabi-g++ -mcpu=cortex-m4 -O2 -std=c++11 -I. -c src/sys/start/ex0.cpp -o bin/ex0.o

A linker script is required to get all the generated objects successfully linked. It states what code/data sections need to be loaded at what addresses as well as defines several symbols that may be required by the sources. 

Siguiendo nuestra politica de echos consumados, y el clasico ya lo arreglare si falla, vamos a lanzar el linkador a ver que ocurre, usando linker script por defector, y stdlib y todo lo que por defecto traiga!!!

Linkamos
]$arm-none-eabi-g++ -mmcu=cortex-m4 bin/ex0.o -o bin/ex0.elf

Esto parece un **Win!!!** 

Bueno, dado que todo ha dio tan tremendamente bien, vamos a emplear unos minutos en analizar nuestro HelloWorld!! 

En primer lugar miranmos la ocupación. 

]$arm-none-eabi-size bin/ex0.elf 
   text	   data	    bss	    dec	    hex	filename
    520	     12	     28	    560	    230	bin/ex0.elf

Si miramos el datasheet de SAM4S, veremos que para el SAM4SD32 dispone de 2 x 1024 Kbytes de memoria flash y 160 Kbytes de SRAM. Sintamonos orgullos de estar dentro de los limite. __Bueno nuestro programa no hace nada, tampoco es para estar orgullosos.__

]$ arm-none-eabi-nm bin/ex0.elf 
Esta aplicación nos dice los nombre de las funciones dentro de nuestro ejecutable.

00008204 r 
00018230 B __bss_end__
00018230 B _bss_end__
00018214 B __bss_start
00018214 B __bss_start__
00018214 b completed.6485
00018210 D __data_start
00008010 t deregister_tm_clones


##Start up Code
Otra parte fundamental del programa es el código de arranque. No basta con ensamblar un puñado de instrucciones y lanzarlas a la memoria del procesador esperando que todo funcione. No chicos, esto es el __Bare Metal World!!!__

Toda aplicación debe incluir un codigo de arranque, generalmente escrito en ensamblador, que se encargue de inicializar el hardware de nuestro sistema.
- Escribir la tabla de interrupciones en la zona de la memoria habilitada para ellos.
- Establecer el puntero de la pila para cada modo de ejecución.
- Poner a 0 la seccion .bss
- Llamar al constructor de objetos globales (static)
- Llamar a la función main.

It may happen that compiler generates some startup code for you, especially if you haven't excluded standard library (stdlib) from compilation. To check whether this is the case, we need to analyse assembler listing of the successfully compiled and linked image binary. All the generated files for a test application will reside in <build_dir>/src/test_cpp/<app_name>. The assembler listing file will have kernel.list name.

Desemsamblamos, esto nos fenera el codigo ensamblador de nuestra aplicación.
arm-none-eabi-objdump -D -S bin/ex0.elf > ex0.list

Y todo este código? Si yo solo habia puesto
while(1){}

## El linker script
A linker script is required to get all the generated objects successfully linked. It states what code/data sections need to be loaded at what addresses as well as defines several symbols that may be required by the sources. Here is a good manual of linker script syntax and here is the linker script I use to get applications linked for Raspberry Pi platform.

## Compilando con custom Linker script
Opciones de compiladción de gnu arm.

https://gcc.gnu.org/onlinedocs/gcc/ARM-Options.html


##Escribir las Flash
BOSSA, esta utilidad parece bastante usada...
http://microchipdeveloper.com/atmelice:connecting-atmel-ice-to-a-swd-target

SWD and JTAG are hardware interfaces between a debugger/programmer "box" (loosely speaking) and an ARM chip.   They provide a standard method for the debugger to talk to various registers inside the ARM CORE.

But an important thing to understand is that Flash memory is NOT part of the ARM core, and neither SWD/JTAG nor CMSIS-DAP really understand anything about how to load stuff into flash to program your chip.  That's because the flash is controlled by vendor-proprietary "stuff" (on SAMD20, it'd be the NVMCTRL "peripheral.")  Probably, this means that the PC debugger (OpenOCD, for example) has to have intimate knowledge of each chip that you might want to program, so that it can manipulate the NVMCTRL registers as if they were memory, eventually causing the right things to happen (SWD/JTAG does give you the ability to read and write memory locations in the address space.)  

dmesg 

[84386.174972] usb 1-5: New USB device found, idVendor=03eb, idProduct=2111
[84386.174982] usb 1-5: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[84386.174988] usb 1-5: Product: EDBG CMSIS-DAP
[84386.174994] usb 1-5: Manufacturer: Atmel Corp.
[84386.175000] usb 1-5: SerialNumber: ATML1803051800003045

En la web de soporte de microchip nos dicen 
https://www.microchip.com/webdoc/protocoldocs/ch01s02.html
el hw id 0x2111 se corresponde con un edbg.

EDBG	0x2111	

| Tool | USB PID | Supports | Notes |
|------|---------|----------|--------|
| EDBG | 0x2111  | Only the Atmel device mounted on that board  CDC Virtual COM port and Atmel Data Gateway Interface support varies between different kits | As used on Xplained Pro boards |

Ahora ya sabemos que hw tenemos, como lo programamos?

https://www.microchip.com/webdoc/GUID-93FB6B89-BAAC-4787-B2DF-8B196201F396/index.html?GUID-1BF79242-98EF-4A6D-933C-E58121E59635

Tela de docu, pero parece ser que la mejor soluciones es emplear edbg.

sudo yum install systemd-devel
git clone https://github.com/ataradov/edbg
cd edbg
make all

edbg -b -t list
Nos muestra las tarjetas soportadas.

sudo ../../resources/edbg/edbg -bpv -t atmel_cm4 -f bin/ex0.bin

Debugger: ATMEL EDBG CMSIS-DAP ATML1803051800003045 01.1A.00FB (S)
Clock frequency: 16.0 MHz
Target: SAM4SD32C (Rev A)
Programming............,.................................................................................................................................. done.
Verification..................................................................................................................................... done.

Y listo!!
