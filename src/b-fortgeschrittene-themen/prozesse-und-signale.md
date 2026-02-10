# Prozesse & Signale

Dieses Kapitel behandelt erweiterte Tools zur Prozessüberwachung, das Signal-System zur Prozesskommunikation und die Verwaltung von Prozessgruppen und Jobs.

## 1    Erweiterte Monitoring-Tools

Neben den Standard-Tools `ps`, `top` und `htop` bietet macOS spezialisierte Werkzeuge für detailliertes System-Monitoring.

### 1.1    `iotop` – I/O-Überwachung

macOS hat kein natives `iotop` wie Linux, aber es gibt Alternativen:

**Alternative 1: `sudo iotop` (falls installiert)**

```zsh
# Installation (falls verfügbar)
brew install iotop

# Ausführen (benötigt root)
sudo iotop
```

**Alternative 2: `fs_usage` für I/O-Überwachung**

```zsh
# Disk-I/O aller Prozesse
sudo fs_usage -f diskio

# Bestimmter Prozess
sudo fs_usage -f diskio -w | grep Safari
```

**Alternative 3: `iostat` – Disk-Statistiken**

```zsh
# Disk-I/O Statistiken
iostat

# Alle 2 Sekunden aktualisieren
iostat -w 2

# Detailliert mit Disk-Namen
iostat -d

# Bestimmtes Intervall, 5 Messungen
iostat 2 5
```

**Ausgabe verstehen:**

```
          disk0       cpu    load average
    KB/t  tps  MB/s  us sy id   1m   5m   15m
   24.00   45  1.06   5  3 92  1.23 1.45 1.67
```

| Spalte | Beschreibung |
| ------ | ------------ |
| KB/t | Kilobytes pro Transfer |
| tps | Transfers pro Sekunde |
| MB/s | Megabytes pro Sekunde |
| us/sy/id | CPU: User/System/Idle % |

**Alternative 4: Activity Monitor CLI-Daten**

```zsh
# Top-Prozesse nach Disk-Nutzung
top -o disk -l 1 -n 10 | tail -n +13
```

### 1.2    `vm_stat` – Speicherstatistiken

`vm_stat` zeigt detaillierte Statistiken des virtuellen Speichersystems.

**Grundlegende Nutzung:**

```zsh
# Einmalige Ausgabe
vm_stat

# Ausgabe:
# Mach Virtual Memory Statistics: (page size of 16384 bytes)
# Pages free:                               12345.
# Pages active:                             67890.
# Pages inactive:                           11111.
# Pages speculative:                         2222.
# Pages throttled:                              0.
# Pages wired down:                         33333.
# Pages purgeable:                           4444.
# ...
```

**Kontinuierliche Überwachung:**

```zsh
# Alle 1 Sekunde
vm_stat 1

# Alle 5 Sekunden
vm_stat 5
```

**Ausgabe verstehen:**

| Metrik | Beschreibung |
| ------ | ------------ |
| Pages free | Freie Speicherseiten |
| Pages active | Aktiv genutzter Speicher |
| Pages inactive | Kürzlich ungenutzt, noch im RAM |
| Pages speculative | Spekulativ geladene Daten |
| Pages wired down | Vom Kernel gesperrt (nicht auslagerbar) |
| Pages purgeable | Kann bei Bedarf freigegeben werden |
| Pageins | Von Disk in RAM geladen |
| Pageouts | Von RAM auf Disk ausgelagert |
| Swapins/Swapouts | Swap-Aktivität |

**In lesbare Werte umrechnen:**

```zsh
# Page-Größe ermitteln
pagesize=$(vm_stat | head -1 | grep -oE '[0-9]+')

# Freier Speicher in MB
free_pages=$(vm_stat | grep "Pages free" | awk '{print $3}' | tr -d '.')
echo "Frei: $(( free_pages * pagesize / 1024 / 1024 )) MB"
```

**Skript für lesbare Ausgabe:**

```zsh
#!/bin/zsh
# mem-usage.sh

pagesize=$(pagesize)

get_pages() {
    vm_stat | grep "$1" | awk '{print $NF}' | tr -d '.'
}

free=$(($(get_pages "Pages free") * pagesize / 1024 / 1024))
active=$(($(get_pages "Pages active") * pagesize / 1024 / 1024))
inactive=$(($(get_pages "Pages inactive") * pagesize / 1024 / 1024))
wired=$(($(get_pages "Pages wired") * pagesize / 1024 / 1024))

echo "Speicher-Nutzung:"
echo "  Frei:     ${free} MB"
echo "  Aktiv:    ${active} MB"
echo "  Inaktiv:  ${inactive} MB"
echo "  Wired:    ${wired} MB"
```

