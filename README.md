README:

::: Software Information :::

The embedded system must be uploaded to a pair of ESP32 microcontrollers with keypads and I2C displays, wired as described in the hardware information section. It is not designed to run on a PC.

This system uses only 4 libraries:
	<Wire.h> : Provides I2C communication from the ESP32 to OLED screens
	<Adafruit_GFX.h> : General graphics library for interfacing with OLED screens
	<Adafruit_SSD1306.h> : Hardware-specific driver for the specific screens used in this project
	<math.h> : Provides low level math functions


::: Hardware Information :::

Once uploaded the embedded system needs to be turned on, either by inserting 2 18650 cells and using the ON/OFF switch to activate the system, or by plugging the USB-C connector into a wall adapter. Key generation and UART communication will start automatically. Never turn the battery power on while plugged into a wall outlet

The keypad columns need to be wired to GPIO 26,25,33,32. The keypad rows need to be wired to 13,14,27,23. These pins are neighbors, all in that order, on the ESP32S3. The two screens share pins 21 and 22. The address of one of the screens must be changed by desoldering the address resistor on the back and soldering it to the neighboring pad. These screens are yellow in the top 20 pixels, and blue from vertical pixel 21-60.

NOTE: The batteries both face the same way(series) and the positive lead should point towards the red wire. To change batteries, slide the battery lid to the right (towards Bob), replace the 2 18650 batteries (or charge them), and slide the lid back on. Tuck the battery cable into the casing while reattaching the lid.

Once on, there are 4 screens, 2 for each microcontroller. The screen labeled "Out" displays the public key in yellow, and below in blue are the user buffer (Buff:), encrypted user buffer (En.Tx:), and decrypted user buffer(De.Tx). The decrypted buffer has been encrypted and decrypted using the public key, and should match the user buffer at all times. 

The "In" screen displays the private key (Kpri) in yellow, and is different on every startup based on (p,q). Below that in blue are the encrypted received message (En.Rx), decrypted received message (De.Rx), and N. 

On startup, the user buffer, encrypted received, and decrypted received are all blank because they are not automatically populated, user input populates them.

On the keypad, the numbers 0-9 and letters A-D are all valid inputs. These represent hexadecimal nibbles. Below that, the '*' key is used to clear the user buffer, and the '#' key is enter. These are labeled on the 3D printed casing. The user buffer will automatically clear when a message is sent.

All values that are sent are zero padded. No matter the user input, 8 nibbles, or 4 bytes will be sent, along with a new line '\n', making 5 bytes.

There are 2 messages that will appear as plaintext, even when encrypted, and those are '0000 0000' and '0000 0001'. This is not a bug, but the very nature of RSA encryption, as both of these messages are unchanged when raised to any power.

To turn the system off, simply unplug it from the USB-C or switch the battery power off. 

::: Testing :::

The "De.Tx" value should always match the user buffer. Enter values and make sure these two are the same. 

The "De.Rx" should always match the message sent from the other microcontroller. Send values using the keypad and ensure they are always the same as the message sent. 

Key generation should be automatic. Turn the system on, then remove the cover and hit the "Boot" button on one of the two microcontrollers. The 'Out' screen connected to that microcontroller should display "Key Generating...". This is because it is waiting for the public key to be sent, and since the other microcontroller already sent its key, it will never receive one. Power cycle the system to stop the waiting behavior.


 
