# Webcam mit crowsnest

### <u>Crowsnest Github Repo</u>
#### https://github.com/mainsail-crew/crowsnest/

### <u>**Crownest deinstallieren**</u>
```
cd ~/crowsnest
```
```
make uninstall
```
### <u>**Crownest intallieren**</u>
```
cd ~
git clone --branch ref/installer https://github.com/mainsail-crew/crowsnest.git
``` 
```
cd crowsnest
```
```
make config
```

### <u>**Moonraker.conf anpassen**</u>
- Moonraker.conf Web Interface öffnen
- Eintrag am ende hinzufügen
```
[update_manager crowsnest]
type: git_repo
path: ~/crowsnest
origin: https://github.com/mainsail-crew/crowsnest.git
install_script: tools/install.sh
```

### <u>**Camera konfigureren**</u>
- Im config Ordner die datei crowsnest.conf anlegen
```
#### crowsnest.conf
#### This is mainsail / MainsailOS default config.
#### See:
#### https://github.com/mainsail-crew/crowsnest/blob/master/README.md
#### for details to configure to your needs.


#####################################################################
####                                                            #####
####      Information about ports and according URL's           #####
####                                                            #####
#####################################################################
####                                                            #####
####    Port 8080 equals /webcam/?action=[stream/snapshot]      #####
####    Port 8081 equals /webcam2/?action=[stream/snapshot]     #####
####    Port 8082 equals /webcam3/?action=[stream/snapshot]     #####
####    Port 8083 equals /webcam4/?action=[stream/snapshot]     #####
####                                                            #####
#####################################################################


[crowsnest]
log_path: ~/printer_data/logs/crowsnest.log
log_level: verbose                      # Valid Options are quiet/verbose/debug
delete_log: false                       # Deletes log on every restart, if set to true

[cam 1]
mode: mjpg                              # mjpg/rtsp
port: 8080                              # Port
device: /dev/video0                     # See Log for available ...
resolution: 1920x1080                   # widthxheight format
max_fps: 25                             # If Hardware Supports this it will be forced, ohterwise ignored/coerced.
#custom_flags:                          # You can run the Stream Services with custom flags.
#v4l2ctl: brightness=0
```
### <u>**Befehle**</u>
- Alle Devices auflisten
```
v4l2-ctl --list-devices   
```
- Alle verfügbaren Parameter der Camera auflisten
```
v4l2-ctl --list-ctrls   
```
- z.B. die Brightness auf 64 setzen
```
v4l2-ctl --set-ctrl=brightness=64 
```
Man kann auf der Console alle Parameter einstellen und im Webfrontend die Änderungen sofort sehen, somit kann man sich alles schön zusammen "basteln"