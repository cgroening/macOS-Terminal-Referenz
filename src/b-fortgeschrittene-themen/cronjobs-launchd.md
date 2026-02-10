# Cronjobs & launchd

Automatisierte Aufgaben sind essentiell für Systemadministration, Backups, Wartung und wiederkehrende Prozesse. Unter macOS gibt es zwei Systeme: das klassische Unix-Tool **cron** und Apples eigenes **launchd**.

## 1    Cron-Grundlagen

`cron` ist der klassische Unix-Dienst für zeitgesteuerte Aufgaben. Er stammt aus den 1970er Jahren und ist auf praktisch jedem Unix/Linux-System verfügbar.

### 1.1    Wie cron funktioniert

- Der `cron`-Daemon läuft im Hintergrund
- Er prüft jede Minute, ob geplante Aufgaben anstehen
- Aufgaben werden in **Crontabs** (cron tables) definiert
- Jeder Benutzer kann eine eigene Crontab haben

### 1.2    Crontab verwalten

| Befehl | Beschreibung |
| ------ | ------------ |
| `crontab -e` | Crontab bearbeiten |
| `crontab -l` | Crontab anzeigen |
| `crontab -r` | Crontab löschen |
| `crontab -u user -e` | Crontab eines anderen Benutzers bearbeiten (root) |

**Erste Verwendung:**

```zsh
crontab -e
```

Dies öffnet den Standard-Editor (meist `vim` oder `nano`). Um den Editor zu ändern:

```zsh
export EDITOR=nano
crontab -e
```

### 1.3    Speicherorte

| Pfad | Beschreibung |
| ---- | ------------ |
| `/var/at/tabs/` | Benutzer-Crontabs (macOS) |
| `/etc/crontab` | System-Crontab |
| `/etc/cron.d/` | Zusätzliche System-Crontabs |
| `/etc/cron.daily/` | Täglich ausgeführte Skripte |
| `/etc/cron.weekly/` | Wöchentlich ausgeführte Skripte |
| `/etc/cron.monthly/` | Monatlich ausgeführte Skripte |

> [!WARNING]
> Unter macOS existieren die `/etc/cron.*`-Verzeichnisse standardmäßig nicht, da Apple `launchd` bevorzugt.

## 2    Cron-Syntax & Beispiele

### 2.1    Grundsyntax

Eine Crontab-Zeile hat folgendes Format:

```
┌───────────── Minute (0-59)
│ ┌───────────── Stunde (0-23)
│ │ ┌───────────── Tag des Monats (1-31)
│ │ │ ┌───────────── Monat (1-12)
│ │ │ │ ┌───────────── Wochentag (0-7, 0 und 7 = Sonntag)
│ │ │ │ │
* * * * * Befehl
```

### 2.2    Sonderzeichen

| Zeichen | Bedeutung | Beispiel |
| ------- | --------- | -------- |
| `*` | Jeder Wert | `* * * * *` = jede Minute |
| `,` | Liste von Werten | `1,15,30` = Minute 1, 15 und 30 |
| `-` | Bereich | `1-5` = Montag bis Freitag |
| `/` | Schrittweite | `*/15` = alle 15 Einheiten |

### 2.3    Beispiele

**Zeitangaben:**

| Ausdruck | Bedeutung |
| -------- | --------- |
| `* * * * *` | Jede Minute |
| `0 * * * *` | Jede Stunde (zur vollen Stunde) |
| `0 0 * * *` | Täglich um Mitternacht |
| `0 6 * * *` | Täglich um 6:00 Uhr |
| `30 8 * * 1-5` | Mo–Fr um 8:30 Uhr |
| `0 0 * * 0` | Jeden Sonntag um Mitternacht |
| `0 0 1 * *` | Am 1. jeden Monats um Mitternacht |
| `0 0 1 1 *` | Am 1. Januar um Mitternacht |
| `*/15 * * * *` | Alle 15 Minuten |
| `0 */2 * * *` | Alle 2 Stunden |
| `0 9-17 * * 1-5` | Mo–Fr, stündlich von 9–17 Uhr |

**Vollständige Crontab-Beispiele:**

