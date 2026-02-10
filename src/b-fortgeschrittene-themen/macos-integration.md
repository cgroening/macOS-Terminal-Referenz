# macOS-Integration

macOS bietet viele Terminal-Befehle zur Integration mit dem Betriebssystem: Zwischenablage, Finder, Systemeinstellungen und mehr.

## 1    Zwischenablage

Die macOS-Zwischenablage (Pasteboard) ist vollständig vom Terminal aus zugänglich.

### 1.1    `pbcopy` – In Zwischenablage kopieren

`pbcopy` kopiert stdin in die Zwischenablage.

**Grundlegende Nutzung:**

```zsh
# Text kopieren
echo "Hallo Welt" | pbcopy

# Dateiinhalt kopieren
cat datei.txt | pbcopy
pbcopy < datei.txt

# Befehlsausgabe kopieren
pwd | pbcopy
ls -la | pbcopy

# Mehrere Zeilen
cat << EOF | pbcopy
Zeile 1
Zeile 2
Zeile 3
EOF
```

**Optionen:**

```zsh
# Standard-Pasteboard (wie Cmd+C)
echo "text" | pbcopy

# Find-Pasteboard (für Cmd+G Suche)
echo "suchtext" | pbcopy -pboard find

# Ruler-Pasteboard
echo "data" | pbcopy -pboard ruler
```

### 1.2    `pbpaste` – Aus Zwischenablage einfügen

`pbpaste` gibt den Inhalt der Zwischenablage auf stdout aus.

**Grundlegende Nutzung:**

```zsh
# In Terminal ausgeben
pbpaste

# In Datei speichern
pbpaste > datei.txt

# An Datei anhängen
pbpaste >> datei.txt

# Als Variable
content=$(pbpaste)
echo "$content"
```

**Optionen:**

```zsh
# Standard-Pasteboard
pbpaste

# Find-Pasteboard
pbpaste -pboard find

# Nur Plain Text (falls Rich Text kopiert)
pbpaste -Prefer txt
```

### 1.3    Praxisbeispiele mit Pipes

**Textverarbeitung:**

```zsh
# Kopierte URLs bereinigen
pbpaste | tr -d '\n' | pbcopy

# Großbuchstaben
pbpaste | tr '[:lower:]' '[:upper:]' | pbcopy

# Zeilen sortieren
pbpaste | sort | pbcopy

# Duplikate entfernen
pbpaste | sort -u | pbcopy

# Leerzeichen trimmen
pbpaste | sed 's/^[[:space:]]*//;s/[[:space:]]*$//' | pbcopy

# Zeilen nummerieren
pbpaste | nl | pbcopy
```

**Entwicklung:**

```zsh
# Git diff kopieren
git diff | pbcopy

# JSON formatieren
pbpaste | jq . | pbcopy

# Base64 kodieren
pbpaste | base64 | pbcopy

# Base64 dekodieren
pbpaste | base64 -d | pbcopy

# MD5-Hash von Clipboard-Inhalt
pbpaste | md5

# URL-Encoding
pbpaste | python3 -c "import sys, urllib.parse; print(urllib.parse.quote(sys.stdin.read().strip()))" | pbcopy
```

**Pfade und Dateien:**

```zsh
# Aktuellen Pfad kopieren
pwd | pbcopy

# Dateiliste kopieren
ls -1 | pbcopy

# Voller Pfad einer Datei
realpath datei.txt | pbcopy

# SSH Public Key kopieren
cat ~/.ssh/id_ed25519.pub | pbcopy
```

**Nützliche Aliase:**

```zsh
# ~/.zshrc

# Clipboard-Shortcuts
alias cb='pbcopy'
alias cbp='pbpaste'
alias cbpwd='pwd | pbcopy'

# Pfad kopieren
cppath() { echo -n "$(pwd)/$1" | pbcopy; echo "Copied: $(pwd)/$1"; }

# Dateiinhalt kopieren
cpfile() { cat "$1" | pbcopy; echo "Copied contents of $1"; }

# JSON formatieren und kopieren
cbjson() { pbpaste | jq . | pbcopy; echo "JSON formatted"; }

# Clipboard-Inhalt anzeigen und in Datei speichern
cbsave() { pbpaste | tee "$1"; }
```

## 2    Dateien & Programme öffnen

### 2.1    `open` – Dateien und URLs öffnen

`open` öffnet Dateien, Verzeichnisse und URLs mit der Standardanwendung.

