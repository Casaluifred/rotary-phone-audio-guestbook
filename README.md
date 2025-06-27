# Wählscheibentelefon Audio Gästebuch

Dieses Projekt verwandelt ein Wählscheibentelefon in einen Sprachrekorder für besondere Anlässe, beispielsweise ein Audio-Gästebuch für Hochzeiten.

![image](images/final_result_2.jpg)

- [Rotary Phone Audio Guestbook](#rotary-phone-audio-guestbook)
  - [Background](#background)
  - [Material](#materials)
  - [Einrichtung](#einrichtung)
    - [Telefon vorbereiten](#telefon-vorbereiten)
    - [Herunterladen und Installieren des spezifischen Raspberry-Images](#herunterladen-und-installieren-des-spezifischen-raspberry-images)
    - [Ersteinrichtung](#ersteinrichtung)
    - [Bei mehreren SSID anmelden](#bei-mehreren-SSID-anmelden)
  - [Software](#software)
  - [Entwickliung](#entwicklung)
  - [Support](#support)
  - [Star History](#star-history)

## Hintergrund

Inspiriert von der bevorstehenden Hochzeit von Freunden wollte ich mit Hilfe eines Wählscheibentelefons ein selbstgemachtes Audio-Gästebuch bauen. 
Eine gewerbliche Lösung stand nie zur Debatte, ich mag das gern selber zusammen bauen.
Gefunden habe ich dann letztendlich eine Lösung von playfultechnology auf Basis des Teensy +.
Die Hardware war schnell fertig, bei der Software  musste ich viel probieren, bis es endlich funktioniert hat.
Während das Ganze beim ersten Einsatz problemlos funktioniert hat, habe ich es nich für eine weitere Hochzeit zur Verfügung gestellt.
Hier gab es dann immer wieder Probleme mit dem Abspielen der Ansagen und dem Aufzeichnen der Gästenachrichten.

Eigentlich dachte ich, ich versuche es komplett selber zu entwickeln, dann fand ich die Lösung von nickpourazima.
Nachdem mir der Raspberry besser liegt als der Arduino habe ich seine Lösung als Basis genommen und auf meine eigenen Anforderungen umgebaut.
Ich habe bewusst auf Deutsch dokumentiert, um auch die abzuholen, die mit Einglisch nicht so gut zurecht kommen.

## [Material](docs/materials.md)

## Einrichtung

### Telefon vorbereitetn

1. Im Abschnitt [Hardware](docs/hardware.md) findest Du detaillierte Anweisungen zum Anschluss des Wählscheibentelefons an den Raspberry Pi.

_Note: Dies ist ein wichtiger erster Schritt, bevor Du mit der Softwareinstallation fortfahren kannst._

### Herunterladen und Installieren des spezifischen Raspberry-Images 

Wenn die Hardware vorbereitet ist, installiere das benutzerdefinierte Raspberry Pi-Image, das die gesamte erforderliche Software enthält:

1. Lade die aktuellste Version herunter (https://github.com/nickpourazima/rotary-phone-audio-guestbook/releases)
2. Entpacke das .gz file: `gunzip rpizero_rotary_phone_audio_guestbook_v<latest>_imagebackup.img.gz`
3. Flashe das image auf eine SD-Karte (z.B. mnit dem 'Raspberry Pi Imager' oder 'BalenaEtcher':

   <img src="images/rpi_imager_custom.png" width="50%" height="50%">

4. Trage die folgenden Anmeldeinformationen ein, wenn die Aufforderung kommt:
   ```bash
   username: admin
   password: password
   ```
5. Richte Die WLAN-Netzwerkverbindung ein
6. Stecke die SD-Karte in den Raspberry Pi und verninde ihn mit der Spannungsversorgung.

### Ersteinrichtung

Sobald die Hardware-Einrichtung abgeschlossen und das Image installiert ist:

1. Starte den Raspberry Pi und warte eine Minute, bis er initialisiert ist.
2. Gebe die Ip-Adresse des Raspi gefolgt vom Port 8000 `<RPI_IP>:8080` in der Adresszeile Deines Browsers ein, um auf das Webinterface zu gelangen:

   ![image](images/webserver_home_light_no_recordings.png)

3. Klicke auf die Seite „Einstellungen“, um die Konfiguration anzupassen:

   ![image](images/webserver_settings_light.png)

Das Audio-Gästebuch ist nun bereit zum Testen/Einsatz! Erweiterte Konfigurationsmöglichkeiten und detaillierte Erklärungen aller Einstellungen findest Du in der [Configuration](docs/configuration.md) Dokumentation.

### Bei mehreren SSID anmelden
Da ich mehrere Netzwerke betreibe, möchte ich mich auch an mehreren WLAN anmelden können. Zum Beispiel zu Hause oder (auf der Hochzeit) über den mitgeführten Hotspot.
Um einen Raspberry Pi Zero W automatisch mit mehreren SSIDs zu verbinden, kann man die Datei /etc/wpa_supplicant/wpa_supplicant.conf bearbeiten und mehrere Netzwerke in der Konfiguration definieren. 
Der Pi versucht dann, sich der Reihe nach mit den angegebenen Netzwerken zu verbinden, bis eine erfolgreiche Verbindung hergestellt ist.
Hier ist eine detaillierte Anleitung:

1. Zugriff auf die Konfigurationsdatei:
Öffne die Datei wpa_supplicant.conf mit einem Texteditor, z.B.
```bash
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf.
```

3. Konfiguration der Netzwerke:
Füge für jedes Netzwerk, mit dem sich der Pi verbinden soll, einen Block mit den folgenden Informationen hinzu:
```bash
network={
    ssid="SSID_1"
    psk="Passwort_fuer_SSID_1"
}
network={
    ssid="SSID_2"
    psk="Passwort_fuer_SSID_2"
}
```

Ersetze SSID_1, Passwort_fuer_SSID_1, SSID_2 und Passwort_fuer_SSID_2 mit den tatsächlichen Werten für Deine Netzwerke.
Füge so viele network Blöcke hinzu, wie benötigt werden.
Es ist empfehlenswert, die gewünschte Reihenfolge der Netzwerke durch die Reihenfolge der Blöcke in der Datei zu bestimmen.

Speichern und Schließen:

Speichere die Änderungen und schließe den Editor.

Neustart oder Netzwerk neustarten:

Starte den Raspberry Pi neu, oder führe den Befehl sudo systemctl daemon-reload gefolgt von sudo systemctl restart wpa_supplicant aus, um die Änderungen zu aktivieren. 
Nun versucht der Raspberry Pi, sich automatisch mit den konfigurierten Netzwerken zu verbinden, beginnend mit dem ersten Netzwerk in der Liste.
Wenn die Verbindung fehlschlägt, versucht er das nächste Netzwerk usw.

 
## [Software](docs/software.md)

## [Entwicklung](docs/development.md)

## Unterstützung

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