```cron
# Backup jeden Tag um 2:00 Uhr
0 2 * * * /Users/max/scripts/backup.sh

# Log-Rotation jeden Sonntag um 3:00 Uhr
0 3 * * 0 /Users/max/scripts/rotate-logs.sh

# Alle 5 Minuten: System-Check
*/5 * * * * /Users/max/scripts/health-check.sh

# Werktags um 9:00: Bericht generieren
0 9 * * 1-5 /Users/max/scripts/daily-report.sh

# Am 1. und 15. jeden Monats
0 0 1,15 * * /Users/max/scripts/bi-monthly.sh

# Nur im Januar, täglich um 6:00
0 6 * 1 * /Users/max/scripts/january-task.sh
```

### 2.4    Spezielle Strings

Einige cron-Implementierungen unterstützen lesbare Kürzel:

| String | Äquivalent | Bedeutung |
| ------ | ---------- | --------- |
| `@reboot` | – | Bei Systemstart |
| `@yearly` | `0 0 1 1 *` | Jährlich |
| `@monthly` | `0 0 1 * *` | Monatlich |
| `@weekly` | `0 0 * * 0` | Wöchentlich |
| `@daily` | `0 0 * * *` | Täglich |
| `@hourly` | `0 * * * *` | Stündlich |

```cron
@daily /Users/max/scripts/backup.sh
@reboot /Users/max/scripts/startup.sh
```

**macOS-Kompatibilität:**

macOS verwendet Vixie cron (BSD-basiert). Die Unterstützung der speziellen Strings:

| String | macOS | Anmerkung |
| ------ | ----- | --------- |
| `@yearly` | ✅ | Funktioniert |
| `@monthly` | ✅ | Funktioniert |
| `@weekly` | ✅ | Funktioniert |
| `@daily` | ✅ | Funktioniert |
| `@hourly` | ✅ | Funktioniert |
| `@reboot` | ❌ | **Nicht zuverlässig** |

**Problem mit `@reboot`:** Unter macOS startet der cron-Daemon erst nach dem Benutzer-Login, nicht beim Systemstart. Jobs mit `@reboot` werden daher nicht ausgeführt oder nur beim ersten Login nach einem Neustart.

**Lösung für Startup-Tasks:** Verwende stattdessen launchd mit `RunAtLoad`:

```xml
<key>RunAtLoad</key>
<true/>
```