### 1.3    `fs_usage` – Dateisystem-Aktivität

`fs_usage` zeigt Dateisystem-Aktivität in Echtzeit – ideal zum Debuggen von I/O-Problemen.

**Grundlegende Nutzung:**

```zsh
# Alle Dateisystem-Aktivität (benötigt root)
sudo fs_usage

# Nur Disk-I/O
sudo fs_usage -f diskio

# Nur Netzwerk
sudo fs_usage -f network

# Nur Dateisystem-Calls
sudo fs_usage -f filesys
```

**Nach Prozess filtern:**

```zsh
# Bestimmter Prozess (Name)
sudo fs_usage -w Safari

# Bestimmte PID
sudo fs_usage -w -p 1234

# Mehrere Prozesse
sudo fs_usage -w Safari Chrome Firefox

# Prozess ausschließen
sudo fs_usage -e Safari
```

**Ausgabe-Optionen:**

```zsh
# Breite Ausgabe (vollständige Pfade)
sudo fs_usage -w

# Mit Timestamps
sudo fs_usage -t

# Kombiniert
sudo fs_usage -w -f diskio -t
```

**Ausgabe verstehen:**

```
14:23:45  RdData     D=0x012345  B=0x1000  /path/to/file   Safari
14:23:45  open       F=3         /path/to/file             Safari
14:23:45  read       F=3  B=0x400                          Safari
14:23:45  close      F=3                                   Safari
```

| Spalte | Beschreibung |
| ------ | ------------ |
| Timestamp | Zeitstempel |
| Operation | read, write, open, close, stat, etc. |
| F= | File Descriptor |
| B= | Bytes |
| D= | Disk Block |
| Pfad | Betroffene Datei |
| Prozess | Prozessname |

**Praktische Anwendungen:**

```zsh
# Was liest/schreibt ein Prozess?
sudo fs_usage -w -f diskio Spotlight

# Welche Dateien öffnet eine App beim Start?
sudo fs_usage -w -f filesys Safari 2>&1 | grep open

# Netzwerkaktivität eines Prozesses
sudo fs_usage -w -f network curl

# In Datei protokollieren
sudo fs_usage -w Safari > fs_log.txt 2>&1 &
# Später: kill %1
```

### 1.4    `nettop` – Netzwerk pro Prozess

`nettop` zeigt Netzwerkverbindungen und Bandbreite pro Prozess – wie `top` für Netzwerk.

**Grundlegende Nutzung:**

```zsh
# Interaktive Ansicht
nettop

# Nicht-interaktiv (einmalig)
nettop -l 1

# Alle 2 Sekunden, 5 Messungen
nettop -l 5 -s 2
```

**Filter-Optionen:**

```zsh
# Nur TCP
nettop -t tcp

# Nur UDP
nettop -t udp

# Bestimmter Prozess
nettop -p Safari

# Nur mit Traffic (Delta-Modus)
nettop -d
```

**Ausgabe-Optionen:**

```zsh
# Nur bestimmte Spalten
nettop -c all       # Alle Spalten
nettop -c bytes_in,bytes_out

# Sortieren
nettop -o bytes_in  # Nach eingehenden Bytes

# JSON-Ausgabe
nettop -j -l 1
```

**Interaktive Tastenkürzel:**

| Taste | Funktion |
| ----- | -------- |
| `q` | Beenden |
| `d` | Delta-Modus (nur Änderungen) |
| `e` | Prozesse ein-/ausklappen |
| `h` | Hilfe |
| `p` | Nach Prozess sortieren |
| `b` | Nach Bytes sortieren |

**Ausgabe verstehen:**

```
                        bytes_in  bytes_out  rx_dupe  ...
Safari.1234               1.2MB     45.6KB       0
  tcp4  192.168.1.100:52341  ->  93.184.216.34:443   512KB   12KB
Chrome.5678               800KB    123.4KB       0
```

**Praktische Beispiele:**

