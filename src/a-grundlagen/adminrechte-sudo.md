# Admin-Rechte (`sudo`)

Ist man als normaler Benutzer angemeldet kann ein Befehl mit Administrator-Rechten ausgeführt werden, indem `sudo` (*superuser do*) vorangestellt wird. Da macOS ein UNIX-basiertes System ist, sind viele Systembefehle nur mit Admin-Rechten ausführbar. Sie sind auch erforderlich für den Zugriff auf geschützte Systemdateien und -einstellungen.

## 1    Syntax

```zsh
sudo <befehl> [optionen] [argumente]
```

Bei der ersten Ausführung von `sudo` wird nach dem Administratorpasswort gefragt.

## 2    Beispiele

**Ordner `/usr/local/test` mit Administratorrechten erstellen:**

```zsh
sudo mkdir /usr/local/test
```

**Systemverzeichnisse ändern:**

```zsh
sudo nano /etc/hosts
```

→ Bearbeitung der Host-Datei, um z.  B. Webseiten umzuleiten.

**Dienste starten/stoppen:**

```zsh
sudo launchctl load /Library/LaunchDaemons/meindienst.plist
```

**Systemweite Berechtigungen ändern:**

```zsh
sudo chmod 755 /usr/local/bin/meinprogramm
```

**Dateien löschen, die geschützt sind:**

```zsh
sudo rm -rf /Pfad/zur/Datei
```

> [!caution]
> Den Befehl `sudo` sollte man nur verwenden, wenn man genau weiß, was man tut. Eine falsche Verwendung kann das System beschädigen!

## 3    Root-Shell und Shell mit Root-Rechten

`sudo` speichert die Authentifizierung kurzfristig (standardmäßig 5 Minuten = 300 Sekunden). Für häufige Admin-Aufgaben eignet sich eine Root-Shell (`sudo -i`) oder eine Shell mit Root-Rechten (`sudo -s`).

### 3.1    Root-Shell (`sudo -i` = *login shell as root*)

Es wird simuliert, dass man als Root-Benutzer eingeloggt ist, fast wie bei einem echten Root-Login.

**Wichtige Eigenschaften:**

- Es werden die Umgebungsvariablen des Root-Benutzers geladen (`PATH`, `HOME`, usw.).
- Man landet im Home-Verzeichnis des Root-Benutzers (`/var/root`).
- Es werden die Shell-Startdateien vom Root-Benutzer genutzt (`.profile` und `.bashrc` bzw. `.zshrc`)

```zsh
sudo -i
```

- Prompt wechselt, z. B. von `cgroening@macbook ~ %` zu `macbook:~ root#`
- `echo $HOME` zeigt den Ordner `/var/root`

### 3.2    Shell mit Root-Rechten (`sudo -s` = *run shell as root*)

Man erhält eine Shell mit Root-Rechten.

**Aber:**

- Die eigenen Umgebungsvariablen bleiben aktiv.
- Man verbleibt im aktuellen Benutzerverzeichnis.
- Die Startdateien des Root-Benutzers werden **nicht** geladen.

```zsh
sudo -s
```

- Prompt wechselt, z. B. von `cgroening@macbook ~ %` zu `root@macbook ~ #`
- `echo $HOME` zeigt den Ordner `/Users/cgroening`

### 3.3    Zusammenfassung

| Option    | Shelltyp      | Verzeichnis   | Umgebungs-<br>variablen | Startdateien <br>geladen? | Prompt                  |
| --------- | ------------- | ------------- | ----------------------- | ------------------------- | ----------------------- |
| `-`       | Normale Shell | Benutzer-Home |                         |                           | `cgroening@macbook ~ %` |
| `sudo -i` | Login-Shell   | Root-Home     | Root                    | Ja                        | `macbook:~ root#`       |
| `sudo -s` | Non-Login     | Benutzer-Home | Eigene                  | Nein                      | `root@macbook ~ #`      |

> [!INFO]
> **`sudo -i` vs `sudo -s`**
>
> - Will man **komplett wie Root arbeiten**, inklusive `PATH` & `HOME` → `sudo -i`
> - Willst man nur **temporär Root-Rechte**, aber mit der eigenen Umgebung → `sudo -s`

## 4    Übersicht

| Befehl          | Funktion                                                 |
| --------------- | -------------------------------------------------------- |
| `sudo <befehl>` | Führt einen Befehl als Administrator aus                 |
| `sudo -i`       | Öffnet eine Root-Shell                                   |
| `sudo -s`       | Öffnet Shell mit Root-Rechten                            |
| `sudo !!`       | Wiederholt letzten Befehl mit `sudo`                     |
| `sudo -v`       | Erneuert den `sudo`-Timer, ohne einen Befehl auszuführen |
| `sudo -k`       | Löscht sofort alle gespeicherten Authentifizierungen     |
| `exit`          | Verlässt die Root-Shell bzw. Shell mit Root-Rechten      |