Siehe [[Terminal-Referenz für macOS - Teil 2 - Fortgeschrittene Themen#3.4 Erstellen von .plist-Jobs|Erstellung von .plist-Jobs]] für Details.

### 2.5    Umgebungsvariablen

Cron führt Befehle mit einer minimalen Umgebung aus. Wichtige Variablen sollten definiert werden:

```cron
# Umgebungsvariablen setzen
SHELL=/bin/zsh
PATH=/usr/local/bin:/usr/bin:/bin:/opt/homebrew/bin
HOME=/Users/max
MAILTO=max@example.com

# Jobs
0 * * * * /Users/max/scripts/task.sh
```

**Wichtig:** Der `PATH` in cron ist sehr eingeschränkt. Entweder:

1. Vollständige Pfade zu Programmen verwenden
2. `PATH` in der Crontab setzen
3. `PATH` im Skript selbst setzen

```zsh
#!/bin/zsh
# Am Anfang des Skripts:
export PATH="/opt/homebrew/bin:/usr/local/bin:$PATH"
```

### 2.6    Ausgabe und Logging

Standardmäßig wird die Ausgabe per E-Mail gesendet (falls konfiguriert). Alternativen:

```cron
# Ausgabe in Datei
0 * * * * /script.sh >> /var/log/script.log 2>&1

# Ausgabe verwerfen
0 * * * * /script.sh > /dev/null 2>&1

# Nur Fehler loggen
0 * * * * /script.sh >> /var/log/script.log 2>&1

# Mit Zeitstempel
0 * * * * /script.sh 2>&1 | while read line; do echo "$(date): $line"; done >> /var/log/script.log
```

**Logging-Wrapper-Skript:**

```zsh
#!/bin/zsh
# /Users/max/scripts/run-with-log.sh

LOGFILE="/var/log/cron-jobs.log"
SCRIPT="$1"
shift

echo "=== $(date '+%Y-%m-%d %H:%M:%S') - Start: $SCRIPT ===" >> "$LOGFILE"
"$SCRIPT" "$@" >> "$LOGFILE" 2>&1
EXIT_CODE=$?
echo "=== $(date '+%Y-%m-%d %H:%M:%S') - Ende: $SCRIPT (Exit: $EXIT_CODE) ===" >> "$LOGFILE"
echo "" >> "$LOGFILE"
```

Verwendung in Crontab:

```cron
0 * * * * /Users/max/scripts/run-with-log.sh /Users/max/scripts/task.sh
```

### 2.7    Häufige Probleme

**1. Skript läuft nicht:**

```zsh
# Skript ausführbar machen
chmod +x /Users/max/scripts/script.sh

# Shebang prüfen
head -1 /Users/max/scripts/script.sh
# Sollte sein: #!/bin/zsh oder #!/bin/bash
```

**2. Befehl nicht gefunden:**

```cron
# FALSCH: brew ist nicht im PATH
0 * * * * brew update

# RICHTIG: Vollständiger Pfad
0 * * * * /opt/homebrew/bin/brew update
```

**3. Berechtigungsprobleme:**

```zsh
# cron-Logs prüfen (macOS)
log show --predicate 'subsystem == "com.apple.cron"' --last 1h
```

## 3    launchd unter macOS

`launchd` ist Apples Init-System und Dienst-Manager. Es ist leistungsfähiger als cron und der empfohlene Weg für geplante Aufgaben unter macOS.

### 3.1    Konzepte

**Agents vs. Daemons:**

| Typ | Läuft als | Speicherort | Beschreibung |
| --- | --------- | ----------- | ------------ |
| **User Agent** | Aktueller Benutzer | `~/Library/LaunchAgents/` | Benutzer-spezifische Aufgaben |
| **Global Agent** | Aktueller Benutzer | `/Library/LaunchAgents/` | Für alle Benutzer, aber im Benutzerkontext |
| **Global Daemon** | root | `/Library/LaunchDaemons/` | Systemweite Dienste |
| **System Daemon** | root | `/System/Library/LaunchDaemons/` | macOS-Systemdienste (nicht bearbeiten!) |

**Für die meisten Benutzeraufgaben:** `~/Library/LaunchAgents/`

### 3.2    launchctl – Das Verwaltungstool

| Befehl | Beschreibung |
| ------ | ------------ |
| `launchctl list` | Alle geladenen Jobs anzeigen |
| `launchctl list | grep LABEL` | Bestimmten Job suchen |
| `launchctl load FILE.plist` | Job laden und aktivieren |
| `launchctl unload FILE.plist` | Job deaktivieren und entladen |
| `launchctl start LABEL` | Job sofort ausführen |
| `launchctl stop LABEL` | Job stoppen |
| `launchctl kickstart gui/$(id -u)/LABEL` | Job neu starten (moderne Syntax) |

**Moderne Syntax (ab macOS 10.10):**

```zsh
# Benutzer-Agent laden
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.user.task.plist

# Benutzer-Agent entladen
launchctl bootout gui/$(id -u)/com.user.task

# System-Daemon laden (als root)
sudo launchctl bootstrap system /Library/LaunchDaemons/com.company.daemon.plist
```

### 3.3    Job-Status prüfen

```zsh
# Alle Jobs auflisten
launchctl list

# Bestimmten Job finden
launchctl list | grep -i backup

# Job-Details anzeigen (moderne Syntax)
launchctl print gui/$(id -u)/com.user.backup

# Fehler prüfen
launchctl error <error_code>
```

## 4    Erstellung von `.plist`-Jobs

Property List (`.plist`) Dateien definieren launchd-Jobs im XML-Format.

### 4.1    Grundstruktur

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.benutzername.jobname</string>

    <key>ProgramArguments</key>
    <array>
        <string>/pfad/zum/skript.sh</string>
    </array>

    <!-- Zeitplan oder Auslöser hier -->

</dict>
</plist>
```

### 4.2    Wichtige Schlüssel

**Identifikation:**

| Schlüssel | Typ | Beschreibung |
| --------- | --- | ------------ |
| `Label` | String | Eindeutiger Bezeichner (erforderlich) |
| `Program` | String | Auszuführendes Programm |
| `ProgramArguments` | Array | Programm + Argumente |

**Zeitsteuerung:**

| Schlüssel | Typ | Beschreibung |
| --------- | --- | ------------ |
| `StartInterval` | Integer | Intervall in Sekunden |
| `StartCalendarInterval` | Dict/Array | Kalenderbasierte Ausführung |

**Auslöser:**

| Schlüssel | Typ | Beschreibung |
| --------- | --- | ------------ |
| `RunAtLoad` | Boolean | Bei Laden ausführen |
| `WatchPaths` | Array | Bei Dateiänderung ausführen |
| `QueueDirectories` | Array | Ausführen wenn Dateien im Ordner |
| `StartOnMount` | Boolean | Bei Volume-Mount ausführen |

**Umgebung:**

| Schlüssel | Typ | Beschreibung |
| --------- | --- | ------------ |
| `WorkingDirectory` | String | Arbeitsverzeichnis |
| `EnvironmentVariables` | Dict | Umgebungsvariablen |
| `UserName` | String | Benutzer (nur Daemons) |
| `GroupName` | String | Gruppe (nur Daemons) |

**Ausgabe:**

| Schlüssel | Typ | Beschreibung |
| --------- | --- | ------------ |
| `StandardOutPath` | String | Stdout in Datei |
| `StandardErrorPath` | String | Stderr in Datei |

**Verhalten:**

| Schlüssel | Typ | Beschreibung |
| --------- | --- | ------------ |
| `KeepAlive` | Boolean/Dict | Prozess am Leben halten |
| `ThrottleInterval` | Integer | Mindestzeit zwischen Neustarts |
| `Disabled` | Boolean | Job deaktivieren |

### 4.3    StartCalendarInterval

Ähnlich wie cron-Syntax, aber als Dictionary:

| Schlüssel | Wertebereich |
| --------- | ------------ |
| `Minute` | 0–59 |
| `Hour` | 0–23 |
| `Day` | 1–31 |
| `Weekday` | 0–7 (0 und 7 = Sonntag) |
| `Month` | 1–12 |

**Beispiele:**

```xml
<!-- Täglich um 6:30 -->
<key>StartCalendarInterval</key>
<dict>
    <key>Hour</key>
    <integer>6</integer>
    <key>Minute</key>
    <integer>30</integer>
</dict>

<!-- Jeden Montag um 9:00 -->
<key>StartCalendarInterval</key>
<dict>
    <key>Weekday</key>
    <integer>1</integer>
    <key>Hour</key>
    <integer>9</integer>
    <key>Minute</key>
    <integer>0</integer>
</dict>

<!-- Mehrere Zeitpunkte (Array) -->
<key>StartCalendarInterval</key>
<array>
    <dict>
        <key>Hour</key>
        <integer>9</integer>
        <key>Minute</key>
        <integer>0</integer>
    </dict>
    <dict>
        <key>Hour</key>
        <integer>17</integer>
        <key>Minute</key>
        <integer>0</integer>
    </dict>
</array>
```

### 4.4    Vollständige Beispiele

**Beispiel 1: Tägliches Backup um 2:00 Uhr**

Datei: `~/Library/LaunchAgents/com.user.backup.plist`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.user.backup</string>

    <key>ProgramArguments</key>
    <array>
        <string>/Users/max/scripts/backup.sh</string>
    </array>

    <key>StartCalendarInterval</key>
    <dict>
        <key>Hour</key>
        <integer>2</integer>
        <key>Minute</key>
        <integer>0</integer>
    </dict>

    <key>StandardOutPath</key>
    <string>/Users/max/logs/backup.log</string>

    <key>StandardErrorPath</key>
    <string>/Users/max/logs/backup-error.log</string>

    <key>WorkingDirectory</key>
    <string>/Users/max</string>

    <key>EnvironmentVariables</key>
    <dict>
        <key>PATH</key>
        <string>/opt/homebrew/bin:/usr/local/bin:/usr/bin:/bin</string>
    </dict>
</dict>
</plist>
```

**Beispiel 2: Alle 30 Minuten ausführen**

Datei: `~/Library/LaunchAgents/com.user.sync.plist`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.user.sync</string>

    <key>ProgramArguments</key>
    <array>
        <string>/Users/max/scripts/sync.sh</string>
    </array>

    <key>StartInterval</key>
    <integer>1800</integer>  <!-- 30 Minuten = 1800 Sekunden -->

    <key>RunAtLoad</key>
    <true/>

    <key>StandardOutPath</key>
    <string>/Users/max/logs/sync.log</string>

    <key>StandardErrorPath</key>
    <string>/Users/max/logs/sync.log</string>
</dict>
</plist>
```

**Beispiel 3: Bei Dateiänderung ausführen (WatchPaths)**

Datei: `~/Library/LaunchAgents/com.user.watcher.plist`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.user.watcher</string>

    <key>ProgramArguments</key>
    <array>
        <string>/Users/max/scripts/process-downloads.sh</string>
    </array>

    <key>WatchPaths</key>
    <array>
        <string>/Users/max/Downloads</string>
    </array>

    <key>StandardOutPath</key>
    <string>/Users/max/logs/watcher.log</string>

    <key>StandardErrorPath</key>
    <string>/Users/max/logs/watcher.log</string>
</dict>
</plist>
```

**Beispiel 4: Bei Login ausführen**

Datei: `~/Library/LaunchAgents/com.user.startup.plist`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.user.startup</string>

    <key>ProgramArguments</key>
    <array>
        <string>/Users/max/scripts/startup.sh</string>
    </array>

    <key>RunAtLoad</key>
    <true/>
</dict>
</plist>
```

**Beispiel 5: Dienst dauerhaft laufen lassen (KeepAlive)**

Datei: `~/Library/LaunchAgents/com.user.server.plist`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.user.server</string>

    <key>ProgramArguments</key>
    <array>
        <string>/Users/max/scripts/server.sh</string>
    </array>

    <key>RunAtLoad</key>
    <true/>

    <key>KeepAlive</key>
    <true/>

    <key>ThrottleInterval</key>
    <integer>10</integer>

    <key>StandardOutPath</key>
    <string>/Users/max/logs/server.log</string>

    <key>StandardErrorPath</key>
    <string>/Users/max/logs/server.log</string>
</dict>
</plist>
```

### 4.5    Job aktivieren

```zsh
# 1. Datei erstellen/bearbeiten
nano ~/Library/LaunchAgents/com.user.backup.plist

# 2. Syntax prüfen
plutil -lint ~/Library/LaunchAgents/com.user.backup.plist

# 3. Job laden
launchctl load ~/Library/LaunchAgents/com.user.backup.plist

# 4. Prüfen ob geladen
launchctl list | grep com.user.backup

# 5. Manuell testen
launchctl start com.user.backup
```

### 4.6    Job deaktivieren

```zsh
# Job entladen
launchctl unload ~/Library/LaunchAgents/com.user.backup.plist

# Job deaktivieren (bleibt entladen nach Neustart)
launchctl unload -w ~/Library/LaunchAgents/com.user.backup.plist

# Oder: Disabled-Key in plist setzen
```

### 4.7    Fehlersuche

```zsh
# plist-Syntax prüfen
plutil -lint ~/Library/LaunchAgents/com.user.task.plist

# Geladene Jobs anzeigen
launchctl list | grep com.user

# Exit-Status prüfen (0 = OK, sonst Fehler)
launchctl list | grep com.user.task
# Ausgabe: PID  Status  Label
#          -    0       com.user.task  (Status 0 = OK)
#          -    1       com.user.task  (Status 1 = Fehler)

# System-Log prüfen
log show --predicate 'subsystem == "com.apple.xpc.launchd"' --last 1h | grep com.user

# Ausgabe-Logs prüfen
tail -f ~/logs/task.log
```

**Häufige Fehler:**

| Status | Bedeutung |
| ------ | --------- |
| 0 | Erfolgreich beendet |
| 1 | Allgemeiner Fehler |
| 78 | Konfigurationsfehler |
| 126 | Befehl nicht ausführbar |
| 127 | Befehl nicht gefunden |

## 5    Unterschiede Cron vs. `launchd`

### 5.1    Vergleichstabelle

| Merkmal | cron | launchd |
| ------- | ---- | ------- |
| **Herkunft** | Unix (1970er) | Apple (2005) |
| **Konfiguration** | Textdatei (crontab) | XML (.plist) |
| **Syntax** | Einfach, kompakt | Verbose, aber flexibel |
| **Zeitsteuerung** | Minutengenau | Sekunden möglich |
| **Intervalle** | Nur über Zeitpunkte | Native Intervall-Unterstützung |
| **Auslöser** | Nur Zeit | Zeit, Dateien, Netzwerk, etc. |
| **Verpasste Jobs** | Werden übersprungen | Werden nachgeholt |
| **Umgebung** | Minimal | Konfigurierbar |
| **Logging** | Manuell | Integriert |
| **Dienst-Management** | Nicht möglich | Vollständig (KeepAlive) |
| **macOS-Integration** | Basic | Vollständig |
| **Portabilität** | Hoch (alle Unix) | Nur macOS |

### 5.2    Wann cron verwenden?

✅ **cron ist besser für:**

- Einfache, zeitbasierte Aufgaben
- Portabilität (Skripte auch auf Linux nutzbar)
- Schnelle, unkomplizierte Einrichtung
- Erfahrene Unix-Nutzer

**Beispiel-Anwendungsfälle:**

```cron
# Einfaches tägliches Backup
0 2 * * * /Users/max/scripts/backup.sh

# Stündlicher Check
0 * * * * /Users/max/scripts/check.sh
```

### 5.3    Wann launchd verwenden?

✅ **launchd ist besser für:**

- macOS-spezifische Aufgaben
- Reaktion auf Ereignisse (Dateiänderungen, Netzwerk)
- Dienste die dauerhaft laufen sollen
- Bessere Fehlerbehandlung
- Integration mit macOS-Funktionen (Power Management, etc.)
- Wenn verpasste Jobs nachgeholt werden sollen

**Beispiel-Anwendungsfälle:**

- Ordner überwachen und bei Änderung reagieren
- Dienst der nach Absturz neu startet
- Task der nur bei Netzwerkverbindung läuft

### 5.4    Entscheidungshilfe

```
Aufgabe                                 → Wahl
────────────────────────────────────────────────
Einfache Zeitsteuerung                 → cron
Reaktion auf Dateiänderungen           → launchd
Dauerlaufenden Dienst                  → launchd
Portabilität zu Linux                  → cron
Nachholung verpasster Jobs             → launchd
Schnelle Einrichtung                   → cron
macOS-Integration (Sleep/Wake)         → launchd
Komplexe Bedingungen                   → launchd
```

### 5.5    Migration cron → launchd

**cron:**

```cron
30 2 * * * /Users/max/scripts/backup.sh
```

**Äquivalentes launchd:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.user.backup</string>

    <key>ProgramArguments</key>
    <array>
        <string>/Users/max/scripts/backup.sh</string>
    </array>

    <key>StartCalendarInterval</key>
    <dict>
        <key>Hour</key>
        <integer>2</integer>
        <key>Minute</key>
        <integer>30</integer>
    </dict>
</dict>
</plist>
```

### 5.6    Beide parallel nutzen

Es ist möglich, beide Systeme gleichzeitig zu verwenden:

- **cron** für einfache, portable Aufgaben
- **launchd** für macOS-spezifische Anforderungen

**Tipp:** Dokumentieren, welche Aufgaben wo konfiguriert sind:

```zsh
# Alle aktiven Jobs anzeigen
echo "=== Cron Jobs ==="
crontab -l

echo ""
echo "=== LaunchAgents ==="
ls ~/Library/LaunchAgents/

echo ""
echo "=== Geladene Jobs ==="
launchctl list | grep com.user
```

### 5.7    Empfehlung

Für neue macOS-Projekte wird **launchd** empfohlen:

1. Bessere Integration ins System
2. Mehr Flexibilität bei Auslösern
3. Zuverlässigere Ausführung
4. Bessere Logging-Möglichkeiten
5. Von Apple unterstützt und gepflegt

**cron** bleibt eine gute Wahl für:

1. Einfache, zeitbasierte Tasks
2. Cross-Platform-Kompatibilität
3. Schnelle Einrichtung ohne XML