```zsh
# Top-Netzwerknutzer
nettop -l 1 -t wifi | head -20

# Verbindungen eines Prozesses
nettop -p Safari -l 1

# Bandbreite überwachen
watch -n 1 "nettop -l 1 -d | head -15"
```

**Alternativen:**

```zsh
# lsof für Netzwerkverbindungen
lsof -i -n -P

# netstat
netstat -an -p tcp

# Aktivitätsmonitor CLI (Netzwerk-Tab)
nettop -c state,bytes_in,bytes_out -l 1
```

## 2    Signale

Signale sind der primäre Mechanismus zur Kommunikation mit Prozessen unter Unix. Sie können Prozesse beenden, pausieren, fortsetzen oder benutzerdefinierte Aktionen auslösen.

### 2.1    Signal-Übersicht (SIGTERM, SIGKILL, SIGHUP, …)

**Wichtige Signale:**

| Signal | Nummer | Beschreibung | Abfangbar |
| ------ | ------ | ------------ | --------- |
| `SIGHUP` | 1 | Hangup (Terminal geschlossen) | Ja |
| `SIGINT` | 2 | Interrupt (Ctrl+C) | Ja |
| `SIGQUIT` | 3 | Quit mit Core-Dump (Ctrl+\) | Ja |
| `SIGKILL` | 9 | Sofort beenden (nicht abfangbar!) | **Nein** |
| `SIGTERM` | 15 | Sauber beenden (Standard) | Ja |
| `SIGSTOP` | 17 | Prozess anhalten | **Nein** |
| `SIGTSTP` | 18 | Terminal Stop (Ctrl+Z) | Ja |
| `SIGCONT` | 19 | Fortsetzen | Ja |
| `SIGUSR1` | 30 | Benutzerdefiniert 1 | Ja |
| `SIGUSR2` | 31 | Benutzerdefiniert 2 | Ja |
| `SIGCHLD` | 20 | Kind-Prozess beendet | Ja |
| `SIGALRM` | 14 | Timer abgelaufen | Ja |
| `SIGPIPE` | 13 | Schreiben in geschlossene Pipe | Ja |

**Alle Signale anzeigen:**

```zsh
kill -l
# HUP INT QUIT ILL TRAP ABRT EMT FPE KILL BUS SEGV SYS PIPE ALRM TERM ...
```

**Signal-Verhalten:**

| Signal | Typische Reaktion |
| ------ | ----------------- |
| SIGTERM | Sauberes Herunterfahren, Aufräumen |
| SIGKILL | Sofortiger Tod, keine Aufräumarbeiten |
| SIGHUP | Konfiguration neu laden (Daemons) |
| SIGINT | Interaktiver Abbruch |
| SIGSTOP/CONT | Pausieren/Fortsetzen |

**Best Practice:**

```
1. Erst SIGTERM (15) versuchen – gibt Prozess Zeit zum Aufräumen
2. Warten (einige Sekunden)
3. Falls nötig: SIGKILL (9) – als letzter Ausweg
```

### 2.2    `kill` – Signale an Prozesse senden

Trotz des Namens kann `kill` alle Signale senden, nicht nur beenden.

**Syntax:**

```zsh
kill [-SIGNAL] PID...
```

**Grundlegende Nutzung:**

```zsh
# Standard: SIGTERM (15)
kill 1234

# SIGKILL (sofort beenden)
kill -9 1234
kill -KILL 1234

# SIGHUP (Konfiguration neu laden)
kill -1 1234
kill -HUP 1234

# SIGSTOP (pausieren)
kill -STOP 1234

# SIGCONT (fortsetzen)
kill -CONT 1234
```

**Mehrere Prozesse:**

```zsh
# Mehrere PIDs
kill 1234 5678 9012

# Alle Prozesse einer Gruppe
kill -TERM -1234    # Negative PID = Prozessgruppe

# Mit $! (letzter Hintergrundprozess)
sleep 100 &
kill $!
```

**Existenz prüfen:**

```zsh
# Signal 0 prüft nur, ob Prozess existiert
kill -0 1234 && echo "Prozess existiert" || echo "Nicht gefunden"
```

### 2.3    `kill -s` – Signal nach Name

Signale können auch mit `-s` und Namen angegeben werden.

**Syntax:**

```zsh
kill -s SIGNALNAME PID
```

**Beispiele:**