**Dateien öffnen:**

```zsh
# Mit Standard-App öffnen
open dokument.pdf
open bild.png
open tabelle.xlsx

# Mehrere Dateien
open datei1.txt datei2.txt datei3.txt

# Alle Bilder im Ordner
open *.jpg
```

**Verzeichnisse öffnen:**

```zsh
# Aktuelles Verzeichnis im Finder
open .

# Bestimmtes Verzeichnis
open ~/Documents
open /Applications

# Home-Verzeichnis
open ~
```

**URLs öffnen:**

```zsh
# Webseite im Standard-Browser
open https://www.example.com

# E-Mail-Link
open mailto:max@example.com

# FTP
open ftp://ftp.example.com

# Mit Parametern
open "https://www.google.com/search?q=terminal+commands"
```

**Spezielle URLs:**

```zsh
# Systemeinstellungen
open "x-apple.systempreferences:"
open "x-apple.systempreferences:com.apple.preference.security"

# App Store
open "macappstore://itunes.apple.com/app/id1234567890"

# Maps
open "maps://?q=Berlin"

# FaceTime
open "facetime://max@example.com"
```

### 2.2    `open -a` – Mit bestimmter App öffnen

```zsh
# Mit spezifischer App öffnen
open -a "Visual Studio Code" datei.txt
open -a Safari https://example.com
open -a "Google Chrome" index.html
open -a Preview bild.png
open -a TextEdit dokument.txt

# App ohne Datei starten
open -a Finder
open -a Terminal
open -a "Activity Monitor"

# Kurzform für .app im Applications-Ordner
open -a Safari
```

**Mit Bundle-ID:**

```zsh
# Über Bundle-Identifier
open -b com.apple.Safari https://example.com
open -b com.microsoft.VSCode datei.txt
open -b com.apple.TextEdit dokument.txt

# Bundle-ID einer App finden
osascript -e 'id of app "Safari"'
# com.apple.Safari

mdls -name kMDItemCFBundleIdentifier /Applications/Safari.app
```

**Neue Instanz:**

```zsh
# Neue App-Instanz (zweites Fenster)
open -n -a Safari

# Versteckt starten
open -j -a "Some App"

# Im Hintergrund (nicht in Vordergrund bringen)
open -g datei.txt
```

### 2.3    `open -R` – Im Finder anzeigen

```zsh
# Datei im Finder zeigen (Reveal)
open -R datei.txt

# Öffnet Finder mit selektierter Datei
open -R ~/Documents/wichtig.pdf

# Nützlich für Downloads
open -R ~/Downloads/installer.dmg
```

**Weitere Optionen:**

| Option | Beschreibung |
| ------ | ------------ |
| `-a APP` | Mit bestimmter App öffnen |
| `-b BUNDLE` | Mit Bundle-ID öffnen |
| `-e` | Mit TextEdit öffnen |
| `-t` | Mit Standard-Texteditor öffnen |
| `-f` | stdin als Datei öffnen |
| `-F` | Frische App (ohne gespeicherten Zustand) |
| `-W` | Warten bis App beendet |
| `-R` | Im Finder anzeigen |
| `-n` | Neue Instanz |
| `-g` | Im Hintergrund |
| `-j` | Versteckt starten |
| `-h` | App verstecken wenn andere aktiv |

**Praktische Beispiele:**

```zsh
# Code im Editor öffnen
open -a "Visual Studio Code" ~/projekte/app/

# Screenshot im Preview öffnen
screencapture -i /tmp/screenshot.png && open -a Preview /tmp/screenshot.png

# Befehlsausgabe im Editor öffnen
ls -la | open -f -a TextEdit

# Auf App-Ende warten
open -W dokument.pdf
echo "PDF wurde geschlossen"

# Quick Look (Vorschau)
qlmanage -p datei.pdf
```

## 3    Systemeinstellungen

### 3.1    `defaults` – Einstellungen lesen

`defaults` liest und schreibt macOS-Einstellungen (Property Lists).

**Einstellungen lesen:**

```zsh
# Alle Einstellungen einer App
defaults read com.apple.finder

# Bestimmte Einstellung
defaults read com.apple.finder ShowPathbar

# Globale Einstellungen
defaults read NSGlobalDomain

# Bestimmte globale Einstellung
defaults read NSGlobalDomain AppleShowAllExtensions
```

**Einstellungen suchen:**

