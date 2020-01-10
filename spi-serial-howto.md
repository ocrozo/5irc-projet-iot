# Guide de démarrage SPI / UART BBB

Attention, cette guide est valable que sur la version **Debian 8.6** (image initialement proposée : [beaglebone-debian-8.6-iot-armhf-2016-12-09-4gb.img](https://debian.beagleboard.org/images/bone-debian-8.6-iot-armhf-2016-12-09-4gb.img.xz])), il faut :
1. Désactiver le HDMI qui utilise les mêmes pins que SPI.
2. Activer le SPI
3. Activer les ports série UART

Pour cela, éditer le fichier `/boot/uEnv.txt` pour n'y garder que les lignes suivantes:
```
cat /boot/uEnv.txt
...
cmdline=coherent_pool=1M quiet net.ifnames=0 cape_universal=enable
#DISABLE HDMI AND ENABLE SPI
cape_disable=bone_capemgr.disable_partno=BB-BONELT-HDMI,BB-BONELT-HDMIN
cape_enable=bone_capemgr.enable_partno=BB-SPIDEV0,BB-SPIDEV1
#ENABLE UART
cape_enable=capemgr.enable_partno=BB-UART1,BB-UART2,BB-UART4.BB-UART5
...
```
et redémarrez votre BBB

## Vérifications :

1. Vérifier que les slots sont identifiés:
```
root@beaglebone:/sys/devices/platform/bone_capemgr# less slots
 0: PF----  -1 
 1: PF----  -1 
 2: PF----  -1 
 3: PF----  -1 
 4: P-O-L-   0 Override Board Name,00A0,Override Manuf,BB-SPIDEV0
 5: P-O-L-   1 Override Board Name,00A0,Override Manuf,BB-SPIDEV1
 6: P-O-L-   2 Override Board Name,00A0,Override Manuf,BB-UART1
 8: P-O-L-   3 Override Board Name,00A0,Override Manuf,BB-UART4
 9: P-O-L-   4 Override Board Name,00A0,Override Manuf,BB-UART5
```
2. Vérifiez que les bus SPI sont identifiés:
```
root@beaglebone:/dev# ls spidev*
spidev1.0  spidev1.1  spidev2.0  spidev2.1
root@beaglebone:/dev# ls i2c-*
i2c-0  i2c-2
root@beaglebone:/dev# ls ttyO*
/dev/ttyO0  /dev/ttyO1  /dev/ttyO4  /dev/ttyO5
```