# Gemeinsamkeiten mit Linux-Terminals

Vieles von dem, was für macOS-Terminals gelten, ist direkt auf Linux übertragbar, es gibt jedoch einige wichtige Unterschiede. In diesem Kapitel erfolgt eine genauere Betrachtung.

## 1    Shell

- **macOS:** Standardmäßig nutzt macOS seit macOS Catalina **zsh** (vorher war es bash).
- **Linux:** Die häufigste Shell ist **bash**, aber auch zsh, fish oder andere Shells sind möglich.

**Bedeutung:**

- Grundlegende Befehle (`ls`, `cd`, `cp`, `mv`, `mkdir`, `rm`, `cat`, `grep`, usw.) funktionieren auf beiden Systemen gleich.
- Skripte in `bash` funktionieren oft auch in `zsh`, aber manche `zsh`-spezifischen Features gibt es auf Linux nicht.

## 2    Dateisystem

- **macOS:** HFS+ oder APFS. Pfade beginnen mit `/` wie bei Linux, aber es gibt kleine Unterschiede:
    - macOS ist standardmäßig **case-insensitive**, d. h. `file.txt = File.txt`.
    - macOS hat viele macOS-spezifische Verzeichnisse wie `/Applications` oder `/System`.

- **Linux:** Typisch ist ext4, XFS usw. Linux ist **case-sensitive**, d. h. `file.txt != File.txt`.

## 3    Paketverwaltung

- **macOS:** Homebrew (`brew`) ist die gängigste Lösung.
- **Linux:** Abhängig von Distribution:
    - Debian/Ubuntu → apt (`apt-get`)
    - Fedora → `dnf`
    - Arch → `pacman`

**Bedeutung:** Viele Tools, die man mit `brew install toolname` installiert, haben auf Linux andere Paketnamen oder Befehle.

## 4    Systembefehle / Administratorrechte

- **macOS:** `sudo` funktioniert wie auf Linux. Systemkonfigurationen laufen oft über grafische Apps oder `/etc`.
- **Linux:** `sudo` oder direkt als `root` (`su`) üblich. Systemdienste verwaltest man meistens mit `systemctl`.

## 5    Texteditoren

Sowohl macOS als auch Linux haben `nano`, `vim` und `emacs`. Unterschiede gibt es höchstens bei vorinstallierten Versionen.

## 6    Fazit

Wenn man das macOS-Terminal gut beherrscht:

- **Alltägliche Navigation, Dateimanipulation, Skripting, Textbearbeitung, Pipes und Redirects** sind sehr ähnlich.
- **Systemverwaltung, Paketverwaltung, manche Pfade und systemnahe Befehle** unterscheiden sich und müssen gelernt werden.

Die meisten Terminal-Befehle sind identisch, nur Systemdetails und Paketverwaltung unterscheiden sich.

| Kategorie                   | Befehl / Aufgabe               | macOS Terminal          | Linux Terminal           | Hinweis                                                         |     |
| --------------------------- | ------------------------------ | ----------------------- | ------------------------ | --------------------------------------------------------------- | --- |
| **Navigation**              | Aktuelles Verzeichnis          | `pwd`                   | `pwd`                    | identisch                                                       |     |
|                             | Verzeichnis wechseln           | `cd /Pfad`              | `cd /Pfad`               | identisch                                                       |     |
|                             | Inhalt anzeigen                | `ls`                    | `ls`                     | identisch; Optionen wie `ls -l` gleich                          |     |
|                             | Verzeichnis erstellen          | `mkdir name`            | `mkdir name`             | identisch                                                       |     |
|                             | Dateien kopieren               | `cp quelle ziel`        | `cp quelle ziel`         | identisch                                                       |     |
|                             | Dateien verschieben/umbenennen | `mv quelle ziel`        | `mv quelle ziel`         | identisch                                                       |     |
|                             | Datei löschen                  | `rm datei`              | `rm datei`               | identisch; Vorsicht bei `rm -rf`                                |     |
| **Text und Suche**          | Datei anzeigen                 | `cat datei`             | `cat datei`              | identisch                                                       |     |
|                             | Zeilen durchsuchen             | `grep "text" datei`     | `grep "text" datei`      | identisch                                                       |     |
|                             | Texteditor                     | `nano datei, vim datei` | `nano datei, vim datei`  | identisch, oft schon vorinstalliert                             |     |
| **System & Info**           | Benutzer                       | `whoami`                | `whoami`                 | identisch                                                       |     |
|                             | Systeminfo                     | `uname -a`              | `uname -a`               | identisch                                                       |     |
|                             | Speicherplatz                  | `df -h`                 | `df -h`                  | identisch                                                       |     |
|                             | Prozesse anzeigen              | `top`                   | `top oder htop`          | htop evtl. separat installieren                                 |     |
| **Rechte & Administration** | Als Admin ausführen            | `sudo befehl`           | `sudo befehl`            | identisch                                                       |     |
|                             | Root werden                    | `sudo -i`               | `sudo -i oder su -`      | kleine Unterschiede                                             |     |
|                             | Systemdienste                  | `launchctl`             | `systemctl`              | unterschiedlich, Linux nutzt meist `systemctl`                  |     |
| **Paketverwaltung**         | Software installieren          | `brew install paket`    | `sudo apt install paket` | Paketnamen unterscheiden sich oft                               |     |
|                             | Software suchen                | `brew search paket`     | `apt search paket`       | gleiches Prinzip, andere Befehle                                |     |
| **Shell / Skripte**         | Shell starten                  | `zsh`                   | `bash (häufig), zsh`     | Skripte meist kompatibel, zsh-spezifische Features evtl. anders |     |
| **Netzwerk**                | IP-Adresse anzeigen            | `ifconfig`              | `ip addr`                | Linux nutzt moderne ip-Befehle häufiger                         |     |
|                             | Netzwerk testen                | `ping host`             | `ping host`              | identisch                                                       |     |
|                             |                                |                         |                          |                                                                 |     |
