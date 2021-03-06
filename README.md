pynrf24
=======

Python port of the RF24 (https://github.com/maniacbug/RF24/) library for NRF24L01+ radios, adapted for the BeagleBone Black, the Banana Pi and the Raspberry Pi.

For more information regarding how to use this library, check the RF24 documentation: http://maniacbug.github.io/RF24/
Most of the information there will also be valid to this library.

Tested in a Banana Pi using spi0 and NRF24L01+ radios.
Should work without problems in a Raspberry Pi or with NRF24L01 (non +) radios.

Do not forget to check this [great tutorial](http://www.diyembedded.com/tutorials/nrf24l01_0/nrf24l01_tutorial_0.pdf)
or the [original datasheet](http://www.nordicsemi.com/eng/Products/2.4GHz-RF/nRF24L01)


Note
----
This library uses a different convention of mac adresses compared to the original library.
The byte order is _NOT_ reversed before sending it to the NRF24L01.

Contact
-------

For any information regarding this library you can open tickets on Github.

Improvements to the code base are also welcome.

Requirements
------------

 * Python 2
 * SPI Enabled on the device: check the spi directory in the repository
 * SPI communication requires spidev:  https://pypi.python.org/pypi/spidev
 * BBB: GPIO access requires Adafruit BBIO library: https://github.com/adafruit/adafruit-beaglebone-io-python
 * BBB: If using GPIO IRQ detection, a custom version of the Adafruit library must be used https://github.com/jpbarraca/adafruit-beaglebone-io-python . A patch was already submitted to Adafruit.


Wiring
------

    nRF24L01+ (top view)     BeagleBoneBlack
	+-+-+                    (header)
	|8|7|	1: GND      ->   P9 GND (P9_1 and P9_2)
	+-+-+	2: 3.3V     ->   P9 3.3v (P9_3 and P9_4)
	|6|5|	3: CE       ->   P9_15 (configurable)
	+-+-+	4: CSN      ->   SPI0.CS (P9_17)
	|4|3|	5: SCKL     ->   SPI0.SCLK (P9_22)
	+-+-+	6: MOSI     ->   SPI0.D1 (P9_18)
	|2|1|	7: MISO     ->   SPI0.D0 (P9_21)
	+-+-+	8: IRQ      ->   P9_16 (configurable)

Examples
--------

Initialization:

    mac = "TEST1" # or mac = [0xe7, 0xe7, 0xe7, 0xe7, 0xe7]
    radio = NRF24(spi=(0,0), ce_pin="P9_15", irq_pin="P9_16")
    radio.data_rate = 2000 #kbps
    radio.crc_length = 2 #bytes
    radio.channel = 0x60
    radio.dbm = 0
    radio.enable_dynamic_payloads()
    # or
    radio.set_auto_ack(1)
    radio.open_writing_pipe(mac)
    radio.open_reading_pipe(0, mac)
    radio.print_details()


Sending Data:

    status = radio.write("HELLO")
    #or
    status = radio.write(['H','E','L','L','O'])


Receiving Data:

    #Wait for data
    pipe = [0]
    while not radio.available(pipe):
        time.sleep(0.01)

    print radio.read(recv_buffer)


Caveats
-------

Performance with this driver (as well as with any other Python "driver") may be lower than expected.
Python is a high level language and there is too much stuff going on (e.g, Garbage Collection)
for timing constrains to be respected. There is no assurance that you can transmit at 2MBits/s,
or even at 250KBits/s. Actually, the hardware can only provide maximum bitrates
when using large packets, no CRC, and without any retransmissions. Which means that radios must
be very close to each other, and in practice you will never get the advertised bitrate. Actually,
maybe Python actually isn't a bottleneck :).

It works great in my case. Use AS-IS, but remember that YMMV.


License
-------

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.