```zsh
# Nach Schlüssel suchen
defaults read | grep -i "pathbar"

# Alle Domains auflisten
defaults domains | tr ',' '\n'

# Alle Einstellungen einer Domain durchsuchen
defaults read com.apple.dock | grep -i "size"
```

**Typ einer Einstellung:**

```zsh
# Typ ermitteln
defaults read-type com.apple.finder ShowStatusBar
# Type is boolean
```

### 3.2    `defaults write` – Einstellungen ändern

**Syntax:**

```zsh
defaults write DOMAIN KEY VALUE
defaults write DOMAIN KEY -TYPE VALUE
```

**Typen:**

| Typ | Flag | Beispiel |
| --- | ---- | -------- |
| String | `-string` | `"text"` |
| Integer | `-int` | `42` |
| Float | `-float` | `3.14` |
| Boolean | `-bool` | `true`, `false`, `YES`, `NO` |
| Array | `-array` | `item1 item2` |
| Dictionary | `-dict` | `key1 value1 key2 value2` |

**Beispiele:**

```zsh
# Boolean
defaults write com.apple.finder ShowPathbar -bool true

# Integer
defaults write com.apple.dock tilesize -int 48

# Float
defaults write NSGlobalDomain com.apple.mouse.scaling -float 2.5

# String
defaults write com.apple.screencapture location -string "~/Screenshots"

# Array
defaults write com.apple.dock persistent-apps -array
```

**Nach Änderungen:**

```zsh
# Finder neu starten
killall Finder

# Dock neu starten
killall Dock

# SystemUIServer neu starten (Menüleiste)
killall SystemUIServer
```

### 3.3    Nützliche versteckte Einstellungen

**Finder:**

```zsh
# Versteckte Dateien anzeigen
defaults write com.apple.finder AppleShowAllFiles -bool true

# Pfadleiste anzeigen
defaults write com.apple.finder ShowPathbar -bool true

# Statusleiste anzeigen
defaults write com.apple.finder ShowStatusBar -bool true

# Dateiendungen immer anzeigen
defaults write NSGlobalDomain AppleShowAllExtensions -bool true

# Warnung bei Endungsänderung deaktivieren
defaults write com.apple.finder FXEnableExtensionChangeWarning -bool false

# Standard-Ansicht (Liste)
defaults write com.apple.finder FXPreferredViewStyle -string "Nlsv"
# Nlsv=Liste, icnv=Icons, clmv=Spalten, Flwv=Cover Flow

# Neues Fenster zeigt Home
defaults write com.apple.finder NewWindowTarget -string "PfHm"

# Finder beenden erlauben
defaults write com.apple.finder QuitMenuItem -bool true

killall Finder
```

**Dock:**

```zsh
# Dock-Größe
defaults write com.apple.dock tilesize -int 36

# Vergrößerung
defaults write com.apple.dock magnification -bool true
defaults write com.apple.dock largesize -int 64

# Position (left, bottom, right)
defaults write com.apple.dock orientation -string "left"

# Automatisch ausblenden
defaults write com.apple.dock autohide -bool true

# Ausblende-Verzögerung
defaults write com.apple.dock autohide-delay -float 0
defaults write com.apple.dock autohide-time-modifier -float 0.5

# Nur aktive Apps zeigen
defaults write com.apple.dock static-only -bool true

# Letzte Apps deaktivieren
defaults write com.apple.dock show-recents -bool false

# App-Indikatoren (Punkte) ausblenden
defaults write com.apple.dock show-process-indicators -bool false

# Spaces-Animation beschleunigen
defaults write com.apple.dock workspaces-edge-delay -float 0.1

killall Dock
```

**Screenshots:**

```zsh
# Speicherort ändern
defaults write com.apple.screencapture location -string "~/Screenshots"

# Format ändern (png, jpg, gif, pdf, tiff)
defaults write com.apple.screencapture type -string "png"

# Schatten deaktivieren
defaults write com.apple.screencapture disable-shadow -bool true

# Dateiname-Präfix
defaults write com.apple.screencapture name -string "Screenshot"

# Zeitstempel im Namen deaktivieren
defaults write com.apple.screencapture include-date -bool false

killall SystemUIServer
```

**Safari:**

