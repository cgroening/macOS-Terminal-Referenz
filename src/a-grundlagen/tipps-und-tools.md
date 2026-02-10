# Tipps und Tools für maximale Effizienz

## 1    Kurzbefehle im Terminal

### 1.1    Navigation und Bearbeitung

| Kurzbefehl     | Beschreibung                            |
| -------------- | --------------------------------------- |
| **STRG + A**   | Springt zum Zeilenanfang                |
| **STRG + E**   | Springt zum Zeilenende                  |
| **OPTION + B** | Springt ein Wort zurück                 |
| **OPTION + F** | Springt ein Wort vorwärts               |
| **STRG + U**   | Löscht alles vor dem Cursor             |
| **STRG + K**   | Löscht alles nach dem Cursor            |
| **STRG + W**   | Löscht das Wort vor dem Cursor          |
| **OPTION + D** | Löscht das Wort nach dem Cursor         |
| **STRG + Y**   | Fügt zuletzt gelöschten Text ein (yank) |
| **STRG + L**   | Bildschirm löschen (wie `clear`)        |
| **STRG + C**   | Beendet den laufenden Prozess           |
| **STRG + Z**   | Hält den Prozess an (siehe Job-Control) |
| **STRG + D**   | Beendet die Shell / EOF senden          |

### 1.2    History-Navigation

| Kurzbefehl   | Beschreibung                          |
| ------------ | ------------------------------------- |
| **↑ / ↓**    | Vorherige/nächste Befehle durchsuchen |
| **STRG + R** | Rückwärts-Suche in der History        |
| **STRG + S** | Vorwärts-Suche in der History         |
| **STRG + G** | Suche abbrechen                       |
| **!!**       | Letzten Befehl wiederholen            |
| **!$**       | Letztes Argument des letzten Befehls  |
| **!^**       | Erstes Argument des letzten Befehls   |
| **!***       | Alle Argumente des letzten Befehls    |

### 1.3    Tab-Vervollständigung

