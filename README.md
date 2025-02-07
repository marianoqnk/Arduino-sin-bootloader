# Arduino-sin-bootloader
Un tutorial para trabajar con la placa de arduino sin utilizar bootloadre
EN primer lugar deberemos disponer de un programador. Se puede utilizr otro Arduino para hacerlo pero por lo que cuesta un programador, mejor comprarlo.
Tenemos varios en Aliexpress:
- [ISP UsbBas ](https://a.aliexpress.com/_EufJKal)
- [ISP otro UsbBas](https://a.aliexpress.com/_EHAlgaH)

  Para que funcionen tenemos que instalar los driver. Se hace con el programa Zitel. [Tutorial](https://www.fischl.de/usbasp/)
Los que me han funcionado a mi son los WinUSB

  Una vez tenemos estos ya estamos casi listos.
  Información de configuración de platformio:
  https://github.com/MCUdude/MiniCore/blob/master/PlatformIO.md

  Tamaño de los boot de Arduino:
  -Arduino UNO 512 bits
  -Arduino Nano 2K hay dos bootloader en nuevo y el viejo la diferencias son la velocidad de comunicación
  - Fichero con las carácteristicas que usa en cada placa [file](https://github.com/arduino/ArduinoCore-avr/blob/master/boards.txt#L152)

### Configuración de Platformio para trabajar sin bootloader o cambiarlo.
```
platform = atmelavr
;board = nanoatmega328new
board = ATmega328P
framework = arduino
```
Cambiamos Board por ATmega328P de esta forma selecciona el minicore.  
La configración de board con [Minicore](https://github.com/MCUdude/MiniCore?tab=readme-ov-file), carga un nucleo llamado minicore que tiene algunas caracteristicas distintas:
- Utiliza el bootloader [Urboot](https://github.com/stefanrueger/urboot/)  Que tiene detección automática de bootloader.
- Soporta Serial.printf("Milliseconds since start: %ld\n", millis()); aunque no imprime float, se puede seleccionar en el compilador o utilizar la funcion dtostr(): Supuestamente haciendo esto funciona build_flags = -D PIO_FRAMEWORK_ARDUINO_NANOLIB_FLOAT_PRINTF



La configuración de Arduino original  carga el core de Arduino, y el bootloader optiboot
EN el caso del nano 328P hay dos versiones la antigua que abre el puerto serie a 57600 y la nueva que lo abre a 115000
Ambas ocupan un bootloader de 2048 no se porque.
```
  "bootloader": {
    "efuse": "0xFD",
    "file": "optiboot/optiboot_atmega328.hex",
    "hfuse": "0xDA",
    "lock_bits": "0x0F",
    "lfuse": "0xFF",
    "unlock_bits": "0x3F"
  },
```



Para ver la memoria libre:
```
int freeRam () 
{
  extern int __heap_start, *__brkval; 
  int v; 
  return (int) &v - (__brkval == 0 ? (int) &__heap_start : (int) __brkval); 

}
```

La configuración de cada placa podemos verla en:

c:\Users\MC.5014877\.platformio\packages\framework-arduino-avr\
  
## FUSES
[Calculadora de fusibles](https://eleccelerator.com/fusecalc/fusecalc.php?chip=atmega328p)
En Atmega328P tenemos dos fusibles para reservar la parte de memoria correspondiente al bootloader.
Podemos seleccionar 256,512,1024 y 2048.

Arduino Uno  
Low Fuse	0xFF  
High Fuse	0xDE  
Extended Fuse	0x05  

Arduino Mega 2560  
Low Fuse	0xFF  
High Fuse	0xD8  
Extended Fuse	0xFD  

Arduino Mega (ATmega1280)  
Low Fuse	0xFF  
High Fuse	0xDA  
Extended Fuse	0xF5  

Arduino Mini  
Low Fuse	0xFF  
High Fuse	0xDD  
Extended Fuse	0x00  


Arduino Pro or Pro Mini (5V, 16 MHz) w/ ATmega328     
Low Fuse	0xFF  
High Fuse	0xDA  
Extended Fuse	0x05  

Arduino Pro or Pro Mini (5V, 16 MHz) w/ ATmega168  
Low Fuse	0xFF  
High Fuse	0xDD  
Extended Fuse	0x00  

Arduino Pro or Pro Mini (3.3V, 8 MHz) w/ ATmega328  
Low Fuse	0xFF  
High Fuse	0xDA  
Extended Fuse	0x05  

Arduino Pro or Pro Mini (3.3V, 8 MHz) w/ ATmega168  
Low Fuse	0xC6  
High Fuse	0xDD  
Extended Fuse	0x00  


##EEPROM
