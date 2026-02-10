# Grundlagen

## 1    Was ist das Terminal

Das Terminal ist ein Programm, dass eine Shell, d. h. einen Kommandozeileninterpreter, ausführt. macOS nutzt standardmäßig `zsh` (Z Shell). Es wird im folgenden davon ausgegangen, dass `zsh` verwendet wird.

Welche Shell genutzt wird, kann man dem folgenden Befehl geprüft werden:

```zsh
echo $SHELL
```

## 2    Wichtige Konzepte/Begriffe

| Begriff                | Bedeutung                                                        |
| ---------------------- | ---------------------------------------------------------------- |
| **Shell**              | Programm, das Befehle interpretiert (`zsh`, `bash`, `fish` etc.) |
| **Prompt**             | Die Eingabeaufforderung, z. B. `username@Mac ~ %`                |
| **Pfad (Path)**        | Gibt an, wo im Dateisystem man sich befindest                    |
| **Home-Verzeichnis**   | Pfad des persönlichen Bereichs: `~ oder /Users/<username>`       |
| **Root**               | Das oberste Verzeichnis des Systems: `/`                         |
| **Umgebungsvariablen** | Werte, die Systemverhalten beeinflussen, z. B. `PATH`, `HOME`    |

## 3    Dateisystem von macOS

## 4    Aktuelles Dateisystem: APFS (Apple File System)

Seit macOS 10.13 (High Sierra, 2017) ist **APFS** das Standard-Dateisystem für SSDs; seit macOS 10.15 (Catalina, 2019) auch für alle Laufwerke.

**Merkmale:**

- **Copy-on-Write (CoW):** Änderungen an Dateien oder Metadaten erzeugen neue Kopien, was das System sicherer macht.

  (Änderungen werden nicht direkt auf bestehende Daten geschrieben werden. Stattdessen legt das System beim Ändern von Dateien oder Metadaten eine neue Kopie der betroffenen Blöcke an und verweist erst danach auf sie. Dadurch bleiben alte Datenversionen solange erhalten, bis der Schreibvorgang erfolgreich abgeschlossen ist. Das schützt vor Datenverlust bei Stromausfall oder Absturz. Außerdem ermöglicht CoW Systemfunktionen wie Snapshots, bei denen ganze Dateisystemzustände nahezu ohne Speicheraufwand gespeichert werden.)

- **Snapshots:** Das System kann zu bestimmten Zeitpunkten eingefroren werden (z. B. vor Systemupdates). Dadurch kann bei Problemen auf einen alten Zustand zurückgewechselt werden. Time Machine nutzt diese Funktion.

- **Space Sharing:** Mehrere Volumes (z. B. "Macintosh HD" und "Macintosh HD – Data") teilen sich denselben physischen Speicherplatz. Das spart Platz, da ungenutzter Speicher flexibel zwischen Volumes verteilt wird.

- **Verschlüsselung:** Native Unterstützung für FileVault 2 und einzelne verschlüsselte Volumes. (FileVault 2 verschlüsselt das gesamte Systemvolume, schützt Daten bei Diebstahl und sorgt dafür, dass nur berechtigte Benutzer darauf zugreifen können.)

- **Case Sensitivity:** Standardmäßig _nicht_ case-sensitive (d. h. `Datei.txt = datei.txt`), kann aber beim Formatieren geändert werden.

## 5    Wichtige Volumes und Partitionen

Seit macOS Catalina ist das System in mehrere logische Volumes aufgeteilt:

- **Macintosh HD:** Systemvolume (read-only, geschützt durch SIP)
  (Das Systemvolume enthält das schreibgeschützte Betriebssystem inklusive aller Kernkomponenten, Frameworks und Standardprogramme.)

- **Macintosh HD – Data:** Benutzer- und Programmdaten (beschreibbar)
  persönliche Dateien, Dokumente, Einstellungen und installierte Programme, z. B. in `/Users/<username>` oder `/Applications`.

- **Preboot, Recovery, VM:** Hilfsvolumes für Boot, Wiederherstellung und Swap
  Preboot hilft beim Starten des Systems, Recovery ermöglicht Wiederherstellung und VM speichert Swap- bzw. Auslagerungsdateien temporär.

Mit dem Befehl `diskutil` wird die komplette Volume-Struktur angezeigt.

## 6    Systemschutz und Berechtigungen

macOS schützt kritische Systembereiche durch verschiedene Mechanismen, damit Systemdateien nicht versehentlich verändert werden können.

- **SIP (System Integrity Protection):**
  Schützt Systemverzeichnisse und Kernel-Extensions. Nur im Recovery-Modus deaktivierbar.

- **Sandboxing:**
  Viele Apps laufen in isolierten Containern, sodass sie nur auf erlaubte Dateien zugreifen können. Dies begrenzt Schäden durch fehlerhafte oder schadhafte Programme.

