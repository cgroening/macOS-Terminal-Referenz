# Die wichtigsten Befehle

## 1    Navigation

| Befehl    | Beschreibung                |
| --------- | --------------------------- |
| `pwd`     | Zeigt aktuelles Verzeichnis |
| `ls`      | Listet Dateien/Ordner       |
| `cd`      | Wechselt Verzeichnis        |
| `history` | Zeigt den Befehlsverlauf an |

### 1.1    `ls`-Befehl

```zsh
ls -l    # Detaillierte Liste mit Berechtigungen, Datum, Größe, ...
ls -a    # Zeigt auch versteckte Dateien und Ordner an
ls -la   # Kombination: Detaillierte Liste inkl. versteckter Elemente
ls -lh   # Liste mit Größenangaben in passender Einheit (KB, MB, GB)
ls -lah  # Kombination
ls -lt   # Sortierung nach Änderungsdatum
ls -lS   # Sortierung nach Dateigröße
ls -l <pfad>  # Anzeigen von Dateien/Ordner des gegebenen Pfads
```

### 1.2    `cd`-Befehl

```zsh
cd Documents    # Wechselt in den Unterordner Documents
cd ~/Downloads  # Wechselt in den Ordner Downloads im Homeverzeichnis
cd /      # Wechselt in Hauptverzeichnis
cd ~      # Wechselt in das Homeverzeichnis
cd ..     # Wechselt ins Eltern-Verzeichnis
cd ../..  # Geht zwei Ebenen nach oben
cd -      # Springt zum letzten Verzeichnis zurück
```

> [!NOTE]
> **Unterschied zwischen `~` und `$HOME`**
>
> Die Tilde (`~`) ist eine Shell-Abkürzung für das Home-Verzeichnis, während `$HOME` eine Umgebungsvariable ist, die von Programmen und Skripten verwendet wird. Beide zeigen auf das Home-Verzeichnis des aktuell angemeldeten Benutzers, sind jedoch unterschiedlich implementiert.