```zsh
# Nach Name
kill -s TERM 1234
kill -s KILL 1234
kill -s HUP 1234

# Äquivalent zu
kill -TERM 1234
kill -15 1234
```

**Alle verfügbaren Signalnamen:**

```zsh
kill -l
# 1) SIGHUP     2) SIGINT     3) SIGQUIT    4) SIGILL
# 5) SIGTRAP    6) SIGABRT    7) SIGEMT     8) SIGFPE
# 9) SIGKILL   10) SIGBUS    11) SIGSEGV   12) SIGSYS
# ...
```

### 2.4    `pkill` – Prozesse nach Name beenden

`pkill` sendet Signale an Prozesse basierend auf Namen oder anderen Kriterien.

**Syntax:**

```zsh
pkill [Optionen] MUSTER
```

**Grundlegende Nutzung:**

```zsh
# Nach Prozessname (Teilmatch)
pkill Safari
pkill -9 Safari      # SIGKILL

# Exakter Name
pkill -x Safari

# Case-insensitive
pkill -i safari
```

**Erweiterte Filter:**

```zsh
# Nach Benutzer
pkill -u max Safari

# Nach Gruppe
pkill -g staff Firefox

# Nach Terminal
pkill -t ttys001

# Ältere als X Sekunden
pkill -o -older 3600 process

# Jüngste Instanz
pkill -n process

# Eltern-PID
pkill -P 1234
```

**Optionen:**

| Option | Beschreibung |
| ------ | ------------ |
| `-SIGNAL` | Signal angeben (-9, -HUP, etc.) |
| `-u USER` | Nach Benutzer |
| `-g GROUP` | Nach Gruppe |
| `-t TTY` | Nach Terminal |
| `-P PPID` | Nach Eltern-PID |
| `-x` | Exakter Name |
| `-i` | Case-insensitive |
| `-n` | Nur neuester Prozess |
| `-o` | Nur ältester Prozess |
| `-f` | Vollständige Kommandozeile matchen |

**Trockenlauf mit `pgrep`:**

```zsh
# Erst prüfen was gematcht wird
pgrep -l Safari
# 1234 Safari

# Dann killen
pkill Safari
```

### 2.5    `killall` – Alle Prozesse eines Namens

`killall` beendet alle Prozesse mit exakt dem angegebenen Namen.

**Syntax:**

```zsh
killall [Optionen] PROZESSNAME
```

**Grundlegende Nutzung:**

```zsh
# Alle Safari-Prozesse beenden
killall Safari

# Mit SIGKILL
killall -9 Safari
killall -KILL Safari

# Mit SIGHUP
killall -HUP nginx
```

**Optionen:**

```zsh
# Interaktiv (Bestätigung)
killall -i Safari

# Verbose
killall -v Safari

# Nach Benutzer
killall -u max Safari

# Ältere als X Sekunden
killall -o 1h Safari    # älter als 1 Stunde
killall -y 5m Safari    # jünger als 5 Minuten

# Regex
killall -r "^Safari"
```

**Optionen-Übersicht:**

| Option | Beschreibung |
| ------ | ------------ |
| `-SIGNAL` | Signal angeben |
| `-v` | Verbose |
| `-i` | Interaktiv (fragen) |
| `-u USER` | Nach Benutzer |
| `-o TIME` | Älter als |
| `-y TIME` | Jünger als |
| `-r` | Regex-Match |
| `-z` | Auch Zombies |

**Unterschied `pkill` vs `killall`:**

| Merkmal | pkill | killall |
| ------- | ----- | ------- |
| Matching | Teilstring/Regex | Exakter Name (Standard) |
| Kommandozeile | Mit `-f` | Nein |
| Filter | Umfangreich | Basis |
| Portabilität | POSIX-ähnlich | BSD/macOS |

**Praktische Beispiele:**

```zsh
# Alle Python-Prozesse
killall python3

# Alle Hintergrund-Jobs des Users
killall -u $(whoami)

# Nginx graceful restart
killall -HUP nginx

# Alle Node.js-Prozesse älter als 1 Tag
killall -o 24h node
```

## 3    Prozessgruppen & Jobs

### 3.1    Vordergrund- und Hintergrundprozesse

**Vordergrundprozess:**

- Blockiert das Terminal
- Empfängt Tastatureingaben
- Ctrl+C sendet SIGINT
- Ctrl+Z sendet SIGTSTP (pausieren)

