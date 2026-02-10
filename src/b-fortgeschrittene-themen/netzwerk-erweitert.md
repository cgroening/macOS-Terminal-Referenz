# Netzwerk (erweitert)

Dieses Kapitel behandelt fortgeschrittene Netzwerk-Tools für DNS-Analyse, Verbindungsdiagnose und Dateiübertragung.

## 1    DNS & Namensauflösung

DNS (Domain Name System) übersetzt Domainnamen in IP-Adressen. Die folgenden Tools helfen bei der Diagnose von DNS-Problemen.

### 1.1    `nslookup` – DNS-Abfragen

`nslookup` ist ein klassisches Tool für DNS-Abfragen, verfügbar auf praktisch allen Systemen.

**Grundlegende Abfragen:**

```zsh
# Einfache Namensauflösung
nslookup example.com
# Server:    192.168.1.1
# Address:   192.168.1.1#53
#
# Non-authoritative answer:
# Name:      example.com
# Address:   93.184.216.34

# IPv6-Adresse abfragen
nslookup -type=AAAA example.com

# Reverse-Lookup (IP → Name)
nslookup 8.8.8.8
# Name: dns.google
```

**Bestimmten DNS-Server verwenden:**

```zsh
# Google DNS
nslookup example.com 8.8.8.8

# Cloudflare DNS
nslookup example.com 1.1.1.1

# Lokaler DNS
nslookup example.com 192.168.1.1
```

**Record-Typen abfragen:**

```zsh
# MX-Records (Mail)
nslookup -type=MX example.com

# NS-Records (Nameserver)
nslookup -type=NS example.com

# TXT-Records (SPF, DKIM, etc.)
nslookup -type=TXT example.com

# SOA-Record (Start of Authority)
nslookup -type=SOA example.com

# CNAME-Record (Alias)
nslookup -type=CNAME www.example.com

# Alle Records
nslookup -type=ANY example.com
```

**Interaktiver Modus:**

```zsh
nslookup
> server 8.8.8.8
> set type=MX
> example.com
> exit
```

**Häufige Record-Typen:**

| Typ | Beschreibung |
| --- | ------------ |
| `A` | IPv4-Adresse |
| `AAAA` | IPv6-Adresse |
| `MX` | Mail-Server |
| `NS` | Nameserver |
| `TXT` | Text-Records (SPF, DKIM) |
| `CNAME` | Alias/Weiterleitung |
| `SOA` | Zonen-Autorität |
| `PTR` | Reverse-Lookup |
| `SRV` | Service-Records |

### 1.2    `dig` – Erweiterte DNS-Analyse

`dig` (Domain Information Groper) ist das mächtigste DNS-Tool mit detaillierten Ausgaben.

**Installation (falls nicht vorhanden):**

```zsh
brew install bind  # enthält dig
```

**Grundlegende Abfragen:**

```zsh
# Standard-Abfrage
dig example.com

# Kurzform (nur Antwort)
dig +short example.com
# 93.184.216.34

# Bestimmter Record-Typ
dig example.com MX
dig example.com AAAA
dig example.com TXT
```

**Ausgabe verstehen:**

```zsh
dig example.com

# ;; QUESTION SECTION:
# ;example.com.                   IN      A
#
# ;; ANSWER SECTION:
# example.com.            86400   IN      A       93.184.216.34
#
# ;; Query time: 23 msec
# ;; SERVER: 192.168.1.1#53(192.168.1.1)
```

| Sektion | Beschreibung |
| ------- | ------------ |
| QUESTION | Gestellte Anfrage |
| ANSWER | Antwort-Records |
| AUTHORITY | Zuständige Nameserver |
| ADDITIONAL | Zusätzliche Infos |
| Query time | Antwortzeit |
| SERVER | Verwendeter DNS-Server |

**Bestimmten DNS-Server verwenden:**

```zsh
# Mit @server
dig @8.8.8.8 example.com
dig @1.1.1.1 example.com +short
```

**Erweiterte Optionen:**

```zsh
# Alle Sektionen anzeigen
dig example.com +all

# Nur Antwort-Sektion
dig example.com +noall +answer

# Ohne Kommentare
dig example.com +nocomments

# Mit Statistiken
dig example.com +stats

# Trace: Komplette DNS-Auflösung verfolgen
dig example.com +trace

# Reverse-Lookup
dig -x 8.8.8.8
```

**Batch-Abfragen:**

