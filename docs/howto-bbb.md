# BBB : PINS, capes, buses

**Source** : _[“Exploring Beaglebone” 2nd edition, Derek Molloy](https://www.wiley.com/en-cu/Exploring+BeagleBone%3A+Tools+and+Techniques+for+Building+with+Embedded+Linux%2C+2nd+Edition-p-9781119533153)_

## Chapter 6  
>It is a surprisingly complex topic that will take some time to get used to, particularly the content on the Linux device tree and pin multiplexing.

>P8 and P9 headers. Many of these pins are multiplexed, meaning they have many more functions than what is displayed in the figure, under 'name'- default operation.
NLE: figure evolved, now very clear, pin has 3 denominations: 
Pin / Name / GPIO. Ex: P9_12 / GPIO1_28 / 60

## NLE: prepare nRF

Slot 1: 
- pin CE P9_12 / GPIO1_28 / 60;
- pin CSN P9_28 / SPI1_CS0 / 113

Slot 2: 
- pin CE P9_23 / GPIO1_17 / 49;
- pin CSN P9_42 / … / GPIO0_7 / 3_18 (7 / 114)

Slot 3:
- pin CE P8_14 / GPIO0_26 / 26;
- pin CSN P9_17 / I2C1_SCL / 5

Slot 4: 
- pin CE P8_16 / GPIO1_14 / 46;
- pin CSN P8_10 / TIMER6 / 68;


How to interface to the expansion header pins in the following ways:
Digital output: how you can use a GPIO to turn an electrical circuit on or off.
- The example uses an LED, but the principles hold true for any circuit type.
- A circuit is given to draw less current from the GPIO.
- A C++ class is developed to make software interfacing straightforward and efficient.
Digital input: read in a digital output from an electrical circuit into a software application running under Linux.
- Circuits are provided to ensure that this is performed safely for your board.
- At the end of the chapter, **more advanced digital input that allows for efficient detection of an input state change is also discussed**.
Analog input: from an electrical circuit in the range of 0 V to 1.8 V using one of the Beagle board’s on-board analog-to-digital converters (ADCs).
Analog output: use PWM to output a proportional signal that can be used as an analog voltage level or as a control signal for certain types of devices, such as servo motors.

Required : Chapter 4 – in particular, switching circuits using FETs and the use of pull-up/down resistors.

GPIO Digital Output
Beagle board GPIOs are 3.3V tolerant, and you can only source 4–6 mA and sink about 8mA to each pin.

Build a circuit:
- GPIO connected to a FET to switch
- when a voltage is applied to the gate input of a FET, it will close the virtual drain-source “switch,” enabling current to flow from the 5 V supply through the 220 Ω current-limiting resistor and then to GND through a
- lighting LED

There is no resistor on the gate of the FET. It is not necessary in this particular case because an internal pull-down resistor is enabled within the BeagleBone by default, on this pin P9_28 (GPIO 113).

Once the circuit is built and attached to the Beagle board, you can boot the board and control the LED using a Linux terminal.

**THE BEAGLE BOARD CONFIG-PIN TOOL **
There is a dash script tool available **for the latest BeagleBoard Linux images that allows you to create mappings between header pins and kernel GPIOs at run time**. The tool has usage as described in Figure 6-4. 

Each pin has a default (reset) mode that is defined by the boot configuration of the device tree model.
??? NLE: what is that ?

List the valid modes:
$ config-pin -l p9.12
default gpio gpio_pu gpio_pd gpio_input

Show info for that pin:  
```
$ config-pin -i p9.12
```
```
Pin name: P9_12  
Function if no cape loaded: gpio  
**Function if cape loaded: default gpio gpio_pu gpio_pd gpio_input**  
Function information: gpio1_28 default gpio1_28 gpio1_28 gpio1_28 gpio1_28  
Kernel GPIO id: 60  
PRU GPIO id: 92  
```
Query the pin and report the default configuration mode.
```
config-pin -q p9.12
P9_12 Mode: default Direction: in Value: 0
```
The AM335x processor has four banks or chips (0–3) of 32 GPIOs numbered 0 to 31.
Thus, GPIO1_28 is GPIO 28 of 32 possible (0–31) on the second (number 1) GPIO chip of four (0–3).  
**Thus, the internal GPIO number corresponding to pin GPIO1_28 is calculated as follows: (1×32) + 28 = 60.**

The total range is GPIO 0 (i.e., GPIO0_0) to GPIO 127 (i.e., GPIO3_31), but not all AM335x GPIOs are available on the headers P9 and P8 on Beaglebone.

To use GPIO 60 as an output, use the following commands at the Linux shell prompt
```
$ config-pin -a p9.12 out
$ config-pin -q p9.12 
P9_12 Mode: default Direction: out Value: 0
```
Go to the GPIO sysfs directory to control the pin directly.  
The `/sys/class/gpio/` directory lists all the available GPIOs along with the four GPIO banks (also named chips) (labeled 0, 32, 64, and 96). Others are either not connected or are used for other functions.
```
# cat /sys/class/gpio/gpio
gpio10/     gpio12/     gpio3/      gpio39/     gpio51/     gpio7/      gpio79/     gpiochip32/
gpio11/     gpio13/     gpio30/     gpio4/      gpio60/     gpio70/     gpio8/      gpiochip64/
gpio110/    gpio14/     gpio31/     gpio44/     gpio61/     gpio71/     gpio80/     gpiochip96/
gpio111/    gpio15/     gpio32/     gpio45/     gpio62/     gpio72/     gpio81/     
gpio112/    gpio2/      gpio33/     gpio46/     gpio63/     gpio73/     gpio86/     
gpio113/    gpio20/     gpio34/     gpio47/     gpio65/     gpio74/     gpio87/     
gpio114/    gpio22/     gpio35/     gpio48/     gpio66/     gpio75/     gpio88/     
gpio115/    gpio23/     gpio36/     gpio49/     gpio67/     gpio76/     gpio89/     
gpio116/    gpio26/     gpio37/     gpio5/      gpio68/     gpio77/     gpio9/      
gpio117/    gpio27/     gpio38/     gpio50/     gpio69/     gpio78/     gpiochip0/ 

/sys/class/gpio$ cd gpio60 
/sys/class/gpio/gpio60$ ls -l
cat label
P9_12
cat direction
in
```
NB: the input/output direction of a pin can be set using 
- equivalently by using the config-pin tool, or
- the **GPIO sysfs** entry (see below)
Here’s an example:
```
/sys/class/gpio/gpio60$ cat direction
in
/sys/class/gpio/gpio60$ echo out > direction
/sys/class/gpio/gpio60$ cat direction

out

/sys/class/gpio/gpio60$ config-pin -a p2.08 out
/sys/class/gpio/gpio60$ cat direction
out
```
## GPIO sysfs
Linux GPIO support includes the ability to export GPIO control and status for use with applications using sysfs. No other driver can be using the GPIO of interest. 
Simple tools like 'cat' and 'echo' can be used to quickly read the current value of a GPIO input or to set the level of a GPIO output.

#### Enable GPIO from application space
In summary : export it first by writing in the export file, which creates a folder containing files that can be altered to control the pin’s state. On the command line, type the following. In this example, GPIO 60 is made available (exported) to application space.  
Ex: pin P9_12 / GPIO1_28 / 60. Connect the led(+) to it, and to the GND.

1. Map the pin into the filesytem, that is export it, echoing the GPIO pin into the export file.
```
root@beaglebone:/sys/class/gpio# echo 60 > export
root@beaglebone:/sys/class/gpio# ls
export	**gpio60**	gpiochip0  gpiochip32  gpiochip64  gpiochip96  unexport
root@beaglebone:/sys/class/gpio# cd gpio60
root@beaglebone:/sys/class/gpio/gpio60# ls -l
total 0
-rw-rw-r-- 1 root gpio 4096 Jan  9 01:05 active_low
lrwxrwxrwx 1 root gpio    0 Jan  9 01:05 device -> ../../../4804c000.gpio
-rw-rw-r-- 1 root gpio 4096 Jan  9 01:05 direction
-rw-rw-r-- 1 root gpio 4096 Jan  9 01:05 edge
drwxrwxr-x 2 root gpio    0 Jan  9 01:05 power
lrwxrwxrwx 1 root gpio    0 Jan  9 01:05 subsystem -> ../../../../../../class/gpio
-rw-rw-r-- 1 root gpio 4096 Jan  9 01:05 uevent
-rw-rw-r-- 1 root gpio 4096 Jan  9 01:05 value

root@beaglebone:/sys/class/gpio/gpio60# echo out > direction
root@beaglebone:/sys/class/gpio/gpio60# echo 1 > value
```
LED should be lit.
```
root@beaglebone:/sys/class/gpio/gpio60# echo 0 > value
```
LED should be download

Also, 
```
root@beaglebone:/sys/class/gpio/gpio60# echo high > direction
```
LED is lit

**Question : How to know if the pin is mapped / exported ? Does it mean that the GPIO pins are already mapped, that is exported by Universal cape IO ?**  
A : the pin's directory should appear in /sys/class/gpio/gpioNN

Now your LED should be on.

To turn it off, use the following command:
```
root@beaglebone:/sys/class/gpio/gpio40# echo 0 > value
```
NLE: OK worked for me on slots 1, 2, 4, but not 3 on P9.22 SCK...

When you’re done with a pin, it’s often a good idea to unexport it so that it becomes available for different purposes. You unexport the pin by writing into the unexport file. The following succession of commands unexports gpio60 and shows that its directory has been eliminated.
```
root@beaglebone:/sys/class/gpio# echo 60 > unexport
```
NLE: 
On the latest image bone-debian-9.5-iot-armhf-2018-08-30-4gb 
- all of the pins are exported
- LED was simply lit by one single command:
```
$ cd /sys/class/gpio/gpio60
$ echo high > direction
```
That's all !

Remarks:
- The 'top' command (executed in another Linux terminal) indicates that the CPU load for this script is 98 percent.
- the current driving the LED is 12 mA, which is large enough to damage a Beagle board if this current were directly sourced from, or sinked to, a GPIO itself.
- A C++ class is presented later in this chapter that can be used to control a GPIO

## GPIO Digital Input
Enables software written on the board to read the state of a push button or any other logic high/ low input. This task is first performed using a Linux terminal, and then it is performed using C/C++ code.

NB: You will notice that, having discussed the need for pull-up or pull-down resistors on push button switches in Chapter 4, none are present in this circuit. This is not accidental, as **we have control over internal pull-up and pull-down resistors on the Beagle boards using the config-pin tool.**


### GPIO Pin Configuration Settings
There are seven different modes for each pin. This is called the multiplexer mode (mmode) for the pin, and it is set using 3 LSB bits, thus 7 values.  
**The total 3+4 = 7 bits value can be used to configure the exact behavior of a GPIO pin within Linux using device tree overlays, which is discussed shortly.**

The mode is discussed shortly, but **when working with GPIOs, the mmode will be 7 (111)_b, LSB**. The most common hexadecimal values that are used to configure a GPIO’s settings are as follows (bit 6 is the most significant bit [MSB] and is on the LHS):
- 0x27 (0100111) Fast, Input, Pull-Down, Enabled and Mux Mode 7
- 0x37 (0110111) Fast, Input, Pull-Up, Enabled, Mux Mode 7
- 0x07 (0000111) Fast, Output, Pull-down, Enabled, Mux Mode 7
- 0x17 (0010111) Fast, Output, Pull-up, Enabled, Mux Mode 7
- 0x2F (0101111) Fast, Input, Pull-down, Disabled, Mux Mode 7

These configuration values are useful, as they enable you to set the pins into the exact operational mode that you require. Use jointly with P8, P9 complete pin tables BBB_SRM. Reading it:
- Any pin that is highlighted in the $PINS column is already allocated and should not be used unless you disable the functionality that it is allocated to (e.g., the HDMI output or the eMMC). See the Notes column for further details.
-  **Mode7 is the GPIO mmode ???**
- **As discussed, the GPIO number is calculated by taking the GPIO chip number, multiplying it by 32, and then adding the offset. For example, GPIO1_12 = (1 × 32) + 12 = GPIO 44.**
- ***The $PINS number is not the GPIO number. This $PINS number in the table is a software reference for each of the pins, and these values are provided in the second column of the table.**

**The fact that the name of the pin is the same as its Mode7 value** means that Mode7 is most likely enabled by default.

The script show-pins.pl (on the PocketBeagle !!!)
```
debian@ebb:~$ cd /opt/scripts/device/
debian@ebb:/opt/scripts/device
$ ls

blue bone x15
debian@ebb:/opt/scripts/device$ cd bone
debian@ebb:/opt/scripts/device/bone$ perl show-pins.pl | grep 1.28
perl show-pins.pl | grep 9.12
P9.12   **30** U18 fast rx down 7 gpio 1.28
	ocp/P9_12_pinmux (pinmux_P9_12_default_pin)
```
**Thus, $PIN of P9.12 (GPIO1_28 = 60) is equal to 30**

Another test in Linux follows (you must use a superuser account):
```
$ sudo -i
#cd /sys/kernel/debug/pinctrl/44e10800.pinmux/ 
# ls
gpio-ranges pingroups pinmux-functions pinmux-pins pins
# cat pins | grep 30
pin 30 (PIN30) 44e10878 00000027 pinctrl-single
```

***The pin number is the $PINS value of the pin in question.**

??? NLE: what does this exactly mean ?... That this should be used in C, Python, etc ?

Please note that these values will change as you use the config-pin tool.
You can actually query the value at the memory address itself using C code that accesses /dev/mem directly.
Because P9_12 ($PINS30) is mapped at the memory address 44e10878 (see the ADDR column), you can use the following steps to install Jan-Derk Bakker’s devmem2 program (as mentioned in Chapter 3):
```
~$ wget http://www.lartmaker.nl/lartware/port/devmem2.c
~$ gcc devmem2.c -o devmem2
~$ sudo ./devmem2
0x44e10838
/dev/mem opened.
Memory mapped at address 0xb6fc6000.
Value at address 0x44E10838 (0xb6fc6838): 0x27
```
The value 0x27 is expected for $PINS30. You can investigate the source code of devmem2 to see how it can be integrated into your projects.

## C++ Control of GPIOs

A C++ class has been written for this book that wraps the GPIO functionality on the Beagle boards to make GPIOs easier to use.


## The Linux Device Tree

A Linux running on embedded platform does not have a BIOS. To boot and configure the device, the Linux needs files on SD (or MMC) that describe the hardware.

## Boot Configuration Files

Earlier Linux kernel versions on the BeagleBone used a kernel overlay “slots” framework (1 st ed) to allow for the dynamic reconfiguration of hardware, which was necessary when capes were plugged into a Beagle board.

The capes identified themselves using identification and version codes that were stored in an on-board EEPROM. The kernel **cape manager** would use this information to search the /lib/firmware/ directory for the correct overlay.

NB : Kernel overlays are currently being phased out by the BeagleBoard.org development team and are being replaced by U-Boot overlays.
**
Currently, you have control over the configuration of hardware at boot time using the /boot/uEnv.txt confguration file.**

Currently, this configuration file allows you to
- Revert to the old kernel overlay “slots” model by commenting out the line
enable_uboot_overlays=1 with a #. May be RISKY, but also the last resort !
- Disable on-board devices (e.g., the eMMC, HDMI, on-board Wi-Fi, and the ADCs) by uncommenting the associated line (e.g., disable_ u b o o t _ o v e r l a y _ a d c =1) .
- Provide support for a custom overlay by specifying it as follows:
dtb_ overlay=/lib/firmware/<custom>.dtbo. The /lib/firmware/ directory provides a repository of overlays.

Interfacing Beaglebone with buses (I2C, SPI, UART)

https://elinux.org/BeagleBone_Black_Enable_SPIDEV

## SPI on the Beagle Boards
The tables of pinout identify that the SPI buses are accessible from the GPIO headers. A short summary here:
```
CE
CS
Device
MikroBUS slot
SPIDEV0
P8.14
P9.17
/spi/dev1.0
3
SPIDEV0
NA
NA
/spi/dev1.1
NA
SPIDEV1
P9.12
P9.28
/spi/dev2.0 & /spi/dev2.1
1
SPIDEV1
P9.23
P9.42
/spi/dev2.0 & /spi/dev2.1
2
SPIDEV1
P8.16
P8.10
/spi/dev2.0 & /spi/dev2.1
4
```
We notice that for the bus SPI n2, that is SPIDEV1, the 2 devices are available on 3 different slots 1,2 and 4. Thus, only 2 out of those 3 slots can be used with SPI at the same time.

**NB: Please note that SPI0 is not available by default on the BeagleBone and therefore must be enabled.**

To test the SPI bus, you can use a program called “spidev_test.c” that is available from www.kernel.org.  
**BUT !!! This is no more compatible with Beaglebone. Take the older version from chapter 8 companion code for Exploring Beaglebone, D. Molloy.**

If this code is executed without connecting to the SPI1 pins, then the output displayed by the spidev_test program will consist of a block of 0x00 or 0xFF values, depending on whether the MISO pin is configured in pull-down or pull-up configuration, respectively.

Ex: pin P9.29 / $PINS 101 / GPIO3_15 = 111
```
root@beaglebone:/sys/kernel/debug/pinctrl/44e10800.pinmux# cat pins | grep 111
pin 111 (PIN111) 44e109bc 00000028 pinctrl-single
```
It's in mode 28, thus not in GPIO mode ! (would be 27). Reserved for SPI1_D0.

With the MISO pin configured in pull-up, spidev_test should return :
```
$ ./spidev_test -D /dev/spidev2.0
spi mode: 0
bits per word: 8

max speed: 500000 Hz (500 KHz)

FF FF FF FF FF FF
FF FF FF FF FF FF
FF FF FF FF FF FF
FF FF FF FF FF FF
FF FF FF FF FF FF
FF FF FF FF FF FF
FF FF
```

https://dev.iachieved.it/iachievedit/adventures-in-beaglebone-black-device-overlays-and-spi/