- **ACLs & POSIX Permissions:**
  macOS verwendet klassische Unix-Rechte (`rwx`, siehe [[../SUMMARY#6.1 `chmod`]]) und erweiterte Access Control Lists (siehe [[../SUMMARY#6.3 ACL]]). → Anzeigen mit `ls -le`.

## 7    Mountpoints und Pfade

- Alle Volumes werden unter `/Volumes/` eingehängt.
- Netzlaufwerke oder externe Festplatten erscheinen dort automatisch.
- Systempfade wie `/System`, `/Library` oder `/usr` sind meist Symlinks auf interne Strukturen des schreibgeschützten Systemvolumes.

## 8    Ordnerstruktur und wichtige Verzeichnisse

macOS basiert auf UNIX (BSD), daher folgt es grob der klassischen Unix-Hierarchie – erweitert um Apple-spezifische Pfade.

### 8.1    Systemebene (Root `/`)

| Ordner          | Beschreibung                                                                                                                                                                                                                                 |
| --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `/System`       | Enthält das Betriebssystem selbst (Frameworks, Libraries, Kernel-Komponenten). Schreibgeschützt                                                                                                                                              |
| `/bin`          | Enthält grundlegende Benutzerbefehle, die auch im Einzelbenutzermodus verfügbar sein müssen, wie beispielweise `ls`, `cp`, `mv`, `rm`, `bash` oder `zsh`. Diese Tools sind für den täglichen Gebrauch und die Systemreparatur unverzichtbar. |
| `/sbin`         | Beinhaltet System- und Verwaltungsbefehle, die normalerweise Administratorrechte erfordern, z. B. `ifconfig`, `mount`, `fsck` oder `reboot`. Diese Tools dienen zur Systemwartung und -konfiguration.                                        |
| `/usr/bin`      | Hier liegen zusätzliche Benutzerprogramme und Utilities, die nicht unbedingt zum Start des Systems benötigt werden, z. B. `grep`, `awk`, `vim`, `curl`, `python`, `git` usw.                                                                 |
| `/usr/sbin`     | Ergänzt `/usr/bin` um administrative Befehle wie `apachectl`, `postfix`, `cron` oder `syslogd`. Sie werden meistens von Diensten oder Admins verwendet, nicht von normalen Nutzern.                                                          |
| `/usr/local`    | Für manuell installierte Tools und Homebrew                                                                                                                                                                                                  |
| `/Library`      | Systemweite Ressourcen: LaunchAgents, Fonts, Extensions, Preferences                                                                                                                                                                         |
| `/Applications` | Systemweite Programme (z. B. Safari, Mail)                                                                                                                                                                                                   |
| `/Volumes`      | Mountpoint für externe Laufwerke, Netzfreigaben, Disk-Images                                                                                                                                                                                 |
| `/private`      | Enthält temporäre Daten (/private/tmp, /private/var) und Logdateien. Viele klassische UNIX-Verzeichnisse (/etc, /tmp, /var) sind Symlinks hierhin                                                                                            |

> [!NOTE]
> Viele dieser Pfade sind symbolisch mit `/System/Volumes/Data/usr/...` verknüpft, da das eigentliche Systemvolume seit macOS Catalina schreibgeschützt ist.

### 8.2    Benutzerverzeichnisse (`/Users`)

Jeder Benutzer hat ein eigenes Homeverzeichnis, z. B. `/Users/john`.

**Wichtige Unterordner:**

| Ordner                  | Beschreibung                                                     |
| ----------------------- | ---------------------------------------------------------------- |
| `Desktop`                 | Dateien auf dem Schreibtisch.                                    |
| `Documents`               | Eigene Dokumente.                                                |
| `Downloads`               | Standard-Download-Ordner.                                        |
| `Library`                 | Benutzerbezogene Einstellungen, Caches, LaunchAgents, App-Daten. |
| `Pictures, Music, Movies` | Medienordner (z. B. für Fotos oder iTunes).                      |

> [!NOTE]
> Der Ordner `~/Library` ist standardmäßig versteckt. Anzeigen mit:
>
> ```zsh
> chflags nohidden ~/Library
> ```

> [!IMPORTANT]
> **Tilde (`~`)**
>
> Das Zeichen `~` steht im Terminal als Abkürzung für das aktuelle Benutzerverzeichnis, z. B. `/Users/john`. Es wird getippt mit **⌥ N**.
>
> Mit `cd ~` kann man in das Homeverzeichnis zu wechseln oder mit `cd ~/Documents`, direkt in den Dokumente-Ordner.
### 8.3    Weitere wichtige Orte

| **Pfad**                                                    | **Zweck**                                     |
| ----------------------------------------------------------- | --------------------------------------------- |
| `/etc/hosts`                                                | Lokale Hostname-Zuordnungen.                  |
| `/var/log/`                                                 | System- und Anwendungs-Logs.                  |
| `/private/tmp`                                              | Temporäre Dateien (automatisch geleert).      |
| `/Library/LaunchAgents` <br>und<br>`/Library/LaunchDaemons` | Automatische Dienste und Hintergrundprozesse. |
| `/usr/share/`                                               | Gemeinsame Systemdateien (z. B. Manpages).    |

## 9    macOS unter der Haube

macOS basiert auf UNIX/BSD und Darwin. Unter der Haube steuern Launchd, Logging und Sicherheitsmechanismen, wie Gatekeeper oder TCC, den Systembetrieb.

### 9.1    Launchd und Dienste

`launchd` ist das zentrale Init-System von macOS und ersetzt klassische Systeme wie `systemd` oder `init`. Es startet Prozesse beim Booten, nach Bedarf oder im Benutzerkontext. Dienste werden über Property-List-Dateien (`.plist`) definiert.

**Speicherort der `.plist`-Dateien:**

- `/System/Library/LaunchDaemons`,
- `/Library/LaunchAgents` oder
- `~/Library/LaunchAgents`

**Aktive Dienste anzeigen:**

```zsh
launchctl list
```

**Manuelle Steuerung von Diensten:**

```zsh
launchctl load/unload
```

Damit verwaltet macOS sowohl System- als auch Benutzerprozesse konsistent.

### 9.2    Systemprotokolle und Logging

macOS verwendet seit Sierra ein *Unified Logging System*, das alle Logmeldungen zentral verfügbar macht.

**Vergangene Ereignisse anzeigen:**

```zsh
log show
```

**Live Ansicht von Ereignissen:**

```zsh
log stream
```

Diese Logs sind strukturiert und nach Subsystemen gegliedert, z. B. "network", "kernel" oder "security".

Ältere Textlogs existieren weiterhin unter `/var/log/`, z. B. `system.log`. Entwickler oder Administratoren können mit Filtern wie

```zsh
log show --predicate 'eventMessage contains "error"'
```

gezielt Fehlermeldungen finden.

### 9.3    Netzwerk & Namensauflösung

macOS bietet zahlreiche Kommandozeilenwerkzeuge zur Verwaltung und Diagnose von Netzwerken. Neben klassischen UNIX-Befehlen wie `ping` und `traceroute` sind vor allem networksetup und scutil für Systemkonfigurationen relevant.

Über den Dienst **mDNSResponder** (Bonjour) werden Geräte im lokalen Netz automatisch erkannt. Die Datei `/etc/hosts` erlaubt lokale DNS-Zuordnungen.

| **Befehl**       | **Beschreibung**                                                                 | **Beispiel**                         |
| ---------------- | -------------------------------------------------------------------------------- | ------------------------------------ |
| `networksetup`   | Zeigt oder ändert Netzwerk-Interfaces, Dienste und DNS-Einstellungen             | `networksetup -listallhardwareports` |
| `ifconfig`       | Zeigt IP-Konfigurationen und Netzwerkinterfaces an oder konfiguriert sie manuell | `ifconfig en0`                       |
| `ping`           | Prüft, ob ein Host erreichbar ist                                                | `ping google.com`                    |
| `traceroute`     | Zeigt die Route von Paketen zum Zielhost an                                      | `traceroute apple.com`               |
| `scutil --dns`   | Listet DNS-Server, Suchdomänen und aktuelle Resolver-Einstellungen auf           | `scutil --dns`                       |
| `cat /etc/hosts` | Zeigt lokale Hostnamen-Zuordnungen an oder ermöglicht deren Bearbeitung          | `sudo nano /etc/hosts`               |

### 9.4    Sicherheitsmechanismen

Die folgenden Mechanismen/Schutzebenen wirken zusammen, um das System vor Malware und unautorisierten Änderungen zu schützen:

- **Gatekeeper** blockiert unsignierte oder nicht-notarisierte Apps.
- **SIP (System Integrity Protection)** schützt Systemverzeichnisse und Kernel-Komponenten selbst vor Root-Änderungen.
- **Notarization** stellt sicher, dass Apps von Apple geprüft wurden.
- **Sandboxing** und **TCC (Transparency, Consent & Control)** begrenzen App-Zugriffe auf Dateien, Kamera, Mikrofon usw.

### 9.5    Prozess- & Systemüberwachung

Es gibt mehrere Tools, um Prozesse, Ressourcen und Systemzustände zu überwachen. Während `ps` und `top` Standardbefehle aus der UNIX-Welt sind, liefert `htop` eine moderne, farbige Ansicht. Befehle wie `lsof`, `vm_stat` und `powermetrics` helfen bei tiefergehender Analyse von Performance, Speicher oder Energieverbrauch.

| **Befehl**     | **Beschreibung**                                                             | **Beispiel**                             |
| -------------- | ---------------------------------------------------------------------------- | ---------------------------------------- |
| `ps aux`       | Listet alle laufenden Prozesse mit Benutzer, PID, CPU- und Speicherverbrauch | `ps aux`                                 |
| `top`          | Zeigt Systemauslastung in Echtzeit an (ähnlich Task-Manager)                 | `top`                                    |
| `htop`         | Erweiterte, interaktive Prozessansicht mit Sortierung, Suche und Farben      | `htop`                                   |
| `lsof`         | Listet offene Dateien, Ports und Sockets                                     | `sudo lsof -i :80`                       |
| `vm_stat`      | Zeigt Statistik zur Speichernutzung und Paging-Aktivität                     | `vm_stat`                                |
| `powermetrics` | Misst Energieverbrauch, CPU-Last, GPU-Aktivität und Temperatur               | `sudo powermetrics --samplers cpu_power` |