```zsh
# Mehrere Domains
dig example.com google.com github.com +short

# Aus Datei
dig -f domains.txt +short
```

**DNS-Propagation prüfen:**

```zsh
# Bei verschiedenen DNS-Servern abfragen
for dns in 8.8.8.8 1.1.1.1 9.9.9.9; do
    echo "=== $dns ==="
    dig @$dns example.com +short
done
```

**Praktische Beispiele:**

```zsh
# SPF-Record prüfen
dig example.com TXT +short | grep spf

# Alle MX-Records mit Priorität
dig example.com MX +short
# 10 mail.example.com.
# 20 mail2.example.com.

# DNSSEC validieren
dig example.com +dnssec

# TTL (Time-to-Live) anzeigen
dig example.com | grep -A1 "ANSWER SECTION"
```

### 1.3    `host` – Einfache Namensauflösung

`host` bietet eine einfachere, lesbarere Ausgabe als dig.

**Grundlegende Nutzung:**

```zsh
# Namensauflösung
host example.com
# example.com has address 93.184.216.34
# example.com has IPv6 address 2606:2800:220:1:248:1893:25c8:1946
# example.com mail is handled by 0 .

# Reverse-Lookup
host 8.8.8.8
# 8.8.8.8.in-addr.arpa domain name pointer dns.google.
```

**Record-Typen:**

```zsh
# Nur bestimmten Typ
host -t MX example.com
host -t NS example.com
host -t TXT example.com
host -t AAAA example.com
```

**DNS-Server angeben:**

```zsh
host example.com 8.8.8.8
```

**Optionen:**

| Option | Beschreibung |
| ------ | ------------ |
| `-t TYPE` | Record-Typ |
| `-a` | Alle Record-Typen |
| `-v` | Verbose (wie dig) |
| `-4` | Nur IPv4 |
| `-6` | Nur IPv6 |

**Vergleich der DNS-Tools:**

| Merkmal | nslookup | dig | host |
| ------- | -------- | --- | ---- |
| Detailgrad | Mittel | Hoch | Niedrig |
| Lesbarkeit | Gut | Technisch | Sehr gut |
| Scripting | Möglich | Sehr gut | Gut |
| Verfügbarkeit | Überall | Meist installiert | Meist installiert |
| Empfehlung | Legacy | Experten | Schnelle Checks |

## 2    Verbindungen & Ports

### 2.1    `netstat` – Netzwerkstatistiken

`netstat` zeigt Netzwerkverbindungen, Routing-Tabellen und Interface-Statistiken.

> [!TIP]
> Unter macOS ist `netstat` verfügbar, aber `lsof -i` ist oft praktischer für Verbindungen. Es zeigt direkt den Prozessnamen und die PID zu jeder Verbindung, während `netstat` unter macOS diese Information nicht liefert. Außerdem bietet `lsof` flexiblere Filteroptionen nach Port, Protokoll und Prozess.

**Aktive Verbindungen:**

```zsh
# Alle Verbindungen
netstat -an

# Nur TCP
netstat -an -p tcp

# Nur UDP
netstat -an -p udp

# Nur lauschende Ports
netstat -an | grep LISTEN
```

**Ausgabe verstehen:**

```
Proto  Local Address          Foreign Address        State
tcp4   192.168.1.100.52341    93.184.216.34.443     ESTABLISHED
tcp4   *.80                   *.*                    LISTEN
```

| Spalte | Beschreibung |
| ------ | ------------ |
| Proto | Protokoll (tcp4, tcp6, udp) |
| Local Address | Lokale IP:Port |
| Foreign Address | Remote IP:Port |
| State | Verbindungsstatus |

**Verbindungsstatus:**

| Status | Beschreibung |
| ------ | ------------ |
| `LISTEN` | Wartet auf Verbindungen |
| `ESTABLISHED` | Aktive Verbindung |
| `TIME_WAIT` | Warten auf Timeout |
| `CLOSE_WAIT` | Warten auf Schließen |
| `SYN_SENT` | Verbindungsaufbau |

**Routing-Tabelle:**

```zsh
netstat -rn
# Destination        Gateway            Flags
# default            192.168.1.1        UGSc
# 192.168.1.0/24     link#6             UCS
```

**Interface-Statistiken:**

```zsh
# Alle Interfaces
netstat -i

# Mit Byte-Zählern
netstat -ib

# Bestimmtes Interface
netstat -I en0
```

**Nützliche Kombinationen:**

