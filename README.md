ockam-experiments

Picture of hardware
[[https://github.com/bshambaugh/ockam-experiments/blob/master/photos/7961C683-282C-4BE6-AFAA-E688BE44E2B2.jpeg]]

I have 2 "SparkFun Cryptographic Co-Processor Breakout - ATECC508A (Qwiic)" boards.  However I read that I can lock them down irreversibly. This is scary. 
":lock:Warning: Configuration settings are PERMANENT. We would like to stress the fact that these cannot be changed later. If you intend to follow along with this tutorial, then configure with SparkFun standard settings.
If you intend to use different features of the co-processor like the Diffie-Hellman Key Exchange and some of its other use cases, please consider designing your own custom configuration. This hookup guide only focuses on digital signature creation and verification."
https://learn.sparkfun.com/tutorials/cryptographic-co-processor-atecc508a-qwiic-hookup-guide/example-1-configuration

"The ATECC508A is capable of many cryptographic processes. This tutorial focuses on Elliptic-curve cryptographic digital signatures (ECC) - using asymetric private/public keys. If those last few words are new to you, please read on! We will spend some time explaining in a bit."
https://learn.sparkfun.com/tutorials/cryptographic-co-processor-atecc508a-qwiic-hookup-guide/

paraphrasing the video in the tutorial: "The ATECC508A creates a digital signature which allows the data and digital signature to be sent, verifing the sender."
"It is using the private key to create the signature, that can only be found on the chip" -- presumably the private key on the chip operates on the message to create the signature
"your signature will change because even though we are using the same private key on the same chunk of data, there is a bit of randomness provided by elliptic curve cryptography"
"sign some data using the private key and verify it using the public key"
https://www.youtube.com/watch?v=_MjjF211BM8

tangent  ""
So, who are alice and bob? 
https://medium.com/asecuritysite-when-bob-met-alice/how-does-trent-send-a-cipher-message-to-bob-alice-and-carol-and-not-know-what-reads-the-message-13b221ed38fc
If I have two entities doing ping pong[-1] do I need a different private key for each entity?
https://medium.com/asecuritysite-when-bob-met-alice/how-does-trent-send-a-cipher-message-to-bob-alice-and-carol-and-not-know-what-reads-the-message-13b221ed38fc
[-1] https://github.com/HelTecAutomation/Heltec_ESP32/blob/master/examples/Factory_Test/WiFi_LoRa_32FactoryTest/WiFi_LoRa_32FactoryTest.ino
oh, dear nuts. Wheb Bob Met Alice Who Met Carol: https://medium.com/asecuritysite-when-bob-met-alice/when-bob-met-alice-who-met-carol-27f36cc19d39

In the alice and bob example in the sparkfun tutorial (http://www.youtube.com/watch?v=_MjjF211BM8&t=10m48s), I have a second coprocessor attached to a 2nd ardunio (the first coprocessor and ardunio is with alice) using
alice's public key. Since ESP32 are more like raspberri pis could I just store alice's public key on the ESP32 rather than a second coprocessor. I imagine that the coprocessor needs to be
with the ardunio since the atmega328P (or whatever similar it happens to be) does not do a lot beyond being a good microcontroller. not a lot of memory or whatever.
"" end tangent

However Ockam’s protocol appears to use ed2559 [0] asymmetric signatures. I am not sure the breakout board will work out of the box like in the Sparkfun tutorial.

From [0]

	Asymmetric signatures	[2]		NaCl			NaCl
						Ed25519			Ed25519
						RFC6979

https://github.com/ockam-network/did-method-spec --> ed2559 ?? 
https://github.com/ockam-network/proposals/tree/master/design/0003-key-agreement-xx
[0] https://news.ycombinator.com/item?id=16748400

The ATECC508A sends the 

perhaps visit:
https://searchsecurity.techtarget.com/definition/asymmetric-cryptography

"To create a digital signature, signing software -- such as an email program -- creates a one-way hash of the electronic data to be signed. The user's private key is then used to encrypt the hash, returning a value that is unique to the hashed data. The encrypted hash, along with other information such as the hashing algorithm, forms the digital signature. Any change in the data, even to a single bit, results in a different hash value.

This attribute enables others to validate the integrity of the data by using the signer's public key to decrypt the hash. If the decrypted hash matches a second computed hash of the same data, it proves that the data hasn't changed since it was signed. If the two hashes don't match, the data has either been tampered with in some way -- indicating a failure of integrity -- or the signature was created with a private key that doesn't correspond to the public key presented by the signer -- indicating a failure of authentication."

I have proposed the following steps. The Ockam code appears to be set up with a Raspberri Pi.

Step 1:

Get the ESP32 to work with a sensor 

https://github.com/espressif/esp-idf/blob/master/examples/peripherals/i2c/i2c_self_test/main/i2c_example_main.c (BH1750 test)

https://heltec.org/project/wifi-lora-32/
https://heltec-automation-docs.readthedocs.io/en/latest/esp32/wifi_lora_32/hardware_update_log.html#v2-1
https://github.com/MicrochipTech/cryptoauthlib/issues/5
https://docs.espressif.com/projects/esp-idf/en/latest/api-reference/peripherals/i2c.html


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

"The Vault interface exposes functions for generating and using cryptographic keys, secure random numbers, key derivation, hashing, message authentication, digital signatures, Diffie-Hellman key exchange, authenticated encryption etc."

From (https://github.com/ockam-network/ockam/blob/develop/documentation/concepts/vaults.md)

Using other devices other than the ESP32, may not be easy. Here is what I have:


I own an ardunio uno,  2 heltec cubecells, 2 heltec esp32 + LoRa + OLED boards, and a heltec programming board with a single chip usb to UART bridge:
https://www.silabs.com/documents/public/data-sheets/C2-P2109.pdf
https://heltec-automation-docs.readthedocs.io/en/latest/cubecell/htcc-ac01/capsule_quick_start.html
UART communication is different than I2C communication:
https://www.circuitbasics.com/basics-uart-communication/ (no clock)
https://www.circuitbasics.com/basics-of-the-i2c-communication-protocol , https://www.youtube.com/watch?v=6IAkYpmA1DQ
I also have a USB to TTL cable.





