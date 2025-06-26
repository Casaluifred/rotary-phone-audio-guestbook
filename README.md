# Rotary Phone Audio Guestbook

Dieses Projekt verwandelt ein Wählscheibentelefon in einen Sprachrekorder für besondere Anlässe, beispielsweise ein Audio-Gästebuch für Hochzeiten.

![image](images/final_result_2.jpg)

- [Rotary Phone Audio Guestbook](#rotary-phone-audio-guestbook)
  - [Background](#background)
  - [Materials](#materials)
  - [Setup](#setup)
    - [Prepare Your Rotary Phone](#prepare-your-rotary-phone)
    - [Download and Install the Custom Image](#download-and-install-the-custom-image)
    - [Initial Configuration](#initial-configuration)
  - [Software](#software)
  - [Development](#development)
  - [Support](#support)
  - [Star History](#star-history)

## Background

Inspiriert von der bevorstehenden Hochzeit von Freunden wollte ich mit Hilfe eines Wählscheibentelefons ein selbstgemachtes Audio-Gästebuch bauen. 
Eine gewerbliche Lösung stand nie zur Debatte, ich mag das gern selber zusammen bauen.
Gefunden habe ich dann letztendlich eine Lösung von playfultechnology auf Basis des Teensy +.
Die Hardware war schnell fertig, bei der Software  musste ich viel probieren, bis es endlich funktioniert hat.
Während das Ganze beim ersten Einsatz problemlos funktioniert hat, habe ich es nich für eine weitere Hochzeit zur Verfügung gestellt.
Hier gab es dann immer wieder Probleme mit dem Abspielen der Ansagen und dem Aufzeichnen der Gästenachrichten.

Eigentlich dachte ich, ich versuche es komplett selber zu entwickeln, dann fand ich die Lösung von nickpourazima.
Nachdem mir der Raspberry besser liegt als der Arduino habe ich seine Lösung als Basis genommen und auf meine eigenen Anforderungen umgebaut.
Ich habe bewusst auf Deutsch dokumentiert, um auch die abzuholen, die mit Einglisch nicht so gut zurecht kommen.

## [Materials](docs/materials.md)

## Setup

### Prepare Your Rotary Phone

1. Follow the [Hardware](docs/hardware.md) section for detailed instructions on wiring your rotary phone to the Raspberry Pi.

_Note: This is a crucial first step before proceeding to software installation._

### Download and Install the Custom Image

With your hardware prepared, install the custom Raspberry Pi image that contains all necessary software:

1. Download the [latest release](https://github.com/nickpourazima/rotary-phone-audio-guestbook/releases)
2. Extract the .gz file: `gunzip rpizero_rotary_phone_audio_guestbook_v<latest>_imagebackup.img.gz`
3. Flash the image to an SD card using Raspberry Pi Imager or BalenaEtcher:

   <img src="images/rpi_imager_custom.png" width="50%" height="50%">

4. Configure the following credentials when prompted:
   ```bash
   username: admin
   password: password
   ```
5. Set up your WiFi network connection
6. Insert the SD card into your Raspberry Pi and power it on

### Initial Configuration

Once you've completed the hardware setup and installed the image:

1. Boot up your Raspberry Pi and allow it a minute to initialize
2. Navigate to `<RPI_IP>:8080` in a web browser to access the control interface:

   ![image](images/webserver_home_light_no_recordings.png)

3. Visit the Settings page to customize your configuration:

   ![image](images/webserver_settings_light.png)

Your audio guest book is now ready for test/deployment! For advanced configuration options and detailed explanations of all settings, refer to the [Configuration](docs/configuration.md) documentation.

## [Software](docs/software.md)

## [Development](docs/development.md)

## Support

It's great to see this project growing. Special thanks to @svartis, @jmdevita, and @Mevel!

If this code helped you or if you have feedback, I'd be happy to [hear about it](mailto:dillpicholas@duck.com)!
Feel like saying thanks? You can [buy me a coffee](https://ko-fi.com/dillpicholas)☕.

## Star History

<a href="https://star-history.com/#nickpourazima/rotary-phone-audio-guestbook&Date">
 <picture>
   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/svg?repos=nickpourazima/rotary-phone-audio-guestbook&type=Date&theme=dark" />
   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/svg?repos=nickpourazima/rotary-phone-audio-guestbook&type=Date" />
   <img alt="Star History Chart" src="https://api.star-history.com/svg?repos=nickpourazima/rotary-phone-audio-guestbook&type=Date" />
 </picture>
</a>