```zsh
# Entwicklermenü aktivieren
defaults write com.apple.Safari IncludeDevelopMenu -bool true

# Web Inspector aktivieren
defaults write com.apple.Safari WebKitDeveloperExtrasEnabledPreferenceKey -bool true

# Vollständige URL anzeigen
defaults write com.apple.Safari ShowFullURLInSmartSearchField -bool true

# Links in Tabs öffnen
defaults write com.apple.Safari TargetedClicksCreateTabs -bool true
```

**Tastatur & Eingabe:**

```zsh
# Tastenwiederholung (schneller)
defaults write NSGlobalDomain KeyRepeat -int 2

# Verzögerung bis Wiederholung
defaults write NSGlobalDomain InitialKeyRepeat -int 15

# Autokorrektur deaktivieren
defaults write NSGlobalDomain NSAutomaticSpellingCorrectionEnabled -bool false

# Automatische Großschreibung deaktivieren
defaults write NSGlobalDomain NSAutomaticCapitalizationEnabled -bool false

# Smart Quotes deaktivieren
defaults write NSGlobalDomain NSAutomaticQuoteSubstitutionEnabled -bool false

# Smart Dashes deaktivieren
defaults write NSGlobalDomain NSAutomaticDashSubstitutionEnabled -bool false
```

**Verschiedenes:**

```zsh
# Crash-Reporter-Dialoge als Notification
defaults write com.apple.CrashReporter UseUNC 1

# Erweiterten Druckdialog als Standard
defaults write NSGlobalDomain PMPrintingExpandedStateForPrint -bool true

# Erweiterten Speicherdialog als Standard
defaults write NSGlobalDomain NSNavPanelExpandedStateForSaveMode -bool true

# .DS_Store auf Netzwerklaufwerken deaktivieren
defaults write com.apple.desktopservices DSDontWriteNetworkStores -bool true

# Bluetooth-Audio-Qualität (aptX)
defaults write com.apple.BluetoothAudioAgent "Apple Bitpool Min (editable)" -int 40

# Subpixel-Font-Rendering (für externe Monitore)
defaults write NSGlobalDomain AppleFontSmoothing -int 1

# App Nap deaktivieren (für alle Apps)
defaults write NSGlobalDomain NSAppSleepDisabled -bool true
```

**Mission Control:**

```zsh
# Fenster nach App gruppieren
defaults write com.apple.dock expose-group-apps -bool true

# Spaces automatisch neu anordnen deaktivieren
defaults write com.apple.dock mru-spaces -bool false

# Dashboard deaktivieren
defaults write com.apple.dashboard mcx-disabled -bool true

killall Dock
```

### 3.4    `defaults delete` – Einstellungen zurücksetzen

```zsh
# Einzelne Einstellung löschen
defaults delete com.apple.finder ShowPathbar

# Alle Einstellungen einer App zurücksetzen
defaults delete com.apple.finder

# Globale Einstellung löschen
defaults delete NSGlobalDomain AppleShowAllExtensions

# Nach Löschen: App neu starten
killall Finder
```

**Aktuelle Einstellungen sichern:**

```zsh
# Domain exportieren
defaults export com.apple.finder ~/finder-backup.plist

# Domain importieren
defaults import com.apple.finder ~/finder-backup.plist
```

## 4    Automator & Shortcuts

### 4.1    Shell-Skripte in Automator

Automator kann Shell-Skripte ausführen und in Workflows integrieren.

**Workflow erstellen:**

1. Automator öffnen
2. Neues Dokument → Workflow oder Quick Action
3. "Shell-Skript ausführen" aus der Bibliothek ziehen
4. Shell wählen (`/bin/zsh`) und Skript eingeben

**Eingabe-Optionen:**

| Option | Beschreibung |
| ------ | ------------ |
| "an stdin" | Eingabe als stdin |
| "als Argumente" | Eingabe als `$1`, `$2`, ... |

**Beispiel: Bilder konvertieren**

```bash
#!/bin/zsh
# Shell: /bin/zsh
# Eingabe: als Argumente

for f in "$@"; do
    sips -s format png "$f" --out "${f%.*}.png"
done
```

**Beispiel: Text transformieren**

```bash
#!/bin/zsh
# Shell: /bin/zsh
# Eingabe: an stdin

tr '[:lower:]' '[:upper:]'
```

**Beispiel: Clipboard verarbeiten**

```bash
#!/bin/zsh
# Kein Input nötig

pbpaste | sort -u | pbcopy
osascript -e 'display notification "Duplikate entfernt" with title "Automator"'
```

### 4.2    Quick Actions erstellen

Quick Actions erscheinen im Finder-Kontextmenü und der Touch Bar.

