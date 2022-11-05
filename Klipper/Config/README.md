# FlyingBear Reborn 2 - Klipper Config

Die Installation und Konfiguration findet ihr auf meinem Youtube-Kanal

### Link: https://www.youtube.com/channel/UCdu7f90jyes9eE86PLhS_YQ


## RaspberryPi Updaten
```
sudo apt update
sudo apt upgrade
```
## Kiauh Clonen
```
git clone https://github.com/th33xitus/kiauh.git
```
Kiauh ausführen und Mainsail, Moonraker und Klipper deinstallieren
```
./kiauh/kiauh.sh
```
- In Kiauh Punkt 3 wählen für Remove
- Unter Webinterface entfernen wir Mainsail mit Punkt 3
- Unter Firware & API entfernen wir nun Moonraker (Punkt 2) sowie Klipper (Punkt 1)
- mit b gehen wir wieder zum Hauptbildschirm zurück
### Installtion
- Wir wählen nun Punkt 1 aus für Install
- als erstes installieren wir Klipper (Punkt 1)
- als zweites wird Moonraker installiert (Punkt 2)
- Jetzt Installieren wir unser Webfrontend welches wir haben wollen.
  - Das erste Interface das insatlliert wird, wird auf Port 80 gemappt, d.h
  wenn wir Mainsail als erstes installieren können wir es dann direkt ohne eingabe eines Ports aufrufen. Bei installation eines weiteren Interface wird bei der Installation nach einen Port gefragt (im Video habe ich hierzu Port 81 eingegeben), das zweite Interface wäre somit unter http://raspberrypi.local:81 erreichbar.
- mit b gehen wir wieder zum Hauptbildschirm zurück

## Firmware Compilieren
- Hier wählen wir den Punkt 4 Advanced um die Firmware zu Compiliren
- Unter Firware wählen wir Build only aus (Punkt 2)
  - extra low-level anhaken damit die erweiterten Communications interfaces angezeigt werden
  - als Micro-contraoller wälen wir STM32 aus
  - Processor model ist STM32F407
  - Bootloader offset ist 48KiB
  - Communication interface USB PA11/Pa12 für den Front USB Anschluss
    - Für RX und TX am Wifi-Modul wählen wir hier Serial USART1 PA10/PA9
  - mit Q und Y verlassen wir Firmware Configuration
- mit b gehen wir wieder zum Hauptbildschirm zurück
- mit q verlassen wir Kiauh

## Firmware Kopieren ins Webinterface Kopieren
```
cp ~/klipper/out/klipper.bin ~/printer_data/config/Robin_nano_6.bin
```
Als nächstes rufen wir das Webinterface auf z.B. mit http://raspberrypi.local je nachdem wie ihr den Rechner genannt habt.
Unter MASCHINE seht ihr jetzt die Kopierte Firmware Datei Robin_nano_6.bin, rechte Maustaste und Dowload klicken.
Kopiert die Datei auf eure SD Karte und legt diese in den Drucker ein.
Schalten den Druck aus falls er an ist und erneut ein. Jetzt sollte ein Ersae zu sehen sein und dann im Anschluss ein Update, das mit einem Beep Quitiert.
Lasst den Drucker noch ein paar Sekunden an und schaltet ihn nun wieder aus, Entfernt die SD Karte, diese brauchen wir hier nicht mehr. Schaltet den Drucker ein, es sollte das Display Dunkel bleiben und ein Beep ertönen. Klipper sollte nun erfolgreich Installiert sein.

## Konfigurationen Importieren
Downladed nun den kompletten Inhalt dieses Repositories 
https://github.com/blacknet76/FlyingBear-Reborn-2/archive/refs/heads/main.zip und entpackt es in eurem Download Verzeichniss.
- zieht die printer.cfg Datei in den config Order von MASCHINE
- erstellt einen Ordner mit dem Namen **macros** und öffnet diesen
- zieht nun die Dateien die im **macros** Ordner sind, auf in den neu erstellen Ordner
- geht wieder eine Ebene höher wo unsere printer.cfg Datei liegt.

