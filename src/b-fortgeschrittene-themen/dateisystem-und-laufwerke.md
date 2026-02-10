# Dateisystem & Volumes

Dieses Kapitel behandelt fortgeschrittene Dateisystem-Operationen unter macOS: symbolische und harte Links, Datei-Metadaten, Volume-Management, Disk Images und Backup-Werkzeuge.

## 1    Links

Links ermöglichen es, auf Dateien oder Verzeichnisse an mehreren Stellen im Dateisystem zu verweisen, ohne die Daten zu duplizieren.

### 1.1    Symbolische Links (`ln -s`)

Ein **symbolischer Link** (Symlink, Softlink) ist ein Verweis auf einen Pfad. Er funktioniert wie eine Verknüpfung und kann auf Dateien oder Verzeichnisse zeigen – auch über Dateisystemgrenzen hinweg.

**Syntax:**

```zsh
ln -s ZIEL LINKNAME
```

**Beispiele:**

```zsh
# Symlink auf eine Datei
ln -s ~/Documents/config.yaml ~/.config/app/config.yaml

# Symlink auf ein Verzeichnis
ln -s ~/Dropbox/Projekte ~/Projekte

# Symlink mit relativem Pfad
cd ~/bin
ln -s ../scripts/backup.sh backup

# Symlink in anderem Verzeichnis erstellen
ln -s /opt/homebrew/bin/python3 /usr/local/bin/python
```

**Eigenschaften:**

| Merkmal | Symbolischer Link |
| ------- | ----------------- |
| Verweist auf | Pfad (String) |
| Ziel gelöscht | Link wird "broken" (toter Link) |
| Über Dateisysteme | ✅ Ja |
| Auf Verzeichnisse | ✅ Ja |
| Eigene Inode | ✅ Ja (eigene Datei) |
| Erkennung | `ls -l` zeigt `l` und `->` |

**Symlink erkennen:**

```zsh
ls -l ~/Projekte
# lrwxr-xr-x  1 max  staff  20 Jan 15 10:30 Projekte -> Dropbox/Projekte

# Nur Symlinks anzeigen
ls -la | grep "^l"

# Mit file-Befehl
file ~/Projekte
# /Users/max/Projekte: symbolic link to Dropbox/Projekte
```

### 1.2    Harte Links (`ln`)

Ein **harter Link** ist ein zusätzlicher Verzeichniseintrag für dieselbe Datei (Inode). Die Datei existiert erst dann nicht mehr, wenn alle harten Links gelöscht wurden.

**Syntax:**

```zsh
ln ZIEL LINKNAME
```

**Beispiele:**

```zsh
# Harter Link auf Datei
ln original.txt hardlink.txt

# Beide zeigen auf dieselbe Inode
ls -li original.txt hardlink.txt
# 12345678 -rw-r--r--  2 max  staff  100 Jan 15 10:30 hardlink.txt
# 12345678 -rw-r--r--  2 max  staff  100 Jan 15 10:30 original.txt
#    ^-- gleiche Inode-Nummer        ^-- Link-Count = 2
```

**Eigenschaften:**

| Merkmal | Harter Link |
| ------- | ----------- |
| Verweist auf | Inode (Daten direkt) |
| Ziel gelöscht | Link funktioniert weiter |
| Über Dateisysteme | ❌ Nein |
| Auf Verzeichnisse | ❌ Nein (außer `.` und `..`) |
| Eigene Inode | ❌ Nein (teilt Inode) |
| Erkennung | Link-Count > 1 |

**Einschränkungen unter macOS:**

```zsh
# Harte Links auf Verzeichnisse sind nicht erlaubt
ln /Users/max/Documents /Users/max/Docs
# ln: /Users/max/Documents: Is a directory

# Harte Links über Volumes hinweg nicht möglich
ln /Volumes/USB/file.txt ~/file.txt
# ln: /Volumes/USB/file.txt: Cross-device link
```

### 1.3    Unterschiede und Anwendungsfälle

**Vergleichstabelle:**

| Merkmal | Symbolischer Link | Harter Link |
| ------- | ----------------- | ----------- |
| Verweist auf | Pfadname | Inode |
| Eigene Inode | Ja | Nein |
| Funktioniert nach Löschen des Originals | Nein (broken) | Ja |
| Über Dateisysteme | Ja | Nein |
| Auf Verzeichnisse | Ja | Nein |
| Platzbedarf | Minimal (Pfad) | Keiner (Eintrag) |
| Typische Nutzung | Verknüpfungen, Aliase | Backups, Deduplizierung |

**Wann Symlinks verwenden:**

- Verknüpfungen zu Verzeichnissen
- Links über verschiedene Volumes/Partitionen
- Wenn der Link als "Verweis" erkennbar sein soll
- Konfigurationsdateien an mehreren Orten verfügbar machen
- Versionierte Verzeichnisse (z.B. `current -> v2.1.0`)

**Wann harte Links verwenden:**

- Backup-Systeme (Time Machine nutzt harte Links)
- Deduplizierung von Dateien
- Wenn die Datei auch nach Löschen des "Originals" erhalten bleiben soll
- Space-effiziente Kopien

**Beispiel: Versionierung mit Symlinks:**

```zsh
# Versionen als Verzeichnisse
mkdir -p ~/apps/myapp-1.0.0 ~/apps/myapp-1.1.0 ~/apps/myapp-2.0.0

# Symlink auf aktuelle Version
ln -s ~/apps/myapp-2.0.0 ~/apps/myapp-current

# Update auf neue Version
rm ~/apps/myapp-current
ln -s ~/apps/myapp-2.1.0 ~/apps/myapp-current

# Oder mit ln -sf (force)
ln -sf ~/apps/myapp-2.1.0 ~/apps/myapp-current
```