**Hintergrundprozess:**

- Terminal bleibt nutzbar
- Kein direkter Tastaturzugriff
- Läuft parallel

**Prozess im Hintergrund starten:**

```zsh
# Mit & am Ende
long-running-command &

# Beispiel
sleep 100 &
# [1] 12345   (Job-Nummer und PID)
```

**Jobs verwalten:**

```zsh
# Alle Jobs anzeigen
jobs
# [1]  + running    sleep 100
# [2]  - running    another-command

# Mit PIDs
jobs -p

# Mit Status
jobs -l
```

**Zwischen Vorder- und Hintergrund wechseln:**

```zsh
# Prozess starten
sleep 100

# Pausieren mit Ctrl+Z
^Z
# [1]  + 12345 suspended  sleep 100

# Im Hintergrund fortsetzen
bg
bg %1       # Bestimmter Job

# In den Vordergrund holen
fg
fg %1       # Bestimmter Job
```

**Job-Referenzen:**

| Referenz | Bedeutung |
| -------- | --------- |
| `%1` | Job Nummer 1 |
| `%+` oder `%%` | Aktueller Job |
| `%-` | Vorheriger Job |
| `%string` | Job der mit "string" beginnt |
| `%?string` | Job der "string" enthält |

**Beispiele:**

```zsh
# Drei Jobs starten
sleep 100 &
vim file.txt &
python script.py &

# Jobs anzeigen
jobs
# [1]    running    sleep 100
# [2]  - running    vim file.txt
# [3]  + running    python script.py

# Vim in Vordergrund
fg %2
fg %vi       # Nach Name

# Python beenden
kill %3
kill %?python
```

### 3.2    `nohup` – Prozesse vom Terminal lösen

`nohup` (No Hangup) verhindert, dass ein Prozess beendet wird wenn das Terminal geschlossen wird.

**Grundlegende Nutzung:**

```zsh
# Prozess startet und läuft nach Terminal-Schließung weiter
nohup command &

# Beispiel
nohup ./long-script.sh &
# nohup: ignoring input and appending output to 'nohup.out'
```

**Ausgabe umleiten:**

```zsh
# Standard: Ausgabe geht nach nohup.out
nohup command &

# Eigene Log-Datei
nohup command > output.log 2>&1 &

# Ausgabe verwerfen
nohup command > /dev/null 2>&1 &
```

**Typischer Workflow:**

```zsh
# 1. Prozess mit nohup starten
nohup ./backup.sh > backup.log 2>&1 &

# 2. PID notieren
echo $!
# 12345

# 3. Terminal schließen (Prozess läuft weiter)
exit

# 4. Später: Status prüfen
ps -p 12345
tail -f backup.log
```

**Praktisches Beispiel:**

```zsh
# Langer Download
nohup wget -c https://example.com/large-file.zip > wget.log 2>&1 &

# Server starten
nohup python -m http.server 8000 > server.log 2>&1 &

# Backup-Skript
nohup rsync -avz /source/ /backup/ > rsync.log 2>&1 &
```

### 3.3    `disown` – Jobs aus Shell entfernen

`disown` entfernt einen Job aus der Job-Tabelle der Shell, sodass er nicht beendet wird wenn die Shell beendet wird.

**Unterschied zu nohup:**

| Merkmal | nohup | disown |
| ------- | ----- | ------ |
| Zeitpunkt | Vor dem Start | Nach dem Start |
| SIGHUP ignorieren | Ja | Mit -h |
| Job-Tabelle | Normal | Entfernt |
| Ausgabe | nohup.out | Unverändert |

**Grundlegende Nutzung:**

```zsh
# Job starten
long-command &

# Aus Job-Tabelle entfernen
disown

# Bestimmten Job
disown %1

# SIGHUP ignorieren (Job bleibt in Liste)
disown -h %1

# Alle Jobs
disown -a
```

**Typischer Workflow:**

```zsh
# 1. Prozess gestartet (vergessen nohup zu nutzen)
./script.sh
^Z
bg

# 2. Jetzt disown um ihn zu behalten
disown -h %1

# 3. Shell kann sicher beendet werden
exit
```

**Praktisches Beispiel:**

