# TOP-PIZERO Snowdive board v0

This board is confirmed to function standalone. It also is designed (but currently untested) to function connected to a bottom board, for instance, the BTM-PALMTOP.

## Requirements

This board works from a 5V/1.5A or 5V/3A input. If you want to get USB host mode out of the, you can use many of the generic "USB-C with data output and charger input" docks; connect the charger first, then plug the USB-C into the board.

You can still use the Pi Zero HDMI port for setup if required for OS setup.

You might benefit from a USB-UART adapter for debugging the system, though it's not required. A USB-UART adapter can be connected into the expansion header. Remember to check config.txt and cmdline.txt to make sure that UART console is enabled.

## Assembly

### FIXES REQUIRED FOR V0

- Remove resistor R6 (next to I2C GPIO expander U27 in the section `I2C GPIO a`),
- Add a ~51K resistor soldered to the VSYS side of C51 in `modem buck` section (can reuse the removed R6 for this)m
- Magnet wire the added resistor's other pin to pin 6 (EN) of U17 MIC2876, signal `EN_5V_BOOST`, section `5V boost`,
- Remove neopixels LED1 and LED2 at the top of the board (unless you've ensured that they're assembled correctly by JLCPCB, which has a likelihood of 50%).
- Desolder the TVS diode D5 in the `LTE modem` section if it's found out to be shorting the modem VBAT `+3V5` rail (possible if it was assembled incorrectly by JLCPCB, which has a likelihood of 50%).

### After-PCBA steps

- If assembled without a modem, solder on a modem as desired (not required)
- If assembled single-sided, solder on a SIM slot and an expansion header as desired (not required)
- Solder the Pi Zero on flush onto the board. Use a headers-less Pi Zero.
- Solder the Pi Zero USB testpoints to the solder points under the board using thin wires (i.e. magnet wire or stranded wire). Check for shorts to GND (in particular the microUSB shield pads) after soldering.

### After-assembly preparations

None.

## Bringup

### Testing under Linux

Make sure to run raspi-config to enable I2C, and that `i2c-dev` is added into `/etc/modules`.

### USB subsystem

**Before attempting to work with data on the USB-C port, slide the switch right next to it away from the USB-C port. This will disengage the hardware switch on the USB data pins.**

By default, USB from the Pi Zero is routed directly to the USB-C socket. This allows you to use the USB gadget mode (for networking/serial/storage over USB), as well as the BCM bootloader mode (entered upon boot without an SD card inserted).

If you want to enable the onboard USB hub (including the modem USB downlink and two of the downlinks exposed to a bottom board), you have to run these console commands:

```bash
i2cset -y 1 0x24 7 0xaf; i2cset -y 1 0x24 3 0xef
```

The `i2c-tools` package is a prerequisite for this - you can get it by using `sudo apt install i2c-tools`.

The USB-C socket will then get connected through a USB hub, instead of being connected directly.

Note: This command can also be executed when using a network connection over USB (i.e. USB-Ethernet), but do expect that you might need to open a new SSH session, as the old session might freeze.

*Next revision will join these pins into a single GPIO, so the command for V1 will change.*

### Modem

To power up the modem `+3V5` power rail, issue this command:

```bash
i2cset -y 1 0x25 7 0xef
```

The `i2c-tools` package is a prerequisite for this - you can get it by using `sudo apt install i2c-tools`.

## Notes

- I recommend ordering PCBA (PCB with assembly) for this board.
- The board is prepared for PCBA ordering, use the [kicad-jlcpcb-tools](https://github.com/Bouni/kicad-jlcpcb-tools/) plugin to generate output files.
- I recommend a stencil for assembling this board.

## Info

- Software: Kicad 9
- Version: 0
- PCB size: 111x80
- Layer count: 2
- Recommended thickness: 1.6mm