### 1.4    Links verwalten (`readlink`, `unlink`)

**readlink – Linkziel auslesen:**

```zsh
# Symlink-Ziel anzeigen
readlink ~/Projekte
# Dropbox/Projekte

# Kanonischen (absoluten) Pfad auflösen
readlink -f ~/Projekte
# /Users/max/Dropbox/Projekte

# Auch für nicht existierende Pfade
readlink -f ./relative/path/../file.txt
# /Users/max/current/file.txt
```

**Optionen:**

| Option | Beschreibung |
| ------ | ------------ |
| `-f` | Kanonischer Pfad (alle Symlinks auflösen) |
| `-n` | Keine Newline am Ende |

**unlink – Link entfernen:**

```zsh
# Symlink entfernen (nicht das Ziel!)
unlink ~/Projekte

# Äquivalent zu rm für einzelne Dateien
rm ~/Projekte
```

> [!caution]
> Bei Verzeichnis-Symlinks keinen Trailing Slash verwenden!
>
> ```zsh
> # RICHTIG: Entfernt den Symlink
> rm ~/Projekte
>
> # FALSCH: Versucht Inhalt des Zielverzeichnisses zu löschen!
> rm -r ~/Projekte/
> ```
>
> Der Trailing Slash (`/`) bewirkt, dass die Shell den Symlink auflöst und dem Befehl das Zielverzeichnis übergibt. Mit `rm -r ~/Projekte/` löscht man daher nicht den Symlink, sondern den Inhalt des verlinkten Verzeichnisses – ein häufiger und folgenschwerer Fehler.

**Links finden:**

```zsh
# Alle Symlinks in einem Verzeichnis
find ~/Documents -type l

# Broken Symlinks finden
find ~/Documents -type l ! -exec test -e {} \; -print

# Alle harten Links zu einer Datei finden (gleiche Inode)
find / -inum $(stat -f %i datei.txt) 2>/dev/null
```

**Link-Informationen mit stat:**

```zsh
# Inode und Link-Count anzeigen
stat -f "Inode: %i, Links: %l" datei.txt

# Bei Symlinks: Link vs. Ziel
stat -f "Link: %N, Ziel: %Y" ~/Projekte
```

## 2    Dateisystem-Tools

### 2.1    `stat` – Detaillierte Datei-Informationen

`stat` zeigt umfassende Metadaten einer Datei an.

**Syntax:**

```zsh
stat [Optionen] DATEI
```

**Standardausgabe:**

```zsh
stat ~/.zshrc
# 16777220 8453123 -rw-r--r-- 1 max staff 0 2048 "Jan 15 10:30:00 2024" ...
```

**Formatierte Ausgabe (macOS BSD-stat):**

```zsh
# Lesbare Ausgabe
stat -x ~/.zshrc

# Bestimmte Felder
stat -f "%N: %z Bytes, modified %Sm" ~/.zshrc
# .zshrc: 2048 Bytes, modified Jan 15 10:30:00 2024

# Nur Dateigröße
stat -f %z ~/.zshrc
# 2048

# Nur Inode
stat -f %i ~/.zshrc
# 8453123

# Berechtigungen oktal
stat -f %Op ~/.zshrc
# 100644

# Besitzer und Gruppe
stat -f "%Su:%Sg" ~/.zshrc
# max:staff
```

**Format-Spezifizierer (BSD/macOS):**

| Spezifizierer | Beschreibung |
| ------------- | ------------ |
| `%N` | Dateiname |
| `%z` | Größe in Bytes |
| `%i` | Inode-Nummer |
| `%l` | Anzahl harter Links |
| `%Op` | Berechtigungen (oktal) |
| `%Sp` | Berechtigungen (symbolisch) |
| `%Su` | Besitzer (Name) |
| `%Sg` | Gruppe (Name) |
| `%u` | Besitzer (UID) |
| `%g` | Gruppe (GID) |
| `%Sa` | Zugriffszeit |
| `%Sm` | Änderungszeit |
| `%Sc` | Statusänderungszeit |
| `%SB` | Erstellungszeit (macOS) |
| `%Y` | Symlink-Ziel |
| `%T` | Dateityp |

**Beispiele:**

```zsh
# Erstellungsdatum (macOS-spezifisch)
stat -f "Erstellt: %SB" -t "%Y-%m-%d %H:%M" ~/.zshrc

# Vergleich zweier Dateien
for f in file1.txt file2.txt; do
    stat -f "%N: %z Bytes, %Sm" "$f"
done

# Alle Infos für Skript
stat -f "size=%z;inode=%i;mode=%Op;uid=%u;gid=%g" datei.txt
```

### 2.2    `file` – Dateityp erkennen

`file` analysiert den Inhalt einer Datei und bestimmt ihren Typ – unabhängig von der Dateiendung.

**Syntax:**

```zsh
file [Optionen] DATEI...
```

**Beispiele:**

```zsh
file document.pdf
# document.pdf: PDF document, version 1.7

file image.png
# image.png: PNG image data, 1920 x 1080, 8-bit/color RGBA

file script.sh
# script.sh: Bourne-Again shell script, ASCII text executable

file binary
# binary: Mach-O 64-bit executable arm64

file archive.tar.gz
# archive.tar.gz: gzip compressed data

file unknown
# unknown: ASCII text, with very long lines
```

**Optionen:**

| Option | Beschreibung |
| ------ | ------------ |
| `-b` | Brief: Kein Dateiname in Ausgabe |
| `-i` | MIME-Typ ausgeben |
| `-I` | MIME-Typ mit Encoding |
| `-L` | Symlinks folgen |
| `-z` | Komprimierte Dateien untersuchen |