```zsh
# Server versehentlich ohne nohup gestartet
python server.py &

# Oops! Terminal schließen würde Server beenden
# Lösung: disown
disown -h

# Jetzt sicher Terminal schließen
exit
```

**Nachträglich von Terminal lösen:**

```zsh
# Prozess läuft im Vordergrund
long-running-process

# Ctrl+Z pausiert
^Z
# [1]  + suspended  long-running-process

# Im Hintergrund fortsetzen
bg

# Von Shell lösen
disown -h

# Terminal kann geschlossen werden
```

### 3.4    Prozessgruppen und Session-IDs

**Konzepte:**

```
Session (SID)
    └── Prozessgruppe (PGID)
            └── Prozess (PID)
            └── Prozess (PID)
    └── Prozessgruppe (PGID)
            └── Prozess (PID)
```

| Begriff | Beschreibung |
| ------- | ------------ |
| **PID** | Process ID – eindeutige Prozess-Kennung |
| **PPID** | Parent PID – PID des Elternprozesses |
| **PGID** | Process Group ID – Gruppe zusammengehöriger Prozesse |
| **SID** | Session ID – Login-Session |

**IDs anzeigen:**

```zsh
# Aktuelle IDs
echo "PID: $$"
echo "PPID: $PPID"

# Mit ps
ps -o pid,ppid,pgid,sid,comm

# Für bestimmten Prozess
ps -o pid,ppid,pgid,sid,comm -p 1234
```

**Prozessgruppen verstehen:**

```zsh
# Pipeline erstellt Prozessgruppe
cat file | grep pattern | sort

# Alle drei Prozesse haben gleiche PGID
ps -o pid,pgid,comm
# PID   PGID  COMM
# 1001  1001  cat
# 1002  1001  grep
# 1003  1001  sort
```

**Signal an Prozessgruppe senden:**

```zsh
# Negative PID = Prozessgruppe
kill -TERM -1001

# Alle Prozesse der Gruppe beenden
kill -9 -$(ps -o pgid= -p $PID)
```

**Sessions verstehen:**

- Jedes Terminal startet eine neue Session
- Session-Leader ist meist die Shell
- Alle von der Shell gestarteten Prozesse gehören zur Session

```zsh
# Neue Session erstellen (für Daemons)
setsid command

# Prozess von Terminal lösen
setsid ./daemon.sh
```

**Prozessbaum anzeigen:**

```zsh
# Mit pstree (falls installiert)
brew install pstree
pstree

# Mit ps
ps -axjf

# Kinder eines Prozesses
pgrep -P 1234
ps --ppid 1234
```

**Waisen- und Zombie-Prozesse:**

| Typ | Beschreibung | Status |
| --- | ------------ | ------ |
| **Waise** | Elternprozess beendet, init übernimmt | Normal |
| **Zombie** | Beendet, aber Eltern hat Exit-Code nicht gelesen | `Z` in ps |

```zsh
# Zombies finden
ps aux | awk '$8=="Z"'

# Oder
ps -eo pid,ppid,stat,comm | grep Z
```

**Zombie-Prozesse bereinigen:**

Zombies können nicht direkt beendet werden. Lösungen:

```zsh
# 1. SIGCHLD an Elternprozess
kill -SIGCHLD $(ps -o ppid= -p $ZOMBIE_PID)

# 2. Elternprozess beenden (Zombie wird von init übernommen)
kill $(ps -o ppid= -p $ZOMBIE_PID)

# 3. Im schlimmsten Fall: Neustart
```

**Daemon-Muster (doppelter Fork):**

```zsh
#!/bin/zsh
# daemon.sh - Prozess vollständig vom Terminal lösen

(
    # Erster Fork
    cd /
    umask 0
    exec setsid ./actual-daemon.sh &
) &

# Shell kann sofort beenden
exit 0
```

**Zusammenfassung Job-Control:**

```
# Starten
command &                    # Im Hintergrund
nohup command &              # Immun gegen SIGHUP

# Kontrolle
jobs                         # Jobs anzeigen
fg %n                        # In Vordergrund
bg %n                        # Im Hintergrund fortsetzen
Ctrl+Z                       # Pausieren
Ctrl+C                       # Abbrechen

# Loslösen
disown %n                    # Aus Job-Liste entfernen
disown -h %n                 # SIGHUP ignorieren

# Beenden
kill %n                      # Job beenden
kill -9 %n                   # Erzwungen
```