```zsh
# Offene Ports nach Anzahl sortiert
netstat -an | grep ESTABLISHED | awk '{print $5}' | cut -d. -f1-4 | sort | uniq -c | sort -rn

# Verbindungen pro Status
netstat -an | awk '/^tcp/ {print $6}' | sort | uniq -c

# Lauschende Dienste
netstat -an -p tcp | grep LISTEN
```

### 2.2    `lsof -i` – Offene Netzwerkverbindungen

`lsof` (List Open Files) ist unter macOS das bevorzugte Tool für Netzwerkdiagnose.

**Grundlegende Nutzung:**

```zsh
# Alle Netzwerkverbindungen
lsof -i

# Mit numerischen Ports (schneller)
lsof -i -n -P
```

**Nach Port filtern:**

```zsh
# Bestimmter Port
lsof -i :80
lsof -i :443
lsof -i :3000

# Port-Bereich
lsof -i :8000-9000

# Mehrere Ports
lsof -i :80 -i :443
```

**Nach Protokoll filtern:**

```zsh
# Nur TCP
lsof -i tcp

# Nur UDP
lsof -i udp

# Nur IPv4
lsof -i 4

# Nur IPv6
lsof -i 6

# Kombiniert
lsof -i tcp:443
```

**Nach Prozess/Benutzer:**

```zsh
# Bestimmter Prozess
lsof -i -c nginx
lsof -i -c python

# Bestimmter Benutzer
lsof -i -u max

# Bestimmte PID
lsof -i -p 1234
```

**Verbindungsstatus:**

```zsh
# Nur lauschende Ports
lsof -i -sTCP:LISTEN

# Nur etablierte Verbindungen
lsof -i -sTCP:ESTABLISHED

# Nur Verbindungen zu bestimmtem Host
lsof -i @192.168.1.100
lsof -i @google.com
```

**Ausgabe verstehen:**

```
COMMAND   PID USER   FD   TYPE   DEVICE SIZE/OFF NODE NAME
nginx    1234 root   6u  IPv4  0x1234     0t0  TCP *:http (LISTEN)
Chrome   5678 max   42u  IPv4  0x5678     0t0  TCP 192.168.1.100:52341->93.184.216.34:https (ESTABLISHED)
```

| Spalte | Beschreibung |
| ------ | ------------ |
| COMMAND | Programmname |
| PID | Prozess-ID |
| USER | Benutzer |
| FD | File Descriptor |
| TYPE | IPv4/IPv6 |
| NAME | Verbindungsdetails |

**Praktische Beispiele:**

```zsh
# Was blockiert Port 8080?
lsof -i :8080

# Prozess auf Port beenden
kill $(lsof -t -i :8080)

# Alle Verbindungen eines Programms
lsof -i -c Chrome -n -P

# Verbindungen zu bestimmter Domain
lsof -i @github.com

# Lauschende Ports mit Prozessnamen
lsof -i -P -n | grep LISTEN

# Schnelle Übersicht
lsof -i -n -P | head -20
```

**Aliase für häufige Abfragen:**

```zsh
# ~/.zshrc
alias ports='lsof -i -P -n | grep LISTEN'
alias conn='lsof -i -P -n'
alias whoport='lsof -i -P -n -sTCP:LISTEN | grep'
```

### 2.3    `nc` (netcat) – Netzwerk-Debugging

`nc` (netcat) ist das "Schweizer Taschenmesser" für Netzwerk-Debugging. Es kann Verbindungen öffnen, auf Ports lauschen und Daten übertragen.

**Port-Scan:**

```zsh
# Einzelner Port
nc -zv host.com 80
# Connection to host.com port 80 [tcp/http] succeeded!

# Port-Bereich
nc -zv host.com 20-25

# Schneller Scan (ohne DNS)
nc -zv -n 192.168.1.1 22

# Mit Timeout
nc -zv -w 3 host.com 80
```

**Port-Verfügbarkeit testen:**

```zsh
# HTTP-Port offen?
nc -zv google.com 80 443

# SSH erreichbar?
nc -zv -w 5 server.com 22
```

**Auf Port lauschen (Server):**

```zsh
# Einfacher Server auf Port 1234
nc -l 1234

# Mit Verbose-Output
nc -lv 1234

# UDP-Server
nc -lu 1234
```

**Verbindung herstellen (Client):**

```zsh
# Zu Server verbinden
nc host.com 1234

# Nachricht senden
echo "Hello" | nc host.com 1234

# HTTP-Request manuell
echo -e "GET / HTTP/1.1\r\nHost: example.com\r\n\r\n" | nc example.com 80
```