**Anwendungen:**

```zsh
# MIME-Typ für Webserver
file -I document.pdf
# document.pdf: application/pdf; charset=binary

# Mehrere Dateien
file *
# bild.jpg:    JPEG image data
# script.py:   Python script, ASCII text
# data.json:   JSON data

# In Skripten: Typ prüfen
if file -b "$1" | grep -q "shell script"; then
    echo "Ist ein Shell-Skript"
fi

# Nur echte Bilder finden
find . -type f -exec file {} \; | grep "image data"
```

### 2.3    `mdls` – macOS-Metadaten anzeigen

`mdls` zeigt Spotlight-Metadaten einer Datei an. Diese umfassen weit mehr als Standard-Dateiattribute.

**Syntax:**

```zsh
mdls [Optionen] DATEI
```

**Beispiel:**

```zsh
mdls foto.jpg
# kMDItemContentType         = "public.jpeg"
# kMDItemContentTypeTree     = (
#     "public.jpeg",
#     "public.image",
#     "public.data"
# )
# kMDItemDateTimeOriginal    = 2024-01-15 10:30:00 +0000
# kMDItemPixelHeight         = 1080
# kMDItemPixelWidth          = 1920
# kMDItemColorSpace          = "RGB"
# ...
```

**Bestimmte Attribute abfragen:**

```zsh
# Nur bestimmtes Attribut
mdls -name kMDItemContentType foto.jpg
# kMDItemContentType = "public.jpeg"

# Mehrere Attribute
mdls -name kMDItemPixelWidth -name kMDItemPixelHeight foto.jpg

# Wert ohne Attributnamen
mdls -raw -name kMDItemPixelWidth foto.jpg
# 1920
```

**Wichtige Metadaten-Attribute:**

| Attribut | Beschreibung |
| -------- | ------------ |
| `kMDItemContentType` | UTI-Dateityp |
| `kMDItemKind` | Lesbare Typbeschreibung |
| `kMDItemFSName` | Dateiname |
| `kMDItemFSSize` | Dateigröße |
| `kMDItemFSCreationDate` | Erstellungsdatum |
| `kMDItemContentCreationDate` | Inhalt erstellt |
| `kMDItemContentModificationDate` | Inhalt geändert |
| `kMDItemPixelWidth/Height` | Bildgröße |
| `kMDItemDurationSeconds` | Medien-Dauer |
| `kMDItemTitle` | Titel |
| `kMDItemAuthors` | Autoren |
| `kMDItemWhereFroms` | Download-URL |

**Praktische Beispiele:**

```zsh
# Bildabmessungen
mdls -name kMDItemPixelWidth -name kMDItemPixelHeight *.jpg

# Woher wurde Datei heruntergeladen?
mdls -name kMDItemWhereFroms ~/Downloads/installer.dmg

# Dauer eines Videos
mdls -raw -name kMDItemDurationSeconds video.mp4

# Alle PDFs mit Seitenzahl
for f in *.pdf; do
    pages=$(mdls -raw -name kMDItemNumberOfPages "$f" 2>/dev/null)
    echo "$f: $pages Seiten"
done
```

**Metadaten setzen mit xattr:**

```zsh
# Extended Attributes anzeigen
xattr -l datei.txt

# Quarantäne-Flag entfernen (heruntergeladene Dateien)
xattr -d com.apple.quarantine datei.app

# Alle xattrs entfernen
xattr -c datei.txt
```

## 3    Volumes & Mounten

### 3.1    `diskutil` – Laufwerke verwalten

`diskutil` ist das zentrale macOS-Tool zur Verwaltung von Festplatten, Partitionen und Volumes.

**Laufwerke auflisten:**

```zsh
# Alle Laufwerke
diskutil list

# Ausgabe:
# /dev/disk0 (internal, physical):
#    #:                       TYPE NAME                    SIZE
#    0:      GUID_partition_scheme                        *500.1 GB
#    1:                        EFI EFI                     209.7 MB
#    2:                 Apple_APFS Container disk1         499.9 GB
#
# /dev/disk1 (synthesized):
#    #:                       TYPE NAME                    SIZE
#    0:      APFS Container Scheme -                      +499.9 GB
#    1:                APFS Volume Macintosh HD            12.4 GB
#    2:                APFS Volume Preboot                 23.1 MB
#    ...
```

**Volume-Informationen:**

```zsh
# Detaillierte Infos zu einem Volume
diskutil info /dev/disk1s1

# Infos über Mount-Point
diskutil info /Volumes/USB

# Nur bestimmte Info
diskutil info /dev/disk1s1 | grep "Volume Name"
```

**Volumes ein- und aushängen:**

```zsh
# Volume aushängen
diskutil unmount /Volumes/USB
diskutil unmount /dev/disk2s1

# Volume einhängen
diskutil mount /dev/disk2s1

# Alle Volumes eines Laufwerks aushängen
diskutil unmountDisk /dev/disk2

# Laufwerk sicher auswerfen
diskutil eject /dev/disk2
```

**Partitionen und Formatierung:**

```zsh
# Volume löschen (APFS)
diskutil eraseVolume APFS "NeuesVolume" /dev/disk2s1

# Ganzes Laufwerk formatieren (ACHTUNG!)
diskutil eraseDisk APFS "MeinDisk" /dev/disk2

# Verfügbare Dateisystem-Typen
diskutil listFilesystems

# Partition hinzufügen (APFS Container)
diskutil apfs addVolume disk1 APFS "Daten"
```