**Quick Action erstellen:**

1. Automator → Neues Dokument → "Schnellaktion"
2. Eingabe: "Dateien oder Ordner" in "Finder"
3. Shell-Skript hinzufügen
4. Speichern (Name erscheint im Kontextmenü)

**Beispiel: In Terminal öffnen**

```bash
#!/bin/zsh
# Eingabe: als Argumente

for f in "$@"; do
    if [[ -d "$f" ]]; then
        open -a Terminal "$f"
        break
    else
        open -a Terminal "$(dirname "$f")"
        break
    fi
done
```

**Beispiel: Bilder verkleinern**

```bash
#!/bin/zsh
# Eingabe: als Argumente

for f in "$@"; do
    sips --resampleWidth 1920 "$f" --out "${f%.*}_small.${f##*.}"
done

osascript -e 'display notification "Bilder verkleinert" with title "Quick Action"'
```

**Beispiel: PDF zusammenfügen**

```bash
#!/bin/zsh
# Eingabe: als Argumente

output="$HOME/Desktop/Merged_$(date +%Y%m%d_%H%M%S).pdf"
"/System/Library/Automator/Combine PDF Pages.action/Contents/MacOS/join" -o "$output" "$@"
open -R "$output"
```

**Speicherort:**

```
~/Library/Services/
```

### 4.3    Integration mit Shortcuts-App

Die Shortcuts-App (ab macOS Monterey) kann Shell-Skripte ausführen.

**Shell-Skript in Shortcuts:**

1. Shortcuts-App öffnen
2. Neuer Shortcut
3. "Shell-Skript ausführen" hinzufügen
4. Shell und Eingabe konfigurieren

**Eingabe verwenden:**

```bash
#!/bin/zsh
# "Shortcut-Eingabe" ist verfügbar als stdin oder $1

# Als stdin
cat | tr '[:lower:]' '[:upper:]'

# Als Argument
echo "$1" | tr '[:lower:]' '[:upper:]'
```

**Shortcuts per Terminal ausführen:**

```zsh
# Shortcut ausführen
shortcuts run "Mein Shortcut"

# Mit Eingabe
echo "text" | shortcuts run "Mein Shortcut"

# Ergebnis erhalten
shortcuts run "Mein Shortcut" <<< "input"

# Shortcut-Liste
shortcuts list
```

**Beispiel: API-Aufruf**

```bash
#!/bin/zsh
curl -s "https://api.example.com/data" | jq -r '.result'
```

**Beispiel: Systeminfo**

```bash
#!/bin/zsh
echo "Hostname: $(hostname)"
echo "User: $(whoami)"
echo "Uptime: $(uptime | awk -F'up ' '{print $2}' | awk -F',' '{print $1}')"
echo "IP: $(ipconfig getifaddr en0)"
```

## 5    Weitere macOS-Befehle

### 5.1    `say` – Text vorlesen

`say` wandelt Text in Sprache um (Text-to-Speech).

**Grundlegende Nutzung:**

```zsh
# Text vorlesen
say "Hallo Welt"

# Aus Datei vorlesen
say -f dokument.txt

# Von stdin
echo "Test" | say

# Clipboard vorlesen
pbpaste | say
```

**Stimme wählen:**

```zsh
# Mit bestimmter Stimme
say -v Anna "Guten Tag"       # Deutsch
say -v Samantha "Hello"       # Englisch (US)
say -v Daniel "Hello"         # Englisch (UK)

# Verfügbare Stimmen
say -v ?

# Alle deutschen Stimmen
say -v ? | grep de_
```

**In Datei speichern:**

```zsh
# Als AIFF
say -o ausgabe.aiff "Dieser Text wird gespeichert"

# Als MP4/M4A
say -o ausgabe.m4a --data-format=aac "Dieser Text"
```

**Geschwindigkeit:**

```zsh
# Langsamer (Standard: ~175 Wörter/Minute)
say -r 100 "Langsam gesprochen"

# Schneller
say -r 250 "Schnell gesprochen"
```

**Interaktiv (Zeichen für Zeichen):**

```zsh
# Eingabe Zeichen für Zeichen vorlesen
say -i
```

**Praktische Anwendungen:**

```zsh
# Nach langem Befehl benachrichtigen
make build && say "Build fertig" || say "Build fehlgeschlagen"

# Timer
sleep 300 && say "5 Minuten vorbei"

# Pomodoro
{ sleep 1500 && say "Pause machen"; sleep 300 && say "Weiterarbeiten"; } &
```

