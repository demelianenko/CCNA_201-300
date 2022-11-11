# Chapter 4 CCNA 200-301

## default console port

###  when using any remote connection

- 9600 bits/second
- no hardware  / no flow control
- 8-bit ASCII
- no parity bits
- 1 stop bit

##  Switch  configuration Files Location

- RAM/DRAM -  is the active memory in the switch, its used like any  other computer. the active config is stored in RAM.
- Flash Memory - stores the IOS images for the switch, its is a chip or a flash card. Could be used to save backup files and or config files.
- ROM/ReadOnlyMemory - Store the bootstrap program that when booting will find the iso and boot the switch image from flash in to ram.
- NVRAM - stores the switch config file.

| **RAM/DRAM** | **Working Memory (like normal ram)** Running config |
| ------------ | --------------------------------------------------- |
| **Flash**    | **IOS image** **(Operating system image)**          |
| **ROM**      | **Boot strap program**                              |
| **NVRAM**    | **Startup config**                                  |

## Config manipulation

One of the following can be used to delete the config and "Factory reset the switch"

```
write erase
erase startup-config
erase nvram
```