**APFS-spezifische Befehle:**

```zsh
# APFS Container auflisten
diskutil apfs list

# Volume zu Container hinzufügen
diskutil apfs addVolume disk1 APFS "NeuesVolume"

# Volume löschen
diskutil apfs deleteVolume disk1s3

# Volume umbenennen
diskutil rename disk1s2 "Neuer Name"
```

**Festplatten reparieren:**

```zsh
# Dateisystem prüfen
diskutil verifyVolume /dev/disk1s1

# Dateisystem reparieren
diskutil repairVolume /dev/disk1s1

# First Aid (GUI-Äquivalent)
diskutil verifyDisk /dev/disk0
diskutil repairDisk /dev/disk0
```

### 3.2    `mount` / `umount` – Volumes ein-/aushängen

Die klassischen Unix-Befehle `mount` und `umount` funktionieren auch unter macOS.

**Aktuelle Mounts anzeigen:**

```zsh
# Alle gemounteten Volumes
mount

# Nur bestimmter Typ
mount | grep apfs
mount | grep nfs

# Übersichtlicher mit df
df -h
```

**Volume manuell mounten:**

```zsh
# Mount-Point erstellen
sudo mkdir /Volumes/MeinUSB

# Volume mounten
sudo mount -t msdos /dev/disk2s1 /Volumes/MeinUSB

# Mit Optionen
sudo mount -t hfs -o ro /dev/disk2s1 /Volumes/ReadOnly
```

**Volume aushängen:**

```zsh
# Normal aushängen
sudo umount /Volumes/MeinUSB

# Erzwingen (wenn "busy")
sudo umount -f /Volumes/MeinUSB

# Besser: diskutil verwenden
diskutil unmount /Volumes/MeinUSB
```

**Mount-Optionen:**

| Option | Beschreibung |
| ------ | ------------ |
| `-t TYPE` | Dateisystem-Typ (apfs, hfs, msdos, ntfs, nfs, smbfs) |
| `-o ro` | Read-only |
| `-o rw` | Read-write |
| `-o noexec` | Keine Ausführung erlauben |
| `-o nosuid` | Setuid ignorieren |

### 3.3    Netzlaufwerke mounten (SMB, NFS, AFP)

**SMB/CIFS (Windows-Freigaben):**

```zsh
# Mit mount_smbfs
mount_smbfs //user@server/share /Volumes/Share

# Mit Passwort (unsicher, besser Keychain nutzen)
mount_smbfs //user:passwort@server/share /Volumes/Share

# Über Finder-URL (öffnet Finder)
open smb://server/share

# Mount-Point vorbereiten
mkdir -p /Volumes/NAS
mount_smbfs //max@nas.local/Daten /Volumes/NAS
```

**NFS:**

```zsh
# NFS-Mount
sudo mount -t nfs server:/export/share /Volumes/NFS

# Mit Optionen
sudo mount -t nfs -o resvport,rw server:/share /Volumes/NFS

# NFS-Exports auf Server anzeigen
showmount -e server
```

**AFP (Apple Filing Protocol, veraltet):**

```zsh
# AFP-Mount
mount_afp afp://user@server/share /Volumes/AFP

# Über Finder
open afp://server/share
```

**WebDAV:**

```zsh
# WebDAV mounten
mount_webdav https://server/dav /Volumes/WebDAV
```

**Netzlaufwerk per Skript verbinden:**

```zsh
#!/bin/zsh
# mount-nas.sh

SERVER="nas.local"
SHARE="Daten"
USER="max"
MOUNTPOINT="/Volumes/NAS"

# Mount-Point erstellen falls nötig
[[ -d "$MOUNTPOINT" ]] || mkdir -p "$MOUNTPOINT"

# Prüfen ob bereits gemountet
if mount | grep -q "$MOUNTPOINT"; then
    echo "Bereits verbunden"
    exit 0
fi

# Mounten (Passwort aus Keychain)
mount_smbfs "//${USER}@${SERVER}/${SHARE}" "$MOUNTPOINT"

if [[ $? -eq 0 ]]; then
    echo "Erfolgreich verbunden: $MOUNTPOINT"
else
    echo "Fehler beim Verbinden"
    exit 1
fi
```

### 3.4    `/etc/fstab` – Automatisches Mounten

Die Datei `/etc/fstab` definiert Volumes, die beim Systemstart automatisch gemountet werden.

**Syntax:**

```
DEVICE  MOUNTPOINT  FSTYPE  OPTIONS  DUMP  PASS
```

**Beispiele:**

```zsh
# /etc/fstab bearbeiten
sudo nano /etc/fstab
```

```
# NFS-Share beim Start mounten
server:/export/data  /Volumes/Data  nfs  rw,resvport  0  0

# SMB-Share (mit Benutzername)
//user@server/share  /Volumes/Share  smbfs  -N  0  0

# USB-Laufwerk nach UUID
UUID=1234-5678  /Volumes/USB  msdos  rw  0  0

# Volume read-only mounten
LABEL=Backup  /Volumes/Backup  apfs  ro  0  0
```

**UUID ermitteln:**

```zsh
diskutil info /dev/disk2s1 | grep "Volume UUID"
```

**fstab neu einlesen:**

```zsh
sudo automount -vc
```

> [!NOTE]
> macOS verwendet primär `/etc/auto_master` für Automounts. Für Netzlaufwerke ist oft ein Login-Item oder launchd-Job praktischer.