### 5.2    `screencapture` – Screenshots per Terminal

**Bildschirmfoto:**

```zsh
# Ganzer Bildschirm
screencapture screenshot.png

# Interaktive Auswahl (wie Cmd+Shift+4)
screencapture -i screenshot.png

# Bestimmtes Fenster (wie Cmd+Shift+4, Space)
screencapture -iW screenshot.png

# Mit Mauszeiger
screencapture -C screenshot.png

# Ohne Schatten bei Fenstern
screencapture -o screenshot.png
```

**In Zwischenablage:**

```zsh
# In Clipboard statt Datei
screencapture -c

# Interaktiv in Clipboard
screencapture -ic
```

**Verzögerung:**

```zsh
# 5 Sekunden warten
screencapture -T 5 screenshot.png

# Interaktiv mit Verzögerung
screencapture -i -T 3 screenshot.png
```

**Formate:**

```zsh
# PNG (Standard)
screencapture -t png screenshot.png

# JPEG
screencapture -t jpg screenshot.jpg

# PDF
screencapture -t pdf screenshot.pdf

# TIFF
screencapture -t tiff screenshot.tiff
```

**Mehrere Bildschirme:**

```zsh
# Jeden Monitor separat
screencapture screen1.png screen2.png

# Nur bestimmten Monitor (-D)
screencapture -D 1 screen.png   # Hauptmonitor
screencapture -D 2 screen.png   # Zweiter Monitor
```

**Optionen:**

| Option | Beschreibung |
| ------ | ------------ |
| `-i` | Interaktive Auswahl |
| `-iW` | Fenster-Auswahl |
| `-iU` | Fenster ohne Schatten |
| `-c` | In Zwischenablage |
| `-C` | Mit Mauszeiger |
| `-o` | Ohne Fensterschatten |
| `-T SEC` | Verzögerung |
| `-t TYPE` | Format (png, jpg, pdf) |
| `-x` | Ohne Ton |
| `-D N` | Display N |
| `-R x,y,w,h` | Rechteck-Bereich |

**Praktische Beispiele:**

```zsh
# Schneller Screenshot auf Desktop
screencapture -x ~/Desktop/screenshot-$(date +%Y%m%d-%H%M%S).png

# Bereich 800x600 ab Position 100,100
screencapture -R 100,100,800,600 region.png

# Screenshot-Funktion
screenshot() {
    local name="${1:-screenshot}"
    screencapture -i ~/Screenshots/"${name}_$(date +%Y%m%d_%H%M%S).png"
}
```

### 5.3    `caffeinate` – Ruhezustand verhindern

`caffeinate` verhindert, dass der Mac in den Ruhezustand geht.

**Grundlegende Nutzung:**

```zsh
# Bis zum Abbruch (Ctrl+C)
caffeinate

# Für bestimmte Zeit (Sekunden)
caffeinate -t 3600   # 1 Stunde

# Während Befehl läuft
caffeinate -s make build

# Während Prozess läuft
caffeinate -w 1234   # PID 1234
```

**Optionen:**

| Option | Beschreibung |
| ------ | ------------ |
| `-d` | Display wach halten |
| `-i` | System wach halten (idle) |
| `-m` | Disk wach halten |
| `-s` | System wach halten (auch mit Netzteil) |
| `-u` | User aktiv simulieren |
| `-t SEC` | Timeout in Sekunden |
| `-w PID` | Bis Prozess endet |

**Praktische Beispiele:**

```zsh
# Während langer Download
caffeinate -i wget https://example.com/large-file.zip

# Während Backup
caffeinate -im rsync -avz /source/ /backup/

# Präsentation: Display an lassen
caffeinate -d -t 7200  # 2 Stunden

# Bis bestimmter Prozess endet
caffeinate -w $(pgrep -x "Handbrake")
```

**Als Hintergrundprozess:**

```zsh
# Im Hintergrund starten
caffeinate -d &
CAFFEINE_PID=$!

# Später beenden
kill $CAFFEINE_PID
```

### 5.4    `mdfind` – Spotlight-Suche per Terminal

`mdfind` nutzt den Spotlight-Index für schnelle Suchen.

**Grundlegende Suche:**

```zsh
# Nach Name oder Inhalt
mdfind "projektplan"

# Nur Dateinamen
mdfind -name "readme.md"

# In bestimmtem Verzeichnis
mdfind -onlyin ~/Documents "bericht"
```