## Drucker Anschluss herausfinden

```
ls /dev/serial/by-id/*
```
Das Ergebnis sollte in etwa so aussehen
*/dev/serial/by-id/usb-Klipper_stm32f407xx_32004A001350465636393320-if00*

Öffnet im Webinterface die **printer.cfg**  Datei und scrollt bis zur MCU Settings hintunter
bei **serial:** tragen wir nun das Ergebnis von unserem ls Befehl ein
*serial: /dev/serial/by-id/usb-Klipper_stm32f407xx_32004A001350465636393320-if00*
Nun klickt oben rechts auf Save & Restart.
Klipper startet nun neu und läd die neue Config ein. Wenn alles funktioniert hat ist der Drucker nun über das Frontend erreichbar.

## Drucker in Betrieb nehmen
Als erstes Homen wir den Drucker, in dem wir im Dashboard auf das Icon mit dem Haus ALL drücken, jetzt führen wir ein PID Tuning für das Hontend und das Bed durch.

### PID Tuning
Hotend PID Tuning
```
PID_CALIBRATE HEATER=extruder TARGET=220
```
Wenn der Prozess abgeschlossen ist Speichern wir die Werte ab
```
SAVE_CONFIG
```
```
PID_CALIBRATE HEATER=heater_bed TARGET=80
```
Auch hier müssen wir nach Abschluss des Prozesses die werte Speichern
```
SAVE_CONFIG
```

### Bed Leveling / Z-Offset
Hier müsst ihr mit dem Papertest euer Bed soweit leveln damit die Noozle am Papier kratz, dies sollte bei allen vier Ecken gleich sein. Anschließend können wir ein Z-Offset einstellen.
Als erstes Fahren wir Z etwas höher z.B. auf 5
und bei X und Y tragen wir 162 ein damit der Kopf in der Mitte des Bettes ist.
Jetzt initalisieren wir den Manuellen Z-Offset mit
```
Z_ENDSTOP_CALIBRATE
```
Jetzt müssen wir den Optimalen Z-Offset finden in dem wir immer weiter mit der Noolze zum Bett gehen, dies machen wir mit z.B. mit
```
TESTZ Z=-.1
```
*Hier habt ihr auch die Möglichkeit in geringeren Schritten nach unten zu gehen z.B. mit Z=-.01 oder mit Z=-.001*
Hier müsst ihr selbst herausfinden wie weit ihr herunter müsst.
Ich würde hier nur grob ausrichten und den Rest dann während eines Test Prints durchführen im Frontend mit dem Z-Offset.

### Pressure advance
Hier müsste ihr den square_tower downloaden. 
```
https://www.klipper3d.org/prints/square_tower.stl
```
Im Slicer müsst ihr dann die Geschwindikeit auf 100mm/s erhöhen eine Layerhöhe von 75% mehr also bei einer 0.4mm Noozle wäre das dann 0.3.

In der Console vom Webinterface tragen wir nun
```
SET_VELOCITY_LIMIT SQUARE_CORNER_VELOCITY=1 ACCEL=500
```
ein, um den ganzen Prozess zu starten.
Anschließend geben wir noch 
```
TUNING_TOWER COMMAND=SET_PRESSURE_ADVANCE PARAMETER=ADVANCE START=0 FACTOR=.005
```
ein. Jetzt starten wir den Druck mit dem square_tower.

Alterantiv kann man auch hier eine schnellere Variante nehmen, die nicht ganz so zeitaufwendig ist.
```
https://realdeuce.github.io/Voron/PA/pressure_advance.html
```
#### Extruder Wert
```
pressure_advance: 0.0715 ; <-- hier den Wert hinterlegen
```
#### PrusaSlicer/SuperSlicer Wert für Filament
der Code könnte auch in Cura im Start-Code hinterlegt werden, macht aber nur Sinn wenn es immer das gleiche Filament ist was man verdruckt.
```
SET_PRESSURE_ADCANCE ADVANCE=0.0715 # <-- hier den Wert hinterlgen
```