**Automatisches Mounten mit launchd:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.user.mount-nas</string>

    <key>ProgramArguments</key>
    <array>
        <string>/usr/bin/mount_smbfs</string>
        <string>//user@server/share</string>
        <string>/Volumes/NAS</string>
    </array>

    <key>RunAtLoad</key>
    <true/>

    <key>WatchPaths</key>
    <array>
        <string>/etc/resolv.conf</string>
    </array>
</dict>
</plist>
```

## 4    Disk Images & Sparse Files

Disk Images sind Container-Dateien, die ein virtuelles Dateisystem enthalten. Unter macOS sind sie allgegenwärtig für Software-Verteilung, verschlüsselte Container und Backups.

**Typen von Disk Images:**

| Typ | Endung | Beschreibung |
| --- | ------ | ------------ |
| Read-only | `.dmg` | Komprimiert, nicht veränderbar |
| Read/write | `.dmg` | Veränderbar, feste Größe |
| Sparse | `.sparseimage` | Wächst bei Bedarf |
| Sparse Bundle | `.sparsebundle` | Wächst, besteht aus Bändern |
| DVD/CD Master | `.cdr` | Für Brennen |

**Sparse Files:**

Ein Sparse File belegt nur so viel Platz wie tatsächlich geschriebene Daten:

```zsh
# Sparse File erstellen (10GB logisch, 0 Bytes real)
dd if=/dev/zero of=sparse.img bs=1 count=0 seek=10G

# Tatsächliche vs. logische Größe
ls -lh sparse.img    # zeigt 10G
du -h sparse.img     # zeigt 0B (oder wenige KB)

