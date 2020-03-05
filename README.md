ockam-experiments

[[https://github.com/bshambaugh/ockam-experiments/blob/master/photos/7961C683-282C-4BE6-AFAA-E688BE44E2B2.jpeg|alt="Cryptographic Co-Processor - ATECC508A and Heltec Wifi 32 v2"]]

I have 2 "SparkFun Cryptographic Co-Processor Breakout - ATECC508A (Qwiic)" boards.  However I read that I can lock them down irreversibly. This is scary. 
":lock:Warning: Configuration settings are PERMANENT. We would like to stress the fact that these cannot be changed later. If you intend to follow along with this tutorial, then configure with SparkFun standard settings.
If you intend to use different features of the co-processor like the Diffie-Hellman Key Exchange and some of its other use cases, please consider designing your own custom configuration. This hookup guide only focuses on digital signature creation and verification."
https://learn.sparkfun.com/tutorials/cryptographic-co-processor-atecc508a-qwiic-hookup-guide/example-1-configuration

However Ockam’s protocol appears to use ed2559 [0] asymmetric signatures. I am not sure the breakout board will work out of the box like in the Sparkfun tutorial.

https://github.com/ockam-network/did-method-spec --> ed2559 ?? 
https://github.com/ockam-network/proposals/tree/master/design/0003-key-agreement-xx
[0] https://news.ycombinator.com/item?id=16748400

I have proposed the following steps. The Ockam code appears to be set up with a Raspberri Pi.

Step 1:

Get the ESP32 to work with a sensor 

https://github.com/espressif/esp-idf/blob/master/examples/peripherals/i2c/i2c_self_test/main/i2c_example_main.c (BH1750 test)

https://heltec.org/project/wifi-lora-32/
https://heltec-automation-docs.readthedocs.io/en/latest/esp32/wifi_lora_32/hardware_update_log.html#v2-1


Step 2:

Setup MicroPython with ESP32 [1] . 

https://github.com/micropython/micropython-lib
https://docs.micropython.org/en/latest/esp32/quickref.html

[1] https://www.adafruitdaily.com/2018/01/05/exploring-micropython-lib/


Step 3:

Run the config_atecc508a.py script [2]. Make sure to include modules from micropython-lib as well as 
common, ctypes , and cryptoauthlib from the main python library https://pypi.org/ are included

[2] https://github.com/ockam-network/ockam/blob/develop/implementations/c/tools/scripts/config_atecc508a.py 

alternatively something like wolfssl might work: https://www.wolfssl.com/docs/atmel/ .

More info about the crypto authentication chip is here:

Possible places to get Ed25519 with ATECC508A (https://www.microchip.com/wwwproducts/en/ATECC508A):

Step 4: 

Use the gradle build tool to build ockam. Build Ockam on the computer and/or the edge device? 
https://gradle.org/

https://github.com/ockam-network/ockam or?
 https://github.com/ockam-network/ockam/blob/develop/implementations/c/settings.gradle
-.>  https://github.com/ockam-network/ockam/blob/develop/implementations/c/tools/cmake/path.cmake
Store these keys in a vault:
https://github.com/ockam-network/ockam/blob/develop/documentation/concepts/vaults.md

https://github.com/ockam-network/ockam/blob/develop/implementations/c/source/ockam/vault/vault.c
https://github.com/ockam-network/ockam/blob/develop/implementations/c/source/ockam/vault/host/mbedcrypto.c

to::
https://github.com/ockam-network/ockam/blob/develop/implementations/c/source/ockam/vault/tpm/microchip/atecc508a.c


For the algorithm compare:
https://github.com/ockam-network/did-method-spec
https://github.com/ockam-network/proposals/tree/master/design/0003-key-agreement-xx

to learn more:
What is ed25519?
https://cryptobook.nakov.com/digital-signatures/eddsa-and-ed25519

What is a vault?
https://owasp.org/www-project-cheat-sheets/cheatsheets/Key_Management_Cheat_Sheet.html

Using other devices other than the ESP32, may not be easy. Here is what I have:


I own an ardunio uno,  2 heltec cubecells, 2 heltec esp32 + LoRa + OLED boards, and a heltec programming board with a single chip usb to UART bridge:
https://www.silabs.com/documents/public/data-sheets/C2-P2109.pdf
UART communication is different than I2C communication:
https://www.circuitbasics.com/basics-uart-communication/ (no clock)
https://www.circuitbasics.com/basics-of-the-i2c-communication-protocol , https://www.youtube.com/watch?v=6IAkYpmA1DQ
I also have a USB to TTL cable.