**Dateiübertragung:**

```zsh
# Empfänger (lauscht)
nc -l 1234 > received_file.txt

# Sender
nc receiver.com 1234 < file.txt

# Mit Fortschritt (via pv)
pv file.txt | nc receiver.com 1234
```

**Chat zwischen zwei Rechnern:**

```zsh
# Rechner A (Server)
nc -l 1234

# Rechner B (Client)
nc rechner-a.local 1234

# Jetzt kann man in beide Richtungen tippen
```

**Banner-Grabbing:**

```zsh
# SSH-Version ermitteln
echo "" | nc -v -w 3 server.com 22
# SSH-2.0-OpenSSH_8.4

# SMTP-Banner
nc -v mail.example.com 25

# HTTP-Header
echo -e "HEAD / HTTP/1.0\r\n\r\n" | nc example.com 80
```

**Proxy / Port-Weiterleitung:**

```zsh
# Einfacher Proxy (erfordert Schleife für mehrere Verbindungen)
nc -l 8080 | nc remote.com 80
```

**Optionen:**

| Option | Beschreibung |
| ------ | ------------ |
| `-l` | Listen-Modus (Server) |
| `-v` | Verbose |
| `-z` | Zero-I/O (nur Scan) |
| `-w SEC` | Timeout |
| `-n` | Kein DNS |
| `-u` | UDP statt TCP |
| `-p PORT` | Quell-Port |
| `-k` | Keep-alive (mehrere Verbindungen) |

**Debugging-Beispiele:**

```zsh
# Ist Dienst erreichbar?
nc -zv -w 3 db.example.com 5432 && echo "PostgreSQL OK" || echo "FEHLER"

# Alle gängigen Ports scannen
for port in 22 80 443 3306 5432 6379 27017; do
    nc -zv -w 2 server.com $port 2>&1 | grep -q succeeded && echo "Port $port: OFFEN"
done
```

## 3    Dateiübertragung

### 3.1    `wget` – Dateien herunterladen

`wget` ist ein robustes Download-Tool mit Unterstützung für Wiederaufnahme und rekursive Downloads.

**Installation:**

```zsh
brew install wget
```

**Einfacher Download:**

```zsh
# Datei herunterladen
wget https://example.com/file.zip

# Mit anderem Namen speichern
wget -O myfile.zip https://example.com/file.zip

# In bestimmtes Verzeichnis
wget -P ~/Downloads https://example.com/file.zip
```

**Abgebrochenen Download fortsetzen:**

```zsh
wget -c https://example.com/large-file.zip
```

**Mehrere Dateien:**

```zsh
# Aus Liste
wget -i urls.txt

# Mehrere URLs
wget https://example.com/file1.zip https://example.com/file2.zip
```

**Hintergrund-Download:**

```zsh
# Im Hintergrund mit Log
wget -b -o download.log https://example.com/file.zip

# Fortschritt prüfen
tail -f download.log
```

**Geschwindigkeit begrenzen:**

```zsh
# Max 1 MB/s
wget --limit-rate=1m https://example.com/file.zip

# Max 500 KB/s
wget --limit-rate=500k https://example.com/file.zip
```

**Webseite spiegeln:**

```zsh
# Komplette Website herunterladen
wget --mirror --convert-links --adjust-extension --page-requisites \
     --no-parent https://example.com/

# Kurzform
wget -mkEpnp https://example.com/
```

**Mit Authentifizierung:**

```zsh
# HTTP-Auth
wget --user=username --password=secret https://example.com/protected/file.zip

# Passwort interaktiv
wget --user=username --ask-password https://example.com/protected/file.zip
```

**Cookies und Header:**

```zsh
# Mit Cookie
wget --header="Cookie: session=abc123" https://example.com/

# Custom User-Agent
wget --user-agent="Mozilla/5.0" https://example.com/

# Referer setzen
wget --referer="https://google.com" https://example.com/file.zip
```

**Wichtige Optionen:**

| Option | Beschreibung |
| ------ | ------------ |
| `-O FILE` | Ausgabedatei |
| `-P DIR` | Ausgabeverzeichnis |
| `-c` | Fortsetzen |
| `-b` | Hintergrund |
| `-q` | Quiet (keine Ausgabe) |
| `-v` | Verbose |
| `--limit-rate` | Geschwindigkeit begrenzen |
| `-r` | Rekursiv |
| `-l DEPTH` | Rekursionstiefe |
| `-np` | No parent (nicht höher) |
| `-k` | Links konvertieren |
| `-t N` | Wiederholungsversuche |
| `--timeout=SEC` | Timeout |

