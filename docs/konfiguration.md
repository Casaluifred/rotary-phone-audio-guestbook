# Konfigurationshandbuch für das Audio-Gästebuch

In dieser Anleitung wird erläutert, wie das Audio Guestbook-System konfiguriert wird, dass es mit Deiner spezifischen Hardware funktioniert, und wie Du es für Deine Veranstaltung anpassen kannst.

## Checkliste für die Ersteinrichtung

- [ ] Lade eine selbsterstellte Begrüßungsmeldung hoch.
- [ ] Stelle die richtige ALSA-Hardwarezuordnung für Dein Audio-Interface ein
- [ ] Konfiguriere die GPIO-Pins für Deine Gabelkontakt
- [ ] Teste Audiowiedergabe und -aufnahme
- [ ] Überprüfe, ob der Systemdienst ordnungsgemäß ausgeführt wird

## Audio Interfacekonfiguration

### ALSA Hardware Mapping

Abhängig von der Konfiguration Deines Audio-Interfaces musst Du möglicherweise die ALSA-Einstellungen anpassen. Identifiziere zunächst Deine Audiogeräte, indem Du Folgendes ausführst:

```bash
aplay -l
```

Es werden alle verfügbaren Soundkarten und digitalen Audiogeräte aufgelistet. Suche nach Deinem USB-Audio-Interface und notiere die Karten- und Gerätenummer.
Wenn Dein Audio-Interface nicht korrekt erkannt wird, musst Du möglicherweise die Datei „.asoundrc“ in Deinem Home-Verzeichnis anpassen. 
Wenn Dein USB-Audio-Interface beispielsweise als Karte 1, Gerät 0 aufgeführt ist, erstelle oder bearbeite die Datei „.asoundrc“, um Folgendes hinzuzufügen:

```bash
pcm.!default {
    type hw
    card 1
}

ctl.!default {
    type hw
    card 1
}
```

### Audio Format Einstellungen

In `config.yaml`, you can customize audio format settings:

- `alsa_hw_mapping`: The ALSA hardware mapping for your audio interface (e.g., "plughw:1,0")
- `mixer_control_name`: The mixer control name to use for volume adjustment (e.g., "Speaker")
- `format`: Audio format to use (default is "cd" - see `aplay --help` for other options)
- `file_type`: Output file format (default is "wav")
- `channels`: Number of audio channels (2 for stereo, 1 for mono)
- `sample_rate`: Recording sample rate in Hz (default is 44100)

## GPIO Konfiguration

Das Audio Gäsetbuch verwendet GPIO-Pins, um zu erkennen, wann der Telefonhörer abgehoben oder aufgelegt wird, sowie für optionale Tasten.

### Gabelkontakt Konfiguration

Der Gabelumschalter ist das wichtigste Konfigurationselement, da er bestimmt, wann die Aufzeichnung beginnt und endet::

- `hook_gpio`: Die GPIO-Pin-Nummer, die mit dem Gabelumschalter des Telefons verbunden ist
- `hook_type`: Stelle es auf „NC“ (normalerweise geschlossen) oder „NO“ (normalerweise offen), abhängig vom Gabelumschaltertyp Deines Telefons.
- `invert_hook`: Auf „true“ setzen, wenn Dein Telefon sich umgekehrt verhält (die Aufnahme beginnt, wenn der Hörer aufgelegt ist, statt abgenommen).)
- `hook_bounce_time`: Entprellzeit in Sekunden, um Fehlauslösungen zu verhindern (normalerweise 0,1)

Informationen zur GPIO-Pin-Zuordnung findest Du im Schaltplan Deines Raspberry Pi-Modells.:

![Raspberry Pi GPIO Pinout](../images/rpi_GPIO.png)

### Grundlegendes zu Schalter-Typen

- **NC (Normally Closed)**: Wenn der Hörer aufgelegt ist (ruht), ist der Stromkreis GESCHLOSSEN. Beim Abheben wird der Stromkreis GEÖFFNET.
- **NO (Normally Open)**: Wenn der Hörer aufgelegt ist (ruht), ist der Stromkreis GEÖFFNET. Beim Abheben wird der Stromkreis GESCHLOSSEN.

Die meisten älteren Wählscheibentelefone verwenden NC-Schalter, aber einige Modelle unterscheiden sich. 
Wenn Du unsicher bist, kannst Du mit einem Multimeter testen oder beide Einstellungen ausprobieren..

### Fehlerbehebung bei der Gabel-Konfiguration

Wenn Du Probleme mit dem Gabel-Verhalten hast:

1. Identifiziere zunächst den Schaltertyp (NO oder NC).:

   - NC (Normally Closed): Der Schalter schließt den Stromkreis, wenn er nicht gedrückt ist
   - NO (Normalerweise offen): Der Schalter schließt den Stromkreis, wenn er gedrückt wird