# Mit stat
stat -f "Logisch: %z, Blöcke: %b" sparse.img
```

## 5    Erweiterte Datei- und Backupwerkzeuge

In diesem Kapitel geht es um Befehle, mit denen man ganze Verzeichnisse oder Datenbestände effizient **kopieren, klonen oder synchronisieren** kannst.

Während einfache Kopierbefehle wie `cp` für einzelne Dateien ausreichen, sind `ditto` und `rsync` leistungsfähige Werkzeuge für komplexere Aufgaben, wie beispielsweise die Erstellung von Backups, der Übertragung großer Datenmengen oder beim der Spiegelung von Ordnern inklusive Berechtigungen, Metadaten und versteckten Dateien.

### 5.1    `cp` – Der klassische Kopierbefehl

`cp` ist der einfachste Befehl zum Kopieren von Dateien oder Verzeichnissen. Mit der Option `-R` (rekursiv) kopiert er auch Unterordner.

**Beispiel:**

```zsh
cp -R Quelle Ziel
```

**Verwendung:**

Für schnelle, einfache Kopiervorgänge, etwa einzelne Dateien oder kleine Ordner.

**Vorteile:**

- immer verfügbar, sehr einfach
- Schnell bei wenigen Dateien

**Nachteile:**

- Keine Fortschrittsanzeige
- Keine Synchronisation
- Kopiert oft keine versteckten Metadaten
- Keine Option zum Erstellen von Archiven

### 5.2    `ditto` – Systemgerechtes Kopieren und Archivieren

Mit dem Befehl `ditto` werden Verzeichnisse kopiert/dupliziert.

> [!NOTE]
> Im Gegensatz zu `cp` kann `ditto` Ressourcendateien, Metadaten, Rechte, unsichtbare Dateien und Paketstrukturen (z. B. App-Bundles) korrekt handhaben.

**Syntax:**

```zsh
ditto [Optionen] Quelle Ziel
```

**Verwendung:**

Ideal zum Klonen von Projekten, App-Bundles oder Backups auf demselben System. Auch nützlich zum Erstellen von ZIP-Archiven mit `-c -k`.

**Vorteile:**

- Bewahrt Metadaten, ACLs, Ressourcen
- Zusammenführen statt stumpfem Überschreiben
- Kann ZIP-Archive erstellen
- macOS-optimiert

**Nachteile:**

- Arbeitet nur lokal (kein Netzwerktransfer)
- Weniger effizient bei großen oder häufigen Synchronisationen

#### 5.2.1    Beispiele

Ordner vollständig kopieren (inklusive versteckter Dateien wie `.DS_Store` und `.git`):

```zsh
ditto ~/Projekte/ProjektA ~/Backup/ProjektA
```

Eine App korrekt klonen (inkl. Metadaten):

```zsh
sudo ditto /Applications/App.app /Applications/App-Kopie.app
```

Nur neue oder geänderte Dateien kopieren:

```zsh
ditto --update ~/Projekte/ ~/Backup/Projekte/
```

Eine ZIP-Datei aus einem Ordner erzeugen:

```zsh
ditto -c -k --sequesterRsrc --keepParent "MeinOrdner" "MeinOrdner.zip"
```

→ Erstellt ein ZIP-Archiv, das auch Ressourcendateien und Metadaten beibehält.

#### 5.2.2    Häufige Optionen

| Option                         | Bedeutung                                                           |
| ------------------------------ | ------------------------------------------------------------------- |
| `--update`                     | Nur neuere Dateien kopieren                                         |
| `--norsrc`                     | Ressourcen-Forks **nicht** mitkopieren                              |
| `--rsrc`                       | Ressourcen-Forks mitkopieren (Standardverhalten)                    |
| `-V`                           | Zeigt detaillierte Ausgaben (Verbose-Modus)                         |
| `-X`                           | Bewahrt erweiterte Attribute (ACLs, Metadaten)                      |
| `-c -k`                        | Erstellt ein ZIP-Archiv                                             |
| `--sequesterRsrc --keepParent` | Erhält Ressourcen und übergeordnete Ordnerstruktur beim Archivieren |

#### 5.2.3    **Typische Anwendungsfälle**

- Vollständige Kopien von App-Bundles, Projekten oder Verzeichnissen unter Beibehaltung macOS-spezifischer Dateiattribute (z. B. ACLs, HFS+ Metadaten)
- Erstellung von Backups und ZIP-Archiven
- Zusammenführen von Ordnern mit Erhalt von Metadaten
- Alternative zu `cp -R` mit besserer macOS-Kompatibilität
- Installer-Skripte und Backup-Tools

### 5.3    `rsync` – Effiziente Synchronisation und Spiegelung

Der Befehl `rsync` wird verwendet, um Dateien und Verzeichnisse effizient zu synchronisieren – lokal oder über ein Netzwerk. Er überträgt nur geänderte Teile von Dateien, was ihn besonders schnell und bandbreitenschonend macht. `rsync` ist ideal für Backups, Spiegelungen und regelmäßige Synchronisationen zwischen Verzeichnissen oder Rechnern.

**Syntax:**

```zsh
rsync [Optionen] Quelle Ziel
```

**Verwendung:**

Für effiziente Backups, Netzwerkübertragungen und automatisierte Synchronisationen.

**Vorteile:**

- Überträgt nur geänderte Dateien → sehr schnell bei Wiederholungen
- Netzwerkfähig (via SSH)
- Fortschrittsanzeige, Testlauf-Modus (-n)
- Ideal für Skripte und Automatisierung

**Nachteile:**

- Etwas komplexer in der Handhabung
- Irrtümlich gesetzte Optionen (z. B. `--delete`) können Daten löschen
- Nicht alle macOS-spezifischen Metadaten werden immer exakt kopiert

#### 5.3.1    Beispiele

**Einen Ordner lokal synchronisieren:**

```zsh
rsync -av ~/Projekte/MeinApp/ ~/Backup/MeinApp/
```

→ Kopiert alle Dateien und Unterordner, behält Berechtigungen und Zeiten bei, synchronisiert nur geänderte Dateien.

**Dateien auf einen anderen Rechner übertragen:**

```zsh
rsync -avz ~/Projekte/MeinApp/ user@server:/Users/user/Backup/MeinApp/
```

→ Überträgt den Ordner über SSH auf einen entfernten Rechner. Die Option `-z` komprimiert die Daten während der Übertragung.

**Nur geänderte Dateien aktualisieren:**

```zsh
rsync -avu ~/Projekte/MeinApp/ ~/Backup/MeinApp/
```

→ Die Option `-u` sorgt dafür, dass nur neuere Dateien kopiert werden.

**Dateien löschen, die im Ziel nicht mehr existieren:**

```zsh
rsync -av --delete ~/Projekte/MeinApp/ ~/Backup/MeinApp/
```

→ Hält das Zielverzeichnis exakt synchron mit der Quelle (Achtung: löscht zusätzliche Dateien im Ziel!).

#### 5.3.2    Häufige Optionen

| **Option**   | **Beschreibung**                                                              |
| ------------ | ----------------------------------------------------------------------------- |
| `-n`         | "Testlauf" – zeigt, was passieren würde, ohne Änderungen vorzunehmen          |
| `-a`         | Archivmodus: kopiert rekursiv und bewahrt Berechtigungen, Zeiten und Symlinks |
| `-v`         | Verbose: zeigt detaillierte Fortschrittsinformationen                         |
| `-z`         | Komprimiert Daten während der Übertragung                                     |
| `-u`         | Überspringt Dateien, die im Ziel neuer sind                                   |
| `--delete`   | Löscht Dateien im Ziel, die in der Quelle nicht mehr existieren               |
| `--progress` | Zeigt den Fortschritt jeder Dateiübertragung                                  |
| `-e ssh`     | Erzwingt die Verwendung von SSH (meist automatisch aktiv)                     |

#### 5.3.3    Typische Anwendungsfälle

- Erstellung und Aktualisierung von Backups´
- Synchronisation zwischen zwei Festplatten oder Rechnern
- Spiegelung von Verzeichnisstrukturen auf Servern
- Automatisierte Sicherungsskripte (z. B. via `cron`)
- Übertragung großer Datenmengen mit minimaler Redundanz

### 5.4    Vergleich: `cp`, `ditto` und `rsync`

Es gibt mehrere Befehle, um Dateien und Ordner zu kopieren oder zu sichern. Die drei wichtigsten sind `cp`, `ditto` und `rsync`. Obwohl sie ähnliche Aufgaben erfüllen, unterscheiden sie sich deutlich in Funktionsumfang, Zuverlässigkeit und Einsatzgebiet.

| Merkmal                                  | `cp`            | `ditto`         | `rsync`             |
| ---------------------------------------- | ------------- | ------------- | ----------------- |
| **Kopiert Verzeichnisse rekursiv**       | ✅ (`-R`)      | ✅             | ✅                 |
| **Bewahrt Metadaten / ACLs**             | ⚠️ Teilweise  | ✅ Vollständig | ⚠️ Teilweise      |
| **Versteckte Dateien (.git, .DS_Store)** | ❌ Nicht immer | ✅             | ✅                 |
| **Nur geänderte Dateien kopieren**       | ❌             | ❌             | ✅                 |
| **Fortschrittsanzeige**                  | ❌             | ⚠️ (mit `-V`) | ✅ (`--progress`)  |
| **Zusammenführen statt Überschreiben**   | ❌             | ✅             | ✅                 |
| **Erstellung von Archiven (ZIP etc.)**   | ❌             | ✅ (`-c -k`)   | ❌                 |
| **Netzwerkfähig**                        | ❌             | ❌             | ✅ (SSH)           |
| **Skript-/Backup-tauglich**              | ⚠️ Einfach    | ✅ Lokal       | ✅ Sehr gut        |
| **Komplexität**                          | ⭐ Einfach     | ⭐⭐ Mittel     | ⭐⭐⭐ Anspruchsvoll |

- **`cp`**: Für einfache, schnelle Kopien – ideal bei Einzeldaten.
- **`ditto`**: Für vollständige, macOS-gerechte Kopien und Archive.
- **`rsync`**: Für effiziente, wiederholbare Backups und Synchronisationen – auch über Netzwerke.

### 5.5    `hdiutil` – Disk Images erstellen und verwalten

`hdiutil` ist das mächtige Kommandozeilen-Tool für alle Disk-Image-Operationen unter macOS.

**Image erstellen:**

```zsh
# Leeres DMG (100 MB, HFS+)
hdiutil create -size 100m -fs HFS+ -volname "MeinVolume" MeinImage.dmg