|Kurzbefehl|Beschreibung|
|---|---|
|**TAB**|Befehl/Pfad vervollständigen|
|**TAB TAB**|Alle Möglichkeiten anzeigen|
|**OPTION + /**|Pfad-Vervollständigung erzwingen|

### 1.4    Praktische Kombinationen

**Letzten Befehl mit sudo wiederholen:**

```zsh
apt update
# Permission denied
sudo !!
```

**Argument vom letzten Befehl übernehmen:**

```zsh
cat /etc/hosts
vim !$    # öffnet /etc/hosts in vim
```

**In der History suchen:**

```zsh
# STRG + R drücken, dann tippen:
(reverse-i-search)`brew': brew install htop
```

## 2    Dokumentation

### 2.1    `man` – Handbuch zu beliebigem Befehl anzeigen

```zsh
man grep
```

Navigation im Handbuch:

- **Leertaste:** Nächste Seite
- **`b`:** Vorherige Seite
- **`/`:** Vorwärts suchen
- **`?`:** Rückwärts suchen
- **`n`:** Nächstes Suchergebnis
- **`N`:** Vorheriges Suchergebnis
- **`q`:** Beenden

**Manpage-Sektionen:**

```zsh
man 1 printf    # Benutzerkommandos
man 2 open      # Systemaufrufe
man 3 printf    # Bibliotheksfunktionen
```

### 2.2    `whatis` – Kurzbeschreibung

Zeigt eine einzeilige Beschreibung eines Befehls:

```zsh
whatis ls
# ls(1) - list directory contents

whatis grep sed awk
# grep(1) - file pattern searcher
# sed(1) - stream editor
# awk(1) - pattern-directed scanning and processing language
```

### 2.3    `apropos` – Befehle suchen

Durchsucht die Manpage-Datenbank nach Stichwörtern:

```zsh
apropos network
apropos "copy files"
apropos -s 1 search    # Nur in Sektion 1 suchen
```

Nützlich, wenn man nicht genau weiß, welcher Befehl benötigt wird.

### 2.4    `tldr` – Vereinfachte Hilfe mit Beispielen

`tldr` (Too Long; Didn't Read) bietet prägnante, praxisorientierte Hilfe mit Beispielen.

**Installation:**

```zsh
brew install tldr
```

**Verwendung:**

```zsh
tldr tar
tldr find
tldr git-commit
```

**Beispielausgabe für `tar`:**

```
tar

Archiving utility.
Often combined with a compression method, such as gzip or bzip2.
More information: https://www.gnu.org/software/tar.

- Create an archive from files:
  tar cf target.tar file1 file2 file3

- Create a gzipped archive:
  tar czf target.tar.gz file1 file2 file3

- Extract an archive:
  tar xf source.tar
````

**Cache aktualisieren:**

```zsh
tldr --update
```

### 2.5    Vergleich der Dokumentations-Tools

| Tool      | Zweck                 | Detailgrad | Beispiele |
| --------- | --------------------- | ---------- | --------- |
| `man`     | Vollständige Referenz | Sehr hoch  | Wenige    |
| `whatis`  | Kurzbeschreibung      | Minimal    | Keine     |
| `apropos` | Befehl finden         | -          | -         |
| `tldr`    | Schnelle Hilfe        | Mittel     | Viele     |

## 3    Empfohlener Workflow

1. **Unbekannter Befehl:** `tldr <befehl>` für schnelle Orientierung
2. **Befehl finden:** `apropos <stichwort>` zum Suchen
3. **Details nachschlagen:** `man <befehl>` für vollständige Dokumentation
4. **Kurze Info:** `whatis <befehl>` für Einzeiler

## 4    Finder

**Aktuellen Ordner oder ausgewählte Datei im Finder öffnen:**

- Rechtsklick auf Ordner oder Datei in Pfadleiste :luc_arrow_right: "In Terminal öffnen"
- oder: Rechtsklick auf Ordner oder Datei in Pfadleiste :luc_arrow_right: "Dienste" :luc_arrow_right: "New Warp Tab Here"
## 5    `tee`-Befehl

Der `tee`-Befehl funktioniert als "T-Stück" für Datenströme. Er dupliziert die Ausgabe eines Befehls und sendet diese

- ins Terminal (`stdout`) und
- an eine andere Ziel (Datei, Pipe, usw.).

Dies ist besonders hilfreich bei Aliasen, Shell-Funktionen und Skripten.

**Beispiel:**

```zsh
alias copypwd='pwd | tee >(pbcopy)'
```

Der aktuelle Pfad wird in die Zwischenablage kopiert und im Terminal ausgegeben. Mit `pwd | pbcopy`, d. h. ohne `tee`, wird nur der Pfad kopiert, aber nicht ausgegeben.

## 6    Ausführung von AppleScript

AppleScript- oder JXA-Code (JXA = JavaScript for Automation) kann mit dem Befehl `osascript` ausgeführt werden.

**Syntax:**

```zsh
osascript [Optionen] [Datei | -e "Skript-Code"]
```

**Ein Skript aus einer Datei ausführen:**

```zsh
osascript ~/Skripte/mein_skript.scpt
```

**Beispiel:**

Anzeige des Titels des aktuellen Songs aus Apple Music:

```zsh
osascript -e 'tell application "Music" to get name of current track'
```

**Optionen:**

- `-e`: Führe eine oder mehrere Codezeilen direkt aus.
- `-l JavaScript`: Verwende JavaScript statt AppleScript

  Beispiel: `osascript -l JavaScript -e 'Application("Finder").open("/Applications")'`

### 6.1    Mitteilungen

Mittels Apple Script ist es auch möglich, Mitteilungen, die rechts oben auf dem Bildschirm erscheinen, auszugeben:

```zsh
osascript -e 'display notification "Hallo!"'
```

### 6.2    Typische Anwendungsfälle

- Automatisierung von Aufgaben von macOS-Apps (z. B. Fenster öffnen, Dateien verschieben)
- Systemdialoge oder Benachrichtigungen aus Skripten heraus anzeigen
- Kombination von Terminal-Befehle mit macOS-Funktionen (z. B. GUI-Interaktionen)
- Integration in Shell-Skripte und Workflows

## 7    Brace Expansion – Effiziente Musterbildung

Brace Expansion ist ein mächtiges Shell-Feature zum schnellen Erzeugen von Mustern und Sequenzen.

### 7.1    Zahlensequenzen

```zsh
echo {1..10}              # 1 2 3 4 5 6 7 8 9 10
echo {01..10}             # 01 02 03 04 05 06 07 08 09 10
echo {10..1}              # Rückwärts: 10 9 8 7 6 5 4 3 2 1
echo {a..z}               # Buchstaben: a b c d ... z
echo {2..20..2}           # Schrittweite: 2 4 6 8 10 12 14 16 18 20
```

### 7.2    Listen

```zsh
echo {rot,grün,blau}      # rot grün blau
mkdir {Docs,Images,Videos}
```

### 7.3    Kombinationen

```zsh
echo {A,B}{1,2}           # A1 A2 B1 B2
mkdir Jahr_{2023,2024}/{Q1,Q2,Q3,Q4}
```

### 7.4    Praktische Anwendungen

**Backup erstellen:**

```zsh
cp wichtig.txt{,.backup}  # Kopiert nach wichtig.txt.backup
```

**Mehrere Dateien auf einmal umbenennen:**

```zsh
mv bild.{jpg,png}         # Umbenennen von jpg zu png
```

**Batch-Download:**

```zsh
curl https://example.com/file{001..100}.jpg -O
```

**Projektstruktur anlegen:**

```zsh
mkdir -p projekt/{src,tests,docs}/{backend,frontend}
```