2. Stelle den `hook_type` entsprechend dem physischen Schalter-Typ ein

3. Wenn das Verhalten immer noch umgekehrt ist (z. B. Aufnahme, wenn der Hörer aufgelegt ist), setze `invert_hook: true`

4. Wenn der Gabelkontakt unregelmäßig ausgelöst wird, erhöhe die „hook_bounce_time“ von 0,1 auf 0,2 oder 0,3 Sekunden

5. Starte den Dienst nach jeder Konfigurationsänderung neu: `sudo systemctl restart audioGuestBook.service`

### Häufige Probleme mit dem Gabelkontakt

- **System reagiert nicht auf das Abheben des Hörers**: Überprüfe die GPIO-Pin-Nummer und die Kabelverbindungen
- **Die Aufnahme beginnt, wenn der Hörer aufgelegt wird**: Setze `invert_hook: true` in config.yaml
- **Mehrere Aufnahmen werden beim Abheben/Auflegen des Hörers ausgelöst**: Erhöhe `hook_bounce_time` auf 0,2 oder 0,3
- **Systemabstürze bei schnellen Gabelkontakt-Betätigungen**: Update auf die neueste Softwareversion, die Race Condition Handling beinhaltet

### Andere GPIO-Einstellungen

- `record_greeting_gpio`: GPIO-Pin für einen Schlater zum Aufzeichnen einer neuen Begrüßung (zum Deaktivieren auf 0 setzen)
- `record_greeting_type`: „NC“ oder „NO“ für den Schalter „Ansage aufnehmen“
- `record_greeting_bounce_time`: Entprellzeit für die Begrüßungstaste
- `shutdown_gpio`: GPIO-Pin für einen Schalter zum Herunterfahren (zum Deaktivieren auf 0 setzen)
- `shutdown_button_hold_time`: Zeit in Sekunden, die die Ausschalttaste gedrückt gehalten werden muss (Standard ist 2)

## Audiodatei-Konfiguration

### Begrüßungsnachricht

- `greeting`: Pfad zur Begrüßungs-Audiodatei
- `greeting_volume`: Lautstärke der Begrüßung (0,0 bis 1,0)
- `greeting_start_delay`: Verzögerung in Sekunden vor dem Abspielen der Begrüßung

#### Aufzeichnen einer benutzerdefinierten Begrüßung

So zeichnest Du eine benutzerdefinierte Begrüßung auf:

1. Schließe ein Mikrofon an den Raspberry Pi an
2. Wenn Du einen Schalter „Record_greeting“ angeschlossen und konfiguriert hast, halte diesen gedrückt, um Deine Nachricht aufzuzeichnen
3. Alternativ kann die Begrüßung auf einem anderen Gerät aufgenommen und die WAV-Datei nach `/home/admin/rotary-phone-audio-guestbook/sounds/greeting.wav` kopiert werden.

### Piepton

- `beep`: Pfad zur Signalton-Audiodatei
- `beep_volume`: Lautstärke für den Signalton
- `beep_start_delay`: Verzögerung in Sekunden vor dem Abspielen des Signaltons
- `beep_include_in_message`: Ob der Signalton in die aufgezeichnete Nachricht aufgenommen werden soll (true oder false)

### Meldung „Zeit überschritten“

- `time_exceeded`: Pfad zur Audiodatei mit der Zeitüberschreitung
- `time_exceeded_volume`: Lautstärke der Meldung „Zeitüberschreitung“

## Aufnahmeeinstellungen

- `recordings_path`: Verzeichnis, in dem Aufnahmen gespeichert werden
- `recording_limit`: Maximale Aufnahmelänge in Sekunden
- `time_exceeded_length`: Zeit in Sekunden, nach der die Meldung „Zeitüberschreitung“ abgespielt wird
- 
## Systemdienst

Der audioGuestBook.service stellt sicher, dass die Anwendung beim Systemstart automatisch ausgeführt wird.

Mit diesen Befehlen können Sie den Dienst steuern:

```bash
# Starten des Dienstes
sudo systemctl start audioGuestBook.service

# Beenden Sie den Dienst
sudo systemctl stop audioGuestBook.service

# Starten Sie den Dienst neu (nach Konfigurationsänderungen)
sudo systemctl restart audioGuestBook.service

# Servicestatus prüfen
sudo systemctl status audioGuestBook.service

# Dienstprotokolle anzeigen
journalctl -u audioGuestBook.service
```

### Beheben von Dienstproblemen

Wenn der Dienst nicht gestartet werden kann:

1. Überprüfe die Protokolle auf Fehler:

   ```bash
   journalctl -u audioGuestBook.service -n 50 --no-pager
   ```