# APFS-Image
hdiutil create -size 100m -fs APFS -volname "MeinVolume" MeinImage.dmg

# Aus Ordner erstellen
hdiutil create -srcfolder ~/MeinOrdner -volname "Backup" Backup.dmg

# Mit Komprimierung
hdiutil create -srcfolder ~/MeinOrdner -format UDZO -volname "Backup" Backup.dmg
```

**Image-Formate:**

| Format | Code | Beschreibung |
| ------ | ---- | ------------ |
| Read/write | `UDRW` | Veränderbar |
| Compressed | `UDZO` | zlib-komprimiert, read-only |
| LZMA compressed | `ULMO` | Stark komprimiert |
| Sparse | `UDSP` | Wachsend, veränderbar |
| Sparse bundle | `UDSB` | Band-basiert, wachsend |
| DVD/CD Master | `UDTO` | Für Brennen |

**Image mounten:**

```zsh
# Standard-Mount (im Finder sichtbar)
hdiutil attach MeinImage.dmg

# Mount ohne Finder-Fenster
hdiutil attach MeinImage.dmg -nobrowse

# Mount an bestimmtem Punkt
hdiutil attach MeinImage.dmg -mountpoint /Volumes/MeinMount

# Read-only mounten
hdiutil attach MeinImage.dmg -readonly

# Ohne Verifizierung (schneller)
hdiutil attach MeinImage.dmg -noverify
```

**Image aushängen:**

```zsh
# Nach Volume-Name
hdiutil detach /Volumes/MeinVolume

# Erzwingen
hdiutil detach /Volumes/MeinVolume -force

# Alle Images aushängen
hdiutil detach -all
```

**Image konvertieren:**

```zsh
# Zu komprimiertem Read-only
hdiutil convert original.dmg -format UDZO -o komprimiert.dmg

# Zu read/write
hdiutil convert readonly.dmg -format UDRW -o readwrite.dmg

# Zu Sparse Bundle
hdiutil convert original.dmg -format UDSB -o sparse.sparsebundle
```

**Image-Größe ändern:**

```zsh
# Größe erweitern (nur sparse/read-write)
hdiutil resize -size 200m MeinImage.dmg

# Auf minimale Größe schrumpfen
hdiutil resize -size min MeinImage.dmg

# Um Betrag erweitern
hdiutil resize -growonly -size 100m MeinImage.dmg
```

**Image-Informationen:**

```zsh
# Image-Info anzeigen
hdiutil imageinfo MeinImage.dmg

# Nur Format
hdiutil imageinfo -format MeinImage.dmg

# Prüfsumme verifizieren
hdiutil verify MeinImage.dmg
```

### 5.6    Sparse Images und Sparse Bundles

Sparse Images wachsen dynamisch und belegen nur so viel Platz wie der tatsächliche Inhalt.

**Sparse Image erstellen:**

```zsh
# Sparse Image (max 50 GB, wächst bei Bedarf)
hdiutil create -size 50g -type SPARSE -fs APFS -volname "Daten" Daten.sparseimage

# Sparse Bundle (bessere Performance bei Time Machine/Netzwerk)
hdiutil create -size 50g -type SPARSEBUNDLE -fs APFS -volname "Daten" Daten.sparsebundle
```

**Unterschied Sparse Image vs. Sparse Bundle:**

| Merkmal | Sparse Image | Sparse Bundle |
| ------- | ------------ | ------------- |
| Dateistruktur | Eine Datei | Ordner mit Bändern |
| Backup-freundlich | ❌ Ganze Datei | ✅ Nur geänderte Bänder |
| Time Machine | Nicht optimal | Empfohlen |
| Netzwerk | Langsam | Schneller |
| Kompaktieren | Möglich | Möglich |

**Sparse Bundle Struktur:**

```zsh
ls Daten.sparsebundle/
# Info.plist
# bands/
#   0
#   1
#   2
#   ...
# token
```

**Größe kompaktieren (ungenutzten Platz freigeben):**

```zsh
# Erst aushängen
hdiutil detach /Volumes/Daten

# Kompaktieren
hdiutil compact Daten.sparsebundle

# Oder für Sparse Image
hdiutil compact Daten.sparseimage
```

**Bandgröße für Sparse Bundle festlegen:**

```zsh
# 8 MB Bänder (Standard)
hdiutil create -size 50g -type SPARSEBUNDLE -fs APFS \
    -imagekey sparse-band-size=16384 \
    -volname "Daten" Daten.sparsebundle

# sparse-band-size ist in 512-Byte-Sektoren
# 16384 * 512 = 8 MB
# 131072 * 512 = 64 MB
```

### 5.7    Verschlüsselte Disk Images

Disk Images können mit AES-128 oder AES-256 verschlüsselt werden – ideal für sensible Daten.

**Verschlüsseltes Image erstellen:**

```zsh
# AES-256 (empfohlen)
hdiutil create -size 100m -fs APFS -volname "Geheim" \
    -encryption AES-256 -stdinpass Geheim.dmg