**Alternative: curl**

```zsh
# curl ist auf macOS vorinstalliert
curl -O https://example.com/file.zip

# Mit Ausgabename
curl -o myfile.zip https://example.com/file.zip

# Fortsetzen
curl -C - -O https://example.com/file.zip

# Fortschrittsbalken
curl -# -O https://example.com/file.zip
```

### 3.2    `scp` – Sichere Kopie über SSH

`scp` kopiert Dateien verschlüsselt über SSH zwischen Rechnern.

**Syntax:**

```zsh
scp [Optionen] QUELLE ZIEL
```

**Datei hochladen:**

```zsh
# Lokale Datei auf Server
scp datei.txt user@server:/pfad/

# Mit Port
scp -P 2222 datei.txt user@server:/pfad/

# Mit SSH-Key
scp -i ~/.ssh/key datei.txt user@server:/pfad/
```

**Datei herunterladen:**

```zsh
# Von Server auf lokal
scp user@server:/pfad/datei.txt ./

# Mit anderem Namen
scp user@server:/pfad/datei.txt lokaler-name.txt
```

**Verzeichnis kopieren:**

```zsh
# Rekursiv (ganzes Verzeichnis)
scp -r ordner/ user@server:/pfad/

# Von Server
scp -r user@server:/pfad/ordner/ ./
```

**Zwischen zwei Servern:**

```zsh
scp user1@server1:/pfad/datei.txt user2@server2:/pfad/
```

**Mit SSH-Config:**

```zsh
# Wenn in ~/.ssh/config definiert:
# Host myserver
#     HostName server.com
#     User admin

scp datei.txt myserver:/pfad/
```

**Optionen:**

| Option | Beschreibung |
| ------ | ------------ |
| `-r` | Rekursiv (Verzeichnisse) |
| `-P PORT` | SSH-Port |
| `-i KEY` | Identity-File |
| `-p` | Berechtigungen/Zeiten erhalten |
| `-C` | Komprimierung |
| `-q` | Quiet |
| `-v` | Verbose |
| `-l LIMIT` | Bandbreite begrenzen (Kbit/s) |

**Fortschrittsanzeige:**

```zsh
# Standard: Fortschrittsbalken aktiv
scp large-file.zip user@server:/pfad/

# Ohne Fortschritt
scp -q large-file.zip user@server:/pfad/
```

**Wildcards:**

```zsh
# Alle .txt-Dateien
scp *.txt user@server:/pfad/

# Alle Dateien eines Ordners auf Server
scp user@server:/pfad/*.log ./logs/
```

### 3.3    `rsync` – Erweiterte Synchronisation