2. Überprüfe, ob config.yaml richtig formatiert ist:

   ```bash
   cd /home/admin/rotary-phone-audio-guestbook
   python3 -c "import yaml; yaml.safe_load(open('config.yaml'))"
   ```

3. Überprüfe die Berechtigungen für Audiogeräte:
   ```bash
   ls -la /dev/snd/
   sudo usermod -a -G audio admin
   ```

## Vollständiges Config.yaml-Beispiel

```yaml
alsa_hw_mapping: plughw:1,0
mixer_control_name: Speaker # look at amixer scontrols for available controls
format: cd # look at aplay --help for available formats
file_type: wav
channels: 2
hook_gpio: 22
hook_type: NO # or 'NC' depending on your hardware configuration
invert_hook: true # Set to true if your hook behavior is reversed (recording starts when handset is down instead of up)
# Software bounce compensation this is the length of time (in seconds) that the component will ignore changes in state after an initial change.
hook_bounce_time: 0.1 # float or None
recording_limit: 300
sample_rate: 44100

# Record greeting message button (Set to 0 to skip setup of this feature)
record_greeting_gpio: 23
record_greeting_type: NC # or 'NO' depending on your hardware configuration
# Software bounce compensation this is the length of time (in seconds) that the component will ignore changes in state after an initial change.
record_greeting_bounce_time: 0.1 # float or None

# The beep sound is played when the user picks up the phone
beep: /home/admin/rotary-phone-audio-guestbook/sounds/beep.wav
beep_volume: 1.0
beep_start_delay: 0.0
beep_include_in_message: true

# The greeting sound is played when the user picks up the phone
greeting: /home/admin/rotary-phone-audio-guestbook/sounds/greeting.wav
greeting_volume: 1.0
greeting_start_delay: 1.5

# The time_exceeded sound is played when the user has been recording for too long
time_exceeded: /home/admin/rotary-phone-audio-guestbook/sounds/time_exceeded.wav
time_exceeded_volume: 1.0

recordings_path: /home/admin/rotary-phone-audio-guestbook/recordings
time_exceeded_length: 300

shutdown_gpio: 0 #Set GPIO pin here --> Note: Pin is active LOW, pull Pin to GND to activate shutdown
shutdown_button_hold_time: 2 # default 2 seconds
```

## Testing Your Configuration

After making changes to your configuration:

1. Restart the service: `sudo systemctl restart audioGuestBook.service`
2. Check the logs for any errors: `journalctl -u audioGuestBook.service -f`
3. Test audio playback:

   ```bash
   aplay -D plughw:1,0 /path/to/test.wav
   ```

4. Test audio recording:

   ```bash
   arecord -D plughw:1,0 -f cd -d 5 test.wav
   aplay -D plughw:1,0 test.wav
   ```

5. Test the phone hook by lifting and replacing the handset

## Webinterface

Du kannst das Audio-Gästebuch auch über die Weboberfläche konfigurieren, die eine benutzerfreundliche Möglichkeit bietet, Einstellungen zu ändern und Aufnahmen zu verwalten.

Greife auf die Weboberfläche zu, indem Du in einem Webbrowser die IP-Adresse und der Port 8080 des Raspberry Pi eingibst: `http://your_raspberry_pi_ip:8080`

Änderungen sollten automatisch über die Weboberfläche angewendet werden. Wenn dies nicht der Fall ist, kannst Du den systemctl-Dienst manuell neu starten..

## Leistungsoptimierung

Wenn das Gästebuch für eine längere Veranstaltung verwendet werden soll, beachte diese Tipps:

1. **Überwachen des Speicherplatzes**: Aufnahmen können sich schnell ansammeln. Überprüfe den verfügbaren Speicherplatz mit:

   ```bash
   df -h
   ```

2. **Automatische Bereinigung aktivieren**: Wenn Speicherplatz ein Problem darstellt, kannst Du einen Cron-Job einrichten, um ältere Aufzeichnungen zu archivieren oder zu löschen.

3. **Kühlung**: Sorge für ausreichende Belüftung des Raspberry Pi, insbesondere wenn die Veranstaltung in einer warmen Umgebung stattfindet.

## Sicherung und Wiederherstellung

Aufnahmen sichern:

1. Verwende die Weboberfläche, um Aufzeichnungen herunterzuladen
2. Oder verwende `scp`, um Aufnahmen auf einen anderen Computer zu kopieren:

   ```bash
   scp -r admin@your_raspberry_pi_ip:/home/admin/rotary-phone-audio-guestbook/recordings /path/to/backup
   ```

3. Sichern der benutzerdefinierten Konfiguration:
   
   ```bash
   scp admin@your_raspberry_pi_ip:/home/admin/rotary-phone-audio-guestbook/config.yaml /path/to/backup
   ```