**Nach Dateityp:**

```zsh
# PDFs
mdfind "kind:pdf projektplan"

# Bilder
mdfind "kind:image"

# E-Mails
mdfind "kind:email from:max"

# Präsentationen
mdfind "kind:presentation"
```

**Metadata-Attribute:**

```zsh
# Nach Erstellungsdatum
mdfind "kMDItemContentCreationDate > \$time.today(-7)"

# Nach Autor
mdfind "kMDItemAuthors == 'Max Mustermann'"

# Nach Dateiendung
mdfind "kMDItemFSName == '*.py'"

# Nach Größe (Bytes)
mdfind "kMDItemFSSize > 100000000"  # > 100 MB
```

**Kombinierte Abfragen:**

```zsh
# PDF und Name
mdfind "kind:pdf AND name:report"

# In Ordner, bestimmter Typ
mdfind -onlyin ~/Projects "kind:source"

# Kürzlich geändert
mdfind "kMDItemContentModificationDate > \$time.today(-1)"
```

**Live-Suche:**

```zsh
# Ergebnisse live aktualisieren
mdfind -live "projektplan"
```

**Mit anderen Befehlen:**

```zsh
# Gefundene PDFs öffnen
mdfind -name "report.pdf" | head -1 | xargs open

# Ergebnisse zählen
mdfind "kind:image" -onlyin ~/Pictures | wc -l

# Größe aller gefundenen Dateien
mdfind "kind:movie" | xargs du -ch | tail -1
```

**Spotlight-Index verwalten:**

```zsh
# Index-Status
mdutil -s /

# Index neu erstellen
sudo mdutil -E /

# Spotlight für Volume deaktivieren
sudo mdutil -i off /Volumes/USB
```

### 5.5    `afplay` – Audio abspielen

`afplay` spielt Audiodateien über das Terminal.

**Grundlegende Nutzung:**

```zsh
# Datei abspielen
afplay musik.mp3
afplay sound.aiff
afplay audio.m4a

# System-Sounds abspielen
afplay /System/Library/Sounds/Ping.aiff
afplay /System/Library/Sounds/Glass.aiff
```

**Optionen:**

```zsh
# Lautstärke (0.0 - 1.0)
afplay -v 0.5 musik.mp3

# Abspielrate (1.0 = normal)
afplay -r 1.5 musik.mp3   # 50% schneller
afplay -r 0.5 musik.mp3   # 50% langsamer

# Bestimmte Zeit abspielen
afplay -t 10 musik.mp3    # 10 Sekunden

# Im Hintergrund
afplay musik.mp3 &
```

**System-Sounds:**

```zsh
# Verfügbare System-Sounds
ls /System/Library/Sounds/

# Notification-Sound
afplay /System/Library/Sounds/Funk.aiff

# Für Skript-Benachrichtigungen
notify() {
    afplay /System/Library/Sounds/Glass.aiff
    osascript -e "display notification \"$1\" with title \"Terminal\""
}

# Verwendung
make build && notify "Build fertig"
```

**Praktische Beispiele:**

```zsh
# Wecker
sleep 3600 && afplay /System/Library/Sounds/Ping.aiff

# Befehl mit Sound bei Fertigstellung
long-running-command; afplay /System/Library/Sounds/Hero.aiff

# Alle MP3s in Ordner abspielen
for f in *.mp3; do afplay "$f"; done

# Zufälliger System-Sound
afplay /System/Library/Sounds/$(ls /System/Library/Sounds/ | sort -R | head -1)
```

**Aliase:**

```zsh
# ~/.zshrc

# Benachrichtigungs-Sounds
alias beep='afplay /System/Library/Sounds/Ping.aiff'
alias done='afplay /System/Library/Sounds/Glass.aiff'
alias error='afplay /System/Library/Sounds/Basso.aiff'

# Nach langem Befehl
alias alert='afplay /System/Library/Sounds/Hero.aiff && osascript -e "display notification \"Fertig\" with title \"Terminal\""'
```

**Weitere Audio-Befehle:**

```zsh
# Systemlautstärke
osascript -e "set volume output volume 50"    # 0-100
osascript -e "output volume of (get volume settings)"

# Stummschalten
osascript -e "set volume output muted true"
osascript -e "set volume output muted false"

# Audio-Gerät Info
system_profiler SPAudioDataType
```