Siehe auch: [[../SUMMARY#4.2.2 ~ vs. $HOME|~ vs. $HOME]]

### 1.3    `history`-Befehl

```zsh
history               # Zeigt die letzten ausgeführten Befehle an
history | grep "cal"  # Zeigt die letzten Befehle an, passen zum Suchbegriff
!123                  # Führt Befehl 123 erneut aus
!!                    # Führt den letzten Befehl erneut aus
```

## 2    Dateien und Ordner

| Befehl  | Beschreibung                | Beispiel                    |
| ------- | --------------------------- | --------------------------- |
| `touch` | Erstellt leere Datei(ein)   | `touch test1.txt test2.txt` |
| `mkdir` | Erstellt Verzeichnis(se)    | `mkdir Projekte Archiv`     |
| `cp`    | Kopiert Datei/Ordner        | `cp quelle.txt ziel.txt`    |
| `mv`    | Verschiebt oder benennt um  | `mv alt.txt neu.txt`        |
| `rm`    | Löscht Datei(en) und Ordner | `rm test1.txt test2.txt`    |
| `rm -r` | Löscht Verzeichnis rekursiv | `rm -r Ordner`              |

> [!danger] Achtung
> Der Befehl `rm` löscht unwiderruflich – **ohne Papierkorb**!
>
> Soll das Löschen bestätigt werden, muss der interaktive Modus verwendet werden:
>
> ```zsh
>rm -i test.txt
>```

> [!TIP]
> Bevor mehrere Dateien gelöscht werden, sollen die Auswirkungen des zugehörigen Befehls geprüft werden. Beispiel:
>
> ```zsh
> ls *.py
> rm *.py
> ```

### 2.1    `mkdir`-Befehl

```zsh
mkdir NeuerOrdner          # Erstellt einen Ordner
mkdir Ordner1 Ordner2      # Erstellt mehrere Ordner
mkdir -p Fotos/2025/10/29  # Erstellt Ordner + Unterordner
```

**Brace Expansion für effiziente Stapelerstellung:**

Geschweifte Klammern ermöglichen die schnelle Erstellung mehrerer ähnlicher Elemente:

```zsh
mkdir Tag_{01..03}           # Erstellt: Tag_01, Tag_02, Tag_03
mkdir {Januar,Februar,März}  # Erstellt drei Monatsordner
touch datei_{a,b,c}.txt      # Erstellt: datei_a.txt, datei_b.txt, datei_c.txt
echo {01..05}                # Zeigt: 01 02 03 04 05
cp dokument.txt{,.backup}    # Kopiert nach dokument.txt.backup
```

Kombinationen sind ebenfalls möglich:

```zsh
mkdir -p Projekt/{src,tests,docs}/{python,rust}
# Erstellt: Projekt/src/python, Projekt/src/rust,
#           Projekt/tests/python, Projekt/tests/rust,
#           Projekt/docs/python, Projekt/docs/rust
```

### 2.2    `cp`- und `mv`-Befehl

```zsh
cp quelle.txt ziel.txt     # Kopiert eine Datei
cp -i quelle.txt ziel.txt  # Fragt nach bevor überschrieben wird
cp -r Ordner1 Ordner2      # Kopiert einen kompletten Ordner (rekursiv)
cp *.txt Text              # Kopiert alle Dateien mit der Endung .txt
```

Gleiche Syntax beim `mv`-Befehl.

## 3    Arbeiten mit Texten und Dateien

| Befehl          | Funktion                                   | Beispiel                    |
| --------------- | ------------------------------------------ | --------------------------- |
| `cat`           | Zeigt den Inhalt von Dateien (ohne Editor) | `cat datei1.txt datei2.txt` |
| `less`          | Scrollbare Ansicht                         | `less datei.txt`            |
| `head / tail`   | Zeigt Anfang/Ende                          | `head -n 20 datei.txt`      |
| `wc`            | Zählt Zeilen, Wörter, Zeichen              | `wc -l datei.txt`           |
| `diff`          | Vergleicht Dateien                         | `diff a.txt b.txt`          |
| `find`          | Sucht Dateien                              | `find . -name "*.txt"`      |
| `nano`          | Datei im Texteditor im Terminal öffnen     | `nano datei.txt`            |
| `grep`          | Sucht nach Textmustern in Dateien          | `grep "Fehler" log.txt`     |
| `open`          | Öffnet Datei/Ordner im Finder              | `open .`                    |
| `open -a <App>` | Öffnet Datei/Ordner in bestimmter App      | `open -a ForkLift .`        |

Alternative zu `less datei.txt`:
`cat datei.txt | more`

### 3.1    Navigation bei Anzeige über `less`-Befehl

- **Leertaste:** Nächste Seite
- **`b`:** Vorherige Seite
- **`/`:** Vorwärts suchen
- **`?`:** Rückwärts suchen
- **`q`:** Beenden

### 3.2    `head`- und `tail`-Befehl

```zsh
head datei.txt        # Zeigt die ersten 20 Zeilen
head -n 20 datei.txt  # Zeigt die ersten 20 Zeilen
tail datei.txt        # Zeigt die letzten 10 Zeilen
tail -n 20 datei.txt  # Zeigt die letzten 20 Zeilen
```

### 3.3    Dateiinhalt in Echtzeit anzeigen

Änderungen in Echtzeit können mit folgendem Befehl verfolgt werden:

```zsh
tail -f info.log
```

Dies funktioniert jedoch nicht immer. Viele Editoren speichern Änderungen nicht direkt in der bestehenden Datei, sondern erstellen eine temporäre Datei, schreiben den neuen Inhalt hinein, löschen die alte Datei und benennen die neue Datei auf den alten Namen um. Dabei ändert sich die inode (Datei-Identität im Dateisystem). Abhilfe schafft `-F` (*follow name*). Es wird erkannt, wenn die Datei ersetzt wurde und folgt automatisch der neuen Datei.

```zsh
tail -F info.log
```

Hierbei wird jedoch immer der neue Dateiinhalt unter den alten geschrieben. Dies wird schnell unübersichtlich. Eine bessere Lösung ist mit dem Befehl `watch` möglich. Diese muss ggf. noch installiert werden:

```zsh
brew install watch
```

Anschließend kann der Dateiinhalt wie folgt überwacht werden:

```zsh
watch -n 1 "clear && cat info.log"
```

- `-n 1`: alle 1 Sekunden aktualisieren
- `clear`: Ausgabe löschen
- `cat test.txt`: Dateiinhalt anzeigen

Nutzt man dies öfters, empfiehlt sich die Erstellung eines [[../SUMMARY#4 1 Aliase|Alias]]:

```
alias watchfile='watch -n 1 "clear && cat $1"'
```

Verwendung mit:

```zsh
watchfile info.log
```

### 3.4    `find`-Befehl

```zsh
find . -name "*.txt"          # Findet alle .txt-Dateien
find . -name "rechnung.pdf"   # Findet die Datei rechnung.pdf
find . -type f -size +5M      # Findet Dateien größer als 5 MB
find . -name "*.*" -mtime -7  # Dateien, die in den letzten 7 Tagen verändert wurden
find . -empty                 # Findet leere Dateien und Ordner
```

### 3.5    `grep`-Befehl

```zsh
grep "Januar" notiz.txt     # Sucht in einer Datei
grep -r "Januar" notiz.txt  # Sucht rekursiv in einem Ordner
grep -n "Januar" notiz.txt  # Zeigt alle Zeilen inkl. Nr. mit dem Suchwort
grep -c "Januar" notiz.txt  # Zählt die Anzahl der Vorkommen des Suchworts
grep -i "Januar" notiz.txt  # Berücksichtigt Groß- und Kleinschreibung
```

### 3.6    Prozesse, Systeme und Netzwerk

### 3.7    Prozesse

| Befehl    | Beschreibung                                  | Beispiel         |
| --------- | --------------------------------------------- | ---------------- |
| `top`     | Live-Systemüberwachung                        | `top`            |
| `htop`    | Live-Systemüberwachung (interaktiv, in Farbe) | `htop`           |
| `ps`      | Zeigt aktive Prozesse                         | `ps aux`         |
| `kill`    | Beendet Prozess                               | `kill 1234`      |
| `killall` | Beendet alle Prozesse eines Namens            | `killall Safari` |

### 3.8    `top`-Befehl

**Kurzbefehle:**

- **`o`**: Nach anderer Spalte sortieren (`o` drücken und Spaltenname eingeben)
- **`q`**: Beenden

**Alternative: `htop`**

`htop` liefert eine interaktive Anzeige in Farbe.

Installation:

```zsh
brew install htop
```
### 3.9    `ps`-Befehl

```zsh
ps                    # Zeigt die eigenen Prozesse
ps aux                # Zeigt alle Prozesse mit Details
ps aux | grep Safari  # Zeigt bestimmte Prozesse
```

### 3.10    System und Hardware

| Befehl        | Beschreibung                                    |
| ------------- | ----------------------------------------------- |
| `df -h`       | Speicherplatz aller Laufwerke anzeigen          |
| `du -sh *`    | Größe von Ordnern anzeigen                      |
| `uname`       | Anzeige von Systeminformationen                 |
| `uptime`      | Zeigt Laufzeit und Systemlast                   |
| `whoami`      | Aktueller Benutzer                              |
| `date`        | Aktuelles Datum/Zeit                            |
| `cal`         | Zeigt einen Kalender an                         |
| `say "Hallo"` | macOS sagt Text laut (praktisch für Testzwecke) |

### 3.11    `du`-Befehl

```zsh
du -sh * | sort -h   # Größe von Ordnern und Dateien aufsteigend sortiert
du -sh * | sort -hr  # Absteigend sortiert
du -sh * | sort -hr | head -5  # Beschränkung auf die 5 größten Elemente
```

### 3.12    `uname`-Befehl

```zsh
uname     # Zeigt den Kernelnamen an (i. d. R. "Darwin")
uname -a  # Alle verfügbaren Systeminformationen
uname -s  # Gleiche ausgabe wie bei uname
uname -r  # Kernel-Version
uname -v  # Kernel-Buildversion
uname -m  # Machine hardware name
uname -n  # Rechnername (Hostname)
uname -p  # Prozessortyp (machmal leer)
uname -o  # Betriebssystem
```

### 3.13    `date`-Befehl

```zsh
date              # Zeigt Datum+Zeit; z. B.: Thu Oct 30 21:53:42 CET 2025
date "+%Y-%m-%d"  # Zeigt Datum, z. B.: 2025-10-30
date "+%H:%M:%S"  # Zeigt Uhrzeit, z. B.: 21:53:42
```

### 3.14    `cal`-Befehl

```zsh
cal          # Aktueller Monat
cal 2026     # Bestimmtes Jahr
cal 01 2026  # Bestimmter Monat
```

Beispielausgabe:

```
    January 2026
Su Mo Tu We Th Fr Sa
             1  2  3
 4  5  6  7  8  9 10
11 12 13 14 15 16 17
18 19 20 21 22 23 24
25 26 27 28 29 30 31
```

### 3.15    Netzwerk

| Befehl     | Beschreibung                 | Beispiel                       |
| ---------- | ---------------------------- | ------------------------------ |
| `ping`     | Testet Verbindung            | `ping google.com`              |
| `ifconfig` | Zeigt Netzwerkschnittstellen | `ifconfig`                     |
| `curl`     | HTTP-Anfragen senden         | `curl https://example.com`     |
| `scp`      | Kopiert Dateien über SSH     | `scp file.txt user@host:/path` |
| `ssh`      | Verbindung zu Server         | `ssh user@ip`                  |

Lokale IP-Adresse(n) anzeigen:

```zsh
ifconfig | grep "inet "
```
### 3.16    `curl`-Befehl

`curl` ist ein vielseitiges Tool zum Abrufen oder Senden von Daten über HTTP, HTTPS, FTP und viele andere Protokolle. Es eignet sich sowohl zum einfachen Testen von Webseiten als auch für API-Aufrufe.

**Beispiele:**

```zsh
# Webseite abrufen und im Terminal anzeigen
curl https://example.com

# Datei herunterladen
curl -O https://example.com/datei.zip

# API-Aufruf mit Header
curl -H "Authorization: Bearer TOKEN" https://api.example.com/data
```

**Häufig verwendete Optionen:**

- `-I`: nur Header anzeigen
- `-L`: Redirects folgen
- `-d`: POST-Daten senden

## 4    Job-Control (`fg`, `bg`, `jobs`, `&`)

Job-Control ermöglicht die Verwaltung von Prozessen, die im Hintergrund oder Vordergrund laufen. Dies ist besonders nützlich bei langläufigen Aufgaben oder wenn man mehrere Programme parallel ausführen möchte.

### 4.1    Grundlegende Konzepte

- **Vordergrundprozess (Foreground):**
  Prozess, der gerade aktiv ist und das Terminal blockiert

- **Hintergrundprozess (Background):**
  Prozess, der im Hintergrund läuft, während das Terminal weiter nutzbar bleibt

- **Job:**
  Eine vom Terminal gestartete Aufgabe (kann mehrere Prozesse enthalten)

### 4.2    Befehle

|Befehl|Beschreibung|
|---|---|
|`jobs`|Zeigt alle Jobs der aktuellen Shell|
|`fg`|Holt einen Job in den Vordergrund|
|`bg`|Setzt einen angehaltenen Job im Hintergrund fort|
|`&`|Startet einen Befehl direkt im Hintergrund|
|`STRG + Z`|Hält den aktuellen Vordergrundprozess an|

### 4.3    Beispiele

**Prozess im Hintergrund starten:**

```zsh
python script.py &
```

Das `&` am Ende startet den Befehl sofort im Hintergrund. Das Terminal bleibt nutzbar.

**Laufenden Prozess anhalten und in Hintergrund verschieben:**

```zsh
python script.py
# STRG + Z drücken (Prozess wird angehalten)
bg
```

**Alle Jobs anzeigen:**

```zsh
jobs
```

Beispielausgabe:

```
[1]  + running    python script.py
[2]  - suspended  vim dokument.txt
````

**Bestimmten Job in den Vordergrund holen:**

```zsh
fg %1    # Job 1 in den Vordergrund
fg %2    # Job 2 in den Vordergrund
fg       # Letzten Job in den Vordergrund
```

**Hintergrundprozess beenden:**

```zsh
kill %1  # Beendet Job 1
```

### 4.4    Praktische Anwendungsfälle

**Mehrere Editoren gleichzeitig:**

```zsh
vim config.txt &
vim script.py &
jobs
```

**Langläufige Aufgabe im Hintergrund:**

```zsh
find / -name "*.log" > logs.txt 2>&1 &
```

**Zwischen mehreren Aufgaben wechseln:**

```zsh
python server.py
# STRG + Z
bg
vim main.py
# STRG + Z
jobs
fg %1  # Zurück zum Server
```