Die Grundlagen von `rsync` werden in [[Terminal-Referenz für macOS - Teil 2 - Fortgeschrittene Themen#7.5.3 rsync – Effiziente Synchronisation und Spiegelung|rsync – Effiziente Synchronisation und Spiegelung]] behandelt. Der Fokus dieses Abschnitts liegt auf der Netzwerkübertragung.

**rsync über SSH (Standard):**

```zsh
# Auf Server kopieren
rsync -avz ~/projekt/ user@server:/backup/projekt/

# Von Server holen
rsync -avz user@server:/daten/ ~/lokale-kopie/
```

**Mit SSH-Optionen:**

```zsh
# Anderer Port
rsync -avz -e "ssh -p 2222" ~/projekt/ user@server:/backup/

# Mit SSH-Key
rsync -avz -e "ssh -i ~/.ssh/mykey" ~/projekt/ user@server:/backup/
```

**Bandbreite begrenzen:**

```zsh
# Max 1 MB/s
rsync -avz --bwlimit=1000 ~/projekt/ user@server:/backup/
```

**Nur geänderte Dateien:**

```zsh
# Delta-Transfer (nur Unterschiede)
rsync -avz --checksum ~/projekt/ user@server:/backup/
```

**Spiegel mit Löschen:**

```zsh
# Ziel exakt wie Quelle (ACHTUNG!)
rsync -avz --delete ~/projekt/ user@server:/backup/projekt/

# Erst Testlauf
rsync -avzn --delete ~/projekt/ user@server:/backup/projekt/
```

**Bestimmte Dateien aus-/einschließen:**

```zsh
# Ausschließen
rsync -avz --exclude='*.log' --exclude='node_modules/' ~/projekt/ user@server:/backup/

# Aus Datei lesen
rsync -avz --exclude-from='exclude.txt' ~/projekt/ user@server:/backup/

# Nur bestimmte Dateien
rsync -avz --include='*.py' --exclude='*' ~/projekt/ user@server:/backup/
```

**Fortschritt anzeigen:**

```zsh
# Fortschritt pro Datei
rsync -avz --progress ~/projekt/ user@server:/backup/

# Gesamtfortschritt
rsync -avz --info=progress2 ~/projekt/ user@server:/backup/
```

**Backup-Skript:**

```zsh
#!/bin/zsh
# backup-to-server.sh

SRC="$HOME/Documents/"
DEST="user@backup-server:/backups/$(hostname)/"
LOG="$HOME/logs/backup-$(date +%Y%m%d).log"

rsync -avz --delete \
    --exclude='.DS_Store' \
    --exclude='*.tmp' \
    --log-file="$LOG" \
    "$SRC" "$DEST"

echo "Backup abgeschlossen: $(date)" >> "$LOG"
```

### 3.4    `sftp` – Interaktiver Dateitransfer

`sftp` bietet eine interaktive FTP-ähnliche Oberfläche über SSH.

**Verbindung herstellen:**

```zsh
# Standard
sftp user@server

# Mit Port
sftp -P 2222 user@server

# Mit SSH-Key
sftp -i ~/.ssh/key user@server

# Direkt in Verzeichnis
sftp user@server:/pfad/
```

**SFTP-Befehle:**

| Befehl | Beschreibung |
| ------ | ------------ |
| `ls` / `dir` | Remote-Verzeichnis auflisten |
| `lls` | Lokales Verzeichnis auflisten |
| `cd DIR` | Remote-Verzeichnis wechseln |
| `lcd DIR` | Lokales Verzeichnis wechseln |
| `pwd` | Remote-Arbeitsverzeichnis |
| `lpwd` | Lokales Arbeitsverzeichnis |
| `get FILE` | Datei herunterladen |
| `put FILE` | Datei hochladen |
| `mget PATTERN` | Mehrere Dateien herunterladen |
| `mput PATTERN` | Mehrere Dateien hochladen |
| `mkdir DIR` | Verzeichnis erstellen |
| `rmdir DIR` | Verzeichnis löschen |
| `rm FILE` | Datei löschen |
| `rename OLD NEW` | Umbenennen |
| `chmod MODE FILE` | Berechtigungen ändern |
| `chown UID FILE` | Besitzer ändern |
| `exit` / `quit` | Beenden |

**Typische Session:**

```zsh
sftp user@server
sftp> cd /var/www
sftp> ls
index.html  styles.css  app.js
sftp> get index.html
Fetching /var/www/index.html to index.html
sftp> lcd ~/Desktop
sftp> put new-styles.css styles.css
Uploading new-styles.css to /var/www/styles.css
sftp> exit
```

**Mehrere Dateien:**

```zsh
sftp> mget *.log
sftp> mput *.html
```

**Rekursiv (Verzeichnisse):**

```zsh
sftp> get -r remote-folder/
sftp> put -r local-folder/
```

**Batch-Modus:**

```zsh
# Befehle aus Datei
sftp -b commands.txt user@server

# commands.txt:
# cd /var/www
# get index.html
# put styles.css
# exit

# Einzelner Befehl
echo "get /var/log/app.log" | sftp user@server
```

**Mit SSH-Config:**

```zsh
# Wenn Host in ~/.ssh/config definiert
sftp myserver
```

**Vergleich der Übertragungstools:**

| Merkmal | scp | rsync | sftp |
| ------- | --- | ----- | ---- |
| Interaktiv | ❌ | ❌ | ✅ |
| Delta-Transfer | ❌ | ✅ | ❌ |
| Fortsetzen | ❌ | ✅ | ❌ |
| Batch-fähig | ✅ | ✅ | ✅ |
| Verzeichnisse | `-r` | ✅ | `-r` |
| Bandbreiten-Limit | ✅ | ✅ | ❌ |
| Löschen am Ziel | ❌ | ✅ | ❌ |
| Anwendungsfall | Schnelle Kopie | Backup/Sync | Browsing |

**Empfehlung:**

- **scp**: Einzelne Dateien schnell kopieren
- **rsync**: Backups, Synchronisation, große Datenmengen
- **sftp**: Interaktives Arbeiten, Dateien browsen