# Passwort eingeben...

# AES-128 (schneller)
hdiutil create -size 100m -fs APFS -volname "Geheim" \
    -encryption AES-128 Geheim.dmg

# Aus Ordner mit Verschlüsselung
hdiutil create -srcfolder ~/Vertraulich \
    -encryption AES-256 -format UDZO \
    -volname "Vertraulich" Vertraulich.dmg
```

**Verschlüsseltes Sparse Bundle (empfohlen für wachsende Daten):**

```zsh
hdiutil create -size 10g -type SPARSEBUNDLE -fs APFS \
    -encryption AES-256 -volname "Tresor" Tresor.sparsebundle
```

**Verschlüsseltes Image mounten:**

```zsh
# Passwort-Abfrage
hdiutil attach Geheim.dmg

# Passwort in Keychain speichern (beim ersten Mount)
# → Option im Dialog wählen

# Passwort per stdin
echo -n "geheim123" | hdiutil attach Geheim.dmg -stdinpass
```

**Passwort ändern:**

```zsh
hdiutil chpass Geheim.dmg
# Altes Passwort eingeben
# Neues Passwort eingeben
```

**Bestehendes Image verschlüsseln:**

```zsh
# Konvertieren mit Verschlüsselung
hdiutil convert Unverschluesselt.dmg \
    -format UDZO -encryption AES-256 \
    -o Verschluesselt.dmg
```

**Sicherheits-Best-Practices:**

- AES-256 für sensitive Daten verwenden
- Starkes Passwort wählen (Passphrase)
- Passwort nicht im Schlüsselbund speichern für höchste Sicherheit
- Image nach Gebrauch aushängen
- Bei Sparse Bundles: Nach Löschen von Dateien kompaktieren

### 5.8    DMG-Dateien per Terminal

**DMG aus Ordner erstellen (App-Verteilung):**

```zsh
# Einfaches DMG
hdiutil create -srcfolder MeinApp.app \
    -volname "MeinApp" \
    -format UDZO \
    MeinApp.dmg

# Mit Hintergrundbild und Icon-Positionen
# (Erfordert erst ein Read/Write Image)
hdiutil create -srcfolder MeinApp.app \
    -volname "MeinApp" \
    -format UDRW \
    -fs HFS+ \
    temp.dmg

# Mounten und anpassen
hdiutil attach temp.dmg

# Finder-Einstellungen setzen (AppleScript oder manuell)
# ...

# Zu komprimiertem DMG konvertieren
hdiutil convert temp.dmg -format UDZO -o MeinApp.dmg
rm temp.dmg
```

**Internet-Enabled DMG (automatisches Entpacken):**

```zsh
hdiutil internet-enable -yes MeinApp.dmg
```

**DMG verifizieren:**

```zsh
# Prüfsumme verifizieren
hdiutil verify MeinApp.dmg

# Inhalt auflisten ohne Mounten
hdiutil imageinfo MeinApp.dmg
```

**DMG in ISO konvertieren:**

```zsh
hdiutil convert original.dmg -format UDTO -o converted.cdr
mv converted.cdr converted.iso
```

**ISO/CDR erstellen:**

```zsh
# Aus Ordner
hdiutil makehybrid -iso -joliet -o output.iso source_folder/

# CD/DVD-Master für Brennen
hdiutil create -srcfolder Daten -format UDTO -o Master.cdr
```

**Bootfähiges USB-Medium erstellen:**

```zsh
# macOS-Installer auf USB
sudo /Applications/Install\ macOS\ Sonoma.app/Contents/Resources/createinstallmedia \
    --volume /Volumes/USB

# Alternativ mit dd (für Linux ISOs etc.)
diskutil unmountDisk /dev/disk2
sudo dd if=linux.iso of=/dev/rdisk2 bs=1m
diskutil eject /dev/disk2
```

**Praktische Aliase:**

```zsh
# ~/.zshrc

# DMG schnell erstellen
dmg-create() {
    local src="$1"
    local name="${2:-${src:t}}"
    hdiutil create -srcfolder "$src" -volname "$name" -format UDZO "${name}.dmg"
}

# Verschlüsseltes Sparse Bundle erstellen
vault-create() {
    local name="$1"
    local size="${2:-10g}"
    hdiutil create -size "$size" -type SPARSEBUNDLE -fs APFS \
        -encryption AES-256 -volname "$name" "${name}.sparsebundle"
}

# DMG schnell mounten
alias dmg-mount='hdiutil attach'
alias dmg-unmount='hdiutil detach'
```

**Cheatsheet hdiutil:**

```
# Erstellen
hdiutil create -size 100m -fs APFS -volname NAME out.dmg
hdiutil create -srcfolder ORDNER -format UDZO out.dmg
hdiutil create -size 10g -type SPARSEBUNDLE -encryption AES-256 out.sparsebundle

# Mounten
hdiutil attach image.dmg
hdiutil attach image.dmg -mountpoint /Volumes/MOUNT
hdiutil attach image.dmg -readonly

# Aushängen
hdiutil detach /Volumes/NAME

# Konvertieren
hdiutil convert in.dmg -format UDZO -o out.dmg
hdiutil convert in.dmg -format UDRW -o out.dmg

# Größe ändern
hdiutil resize -size 200m image.sparseimage
hdiutil compact image.sparsebundle

# Info
hdiutil imageinfo image.dmg
hdiutil verify image.dmg
```
