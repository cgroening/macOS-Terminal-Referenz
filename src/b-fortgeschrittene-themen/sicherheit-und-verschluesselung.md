# Sicherheit & Verschlüsselung

Dieses Kapitel behandelt Werkzeuge für Verschlüsselung, digitale Signaturen und sichere Systemadministration.

## 1    GPG (GnuPG)

GnuPG (GNU Privacy Guard) ist die freie Implementierung des OpenPGP-Standards für asymmetrische Verschlüsselung und digitale Signaturen.

### 1.1    Installation und Schlüsselerstellung

**Installation:**

```zsh
brew install gnupg

# Version prüfen
gpg --version
```

**Optionale GUI-Tools:**

```zsh
# GPG Suite (macOS GUI)
brew install --cask gpg-suite

# Oder nur Pinentry für Passphrase-Eingabe
brew install pinentry-mac
```

**Schlüsselpaar generieren:**

```zsh
# Interaktiver Assistent
gpg --full-generate-key

# Ausgabe:
# Bitte wählen Sie, welche Art von Schlüssel Sie möchten:
#    (1) RSA und RSA
#    (2) DSA und Elgamal
#    (3) DSA (nur signieren)
#    (4) RSA (nur signieren)
#    (9) ECC (signieren und verschlüsseln) *Standard*
#   (10) ECC (nur signieren)
# Ihre Auswahl? 9
```

**Empfohlene Einstellungen:**

| Einstellung | Empfehlung |
| ----------- | ---------- |
| Algorithmus | ECC (Curve 25519) oder RSA 4096 |
| Gültigkeit | 1-2 Jahre (kann verlängert werden) |
| Name | Vollständiger Name |
| E-Mail | Haupt-E-Mail-Adresse |
| Passphrase | Stark und einzigartig |

**Schnelle Schlüsselerstellung:**

```zsh
# Schnell mit Defaults
gpg --quick-generate-key "Max Mustermann <max@example.com>"

# Mit spezifischen Optionen
gpg --quick-generate-key "Max Mustermann <max@example.com>" rsa4096 cert 2y
```

**Schlüssel nach Erstellung:**

```zsh
# Öffentliche Schlüssel anzeigen
gpg --list-keys
gpg -k

# Private Schlüssel anzeigen
gpg --list-secret-keys
gpg -K

# Ausgabe:
# pub   ed25519 2024-01-15 [SC] [expires: 2026-01-15]
#       ABCD1234EFGH5678IJKL9012MNOP3456QRST7890
# uid           [ultimate] Max Mustermann <max@example.com>
# sub   cv25519 2024-01-15 [E] [expires: 2026-01-15]
```

**Schlüssel-IDs verstehen:**

| Länge | Beispiel | Verwendung |
| ----- | -------- | ---------- |
| Fingerprint (40) | `ABCD1234...7890` | Vollständige Identifikation |
| Long ID (16) | `MNOP3456QRST7890` | Eindeutig genug |
| Short ID (8) | `QRST7890` | Veraltet, Kollisionsgefahr |
| E-Mail | `max@example.com` | Bequem, aber nicht eindeutig |

### 1.2    Dateien verschlüsseln und entschlüsseln

**Asymmetrisch verschlüsseln (für Empfänger):**

```zsh
# Für einen Empfänger
gpg --encrypt --recipient max@example.com datei.txt
# Ergebnis: datei.txt.gpg

# Kurzform
gpg -e -r max@example.com datei.txt

# Für mehrere Empfänger
gpg -e -r alice@example.com -r bob@example.com datei.txt

# Mit ASCII-Armor (Textformat)
gpg -e -a -r max@example.com datei.txt
# Ergebnis: datei.txt.asc

# Ausgabedatei angeben
gpg -e -r max@example.com -o geheim.gpg datei.txt
```

**Asymmetrisch entschlüsseln:**

```zsh
# Standard (fragt nach Passphrase)
gpg --decrypt datei.txt.gpg
# Ausgabe auf stdout

# In Datei speichern
gpg -d datei.txt.gpg > datei.txt
gpg -d -o datei.txt datei.txt.gpg

# Kurzform
gpg datei.txt.gpg
# Erstellt automatisch datei.txt
```

**Symmetrisch verschlüsseln (mit Passwort):**

```zsh
# Mit Passwort verschlüsseln
gpg --symmetric datei.txt
gpg -c datei.txt
# Fragt nach Passphrase, erstellt datei.txt.gpg

# Mit spezifischem Algorithmus
gpg -c --cipher-algo AES256 datei.txt

# Als ASCII
gpg -c -a datei.txt
```

**Symmetrisch entschlüsseln:**

```zsh
gpg -d datei.txt.gpg
gpg -d -o datei.txt datei.txt.gpg
```

**Kombiniert (symmetrisch + asymmetrisch):**

```zsh
# Sowohl mit Passwort als auch für Empfänger
gpg -c -e -r max@example.com datei.txt
```

**Verzeichnisse verschlüsseln:**

```zsh
# Erst tar, dann gpg
tar -czvf - ordner/ | gpg -c -o ordner.tar.gz.gpg

# Entschlüsseln und entpacken
gpg -d ordner.tar.gz.gpg | tar -xzvf -
```

### 1.3    Signaturen erstellen und prüfen

Digitale Signaturen beweisen Authentizität und Integrität.

**Datei signieren:**

```zsh
# Detached Signature (separate .sig Datei)
gpg --detach-sign datei.txt
# Ergebnis: datei.txt.sig

# ASCII-Format
gpg --detach-sign -a datei.txt
# Ergebnis: datei.txt.asc

# Klartext-Signatur (Signatur + Original)
gpg --clearsign datei.txt
# Ergebnis: datei.txt.asc (enthält Original + Signatur)

# Signatur eingebettet (binär)
gpg --sign datei.txt
# Ergebnis: datei.txt.gpg
```

**Signatur prüfen:**

```zsh
# Detached Signature prüfen
gpg --verify datei.txt.sig datei.txt

# Ausgabe bei gültiger Signatur:
# gpg: Signature made Mon Jan 15 10:30:00 2024 CET
# gpg:                using EDDSA key ABCD...7890
# gpg: Good signature from "Max Mustermann <max@example.com>"

# Clearsign oder eingebettete Signatur
gpg --verify datei.txt.asc

# Signierte Datei extrahieren und prüfen
gpg --decrypt datei.txt.gpg
```

**Signieren und Verschlüsseln:**

```zsh
# Verschlüsseln + Signieren
gpg -e -s -r empfaenger@example.com datei.txt

# Entschlüsseln prüft automatisch Signatur
gpg -d datei.txt.gpg
```

**Git-Commits signieren:**

```zsh
# GPG-Schlüssel für Git konfigurieren
git config --global user.signingkey ABCD1234EFGH5678

# Einzelnen Commit signieren
git commit -S -m "Signierter Commit"

# Alle Commits signieren
git config --global commit.gpgsign true

# Signaturen anzeigen
git log --show-signature
```

### 1.4    Schlüsselverwaltung

**Schlüssel exportieren:**

```zsh
# Öffentlichen Schlüssel exportieren (zum Teilen)
gpg --export -a max@example.com > publickey.asc
gpg --export --armor ABCD1234 > publickey.asc

# Privaten Schlüssel exportieren (Backup!)
gpg --export-secret-keys -a max@example.com > privatekey.asc

# Alle Schlüssel exportieren
gpg --export -a > all-public-keys.asc
gpg --export-secret-keys -a > all-private-keys.asc
```

**Schlüssel importieren:**

```zsh
# Öffentlichen Schlüssel importieren
gpg --import publickey.asc

# Privaten Schlüssel importieren
gpg --import privatekey.asc

# Von Keyserver
gpg --keyserver hkps://keys.openpgp.org --recv-keys ABCD1234EFGH5678
```

**Schlüssel auf Keyserver hochladen:**

```zsh
gpg --keyserver hkps://keys.openpgp.org --send-keys ABCD1234EFGH5678
```

**Schlüssel bearbeiten:**

```zsh
# Interaktiver Editor
gpg --edit-key max@example.com

# Verfügbare Befehle:
# uid       - UID auswählen
# trust     - Vertrauensstufe setzen
# expire    - Ablaufdatum ändern
# passwd    - Passphrase ändern
# adduid    - E-Mail hinzufügen
# deluid    - E-Mail entfernen
# save      - Speichern und beenden
# quit      - Ohne Speichern beenden
```

**Vertrauensstufen:**

| Stufe | Bedeutung |
| ----- | --------- |
| Unknown | Nicht bewertet |
| Never | Nicht vertrauenswürdig |
| Marginal | Teilweise vertrauenswürdig |
| Full | Voll vertrauenswürdig |
| Ultimate | Eigener Schlüssel |

```zsh
# Vertrauen setzen
gpg --edit-key alice@example.com
gpg> trust
# Stufe wählen
gpg> save
```

**Schlüssel widerrufen:**

```zsh
# Widerrufszertifikat erstellen (bei Erstellung!)
gpg --gen-revoke ABCD1234 > revoke.asc

# Schlüssel widerrufen (wenn kompromittiert)
gpg --import revoke.asc
gpg --keyserver hkps://keys.openpgp.org --send-keys ABCD1234
```

**Schlüssel löschen:**

```zsh
# Öffentlichen Schlüssel löschen
gpg --delete-keys ABCD1234

# Privaten Schlüssel löschen
gpg --delete-secret-keys ABCD1234

# Beides
gpg --delete-secret-and-public-keys ABCD1234
```

**GPG-Agent konfigurieren:**

```zsh
# ~/.gnupg/gpg-agent.conf
default-cache-ttl 3600
max-cache-ttl 86400
pinentry-program /opt/homebrew/bin/pinentry-mac
```

```zsh
# Agent neu starten
gpgconf --kill gpg-agent
gpg-agent --daemon
```

## 2    OpenSSL

OpenSSL ist das Schweizer Taschenmesser für Kryptographie: Zertifikate, Verschlüsselung, Hashes und mehr.

### 2.1    Zertifikate anzeigen und prüfen

**Zertifikat einer Website anzeigen:**

```zsh
# Zertifikat abrufen und anzeigen
openssl s_client -connect example.com:443 -servername example.com </dev/null 2>/dev/null | \
    openssl x509 -noout -text

# Nur wichtige Infos
openssl s_client -connect example.com:443 -servername example.com </dev/null 2>/dev/null | \
    openssl x509 -noout -subject -issuer -dates

# Ausgabe:
# subject=CN = example.com
# issuer=C = US, O = DigiCert Inc, CN = DigiCert TLS RSA SHA256 2020 CA1
# notBefore=Jan 15 00:00:00 2024 GMT
# notAfter=Jan 15 23:59:59 2025 GMT
```

**Lokales Zertifikat anzeigen:**

```zsh
# PEM-Datei
openssl x509 -in certificate.pem -noout -text

# DER-Format
openssl x509 -in certificate.der -inform DER -noout -text

# Nur bestimmte Felder
openssl x509 -in cert.pem -noout -subject
openssl x509 -in cert.pem -noout -issuer
openssl x509 -in cert.pem -noout -dates
openssl x509 -in cert.pem -noout -fingerprint
openssl x509 -in cert.pem -noout -serial
```

**Zertifikatskette prüfen:**

```zsh
# Kette verifizieren
openssl verify -CAfile ca-bundle.crt certificate.pem

# Mit Zwischenzertifikaten
openssl verify -CAfile root.crt -untrusted intermediate.crt server.crt
```

**TLS-Verbindung testen:**

```zsh
# Verbindung und Handshake testen
openssl s_client -connect example.com:443

# Mit bestimmtem Protokoll
openssl s_client -connect example.com:443 -tls1_2
openssl s_client -connect example.com:443 -tls1_3

# Zertifikatskette anzeigen
openssl s_client -connect example.com:443 -showcerts

# SNI (Server Name Indication)
openssl s_client -connect server.com:443 -servername www.example.com
```

**Ablaufdatum prüfen:**

```zsh
# Website-Zertifikat
echo | openssl s_client -connect example.com:443 -servername example.com 2>/dev/null | \
    openssl x509 -noout -enddate
# notAfter=Jan 15 23:59:59 2025 GMT

# Tage bis Ablauf
expiry=$(echo | openssl s_client -connect example.com:443 2>/dev/null | \
    openssl x509 -noout -enddate | cut -d= -f2)
echo $(( ($(date -jf "%b %d %H:%M:%S %Y %Z" "$expiry" +%s) - $(date +%s)) / 86400 )) Tage
```

**Selbstsigniertes Zertifikat erstellen:**

```zsh
# Schlüssel und Zertifikat in einem Schritt
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes

# Mit spezifischen Angaben
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes \
    -subj "/CN=localhost/O=Development/C=DE"
```

### 2.2    Dateien verschlüsseln (symmetrisch)

**Datei verschlüsseln:**

```zsh
# AES-256-CBC (empfohlen)
openssl enc -aes-256-cbc -salt -pbkdf2 -in datei.txt -out datei.enc

# Mit explizitem Passwort (unsicher für Skripte!)
openssl enc -aes-256-cbc -salt -pbkdf2 -in datei.txt -out datei.enc -pass pass:geheim

# Passwort aus Datei
openssl enc -aes-256-cbc -salt -pbkdf2 -in datei.txt -out datei.enc -pass file:password.txt

# Base64-kodiert (für E-Mail etc.)
openssl enc -aes-256-cbc -salt -pbkdf2 -a -in datei.txt -out datei.enc
```

**Datei entschlüsseln:**

```zsh
# Standard
openssl enc -aes-256-cbc -d -pbkdf2 -in datei.enc -out datei.txt

# Base64-kodiert
openssl enc -aes-256-cbc -d -pbkdf2 -a -in datei.enc -out datei.txt
```

**Verfügbare Algorithmen:**

```zsh
# Alle Cipher auflisten
openssl enc -list

# Empfohlene Algorithmen:
# aes-256-cbc    - AES 256-bit, CBC-Modus
# aes-256-gcm    - AES 256-bit, GCM-Modus (authentifiziert)
# chacha20       - ChaCha20 Stream-Cipher
```

**Wichtige Optionen:**

| Option | Beschreibung |
| ------ | ------------ |
| `-e` | Encrypt (Standard) |
| `-d` | Decrypt |
| `-salt` | Salt verwenden (Standard, wichtig!) |
| `-pbkdf2` | Moderne Key-Derivation (empfohlen) |
| `-iter N` | PBKDF2-Iterationen |
| `-a` | Base64-Encoding |
| `-in FILE` | Eingabedatei |
| `-out FILE` | Ausgabedatei |
| `-pass` | Passwort-Quelle |

### 2.3    Hashes und Checksummen

**Einzelne Datei hashen:**

```zsh
# SHA-256 (empfohlen)
openssl sha256 datei.txt
# SHA256(datei.txt)= abc123...

# Nur Hash (ohne Dateiname)
openssl sha256 -r datei.txt | cut -d' ' -f1

# Verschiedene Algorithmen
openssl md5 datei.txt        # veraltet
openssl sha1 datei.txt       # veraltet
openssl sha256 datei.txt     # empfohlen
openssl sha384 datei.txt
openssl sha512 datei.txt
```

**Alternative: Standard-Tools:**

```zsh
# macOS eingebaute Tools
shasum -a 256 datei.txt
md5 datei.txt

# SHA-256
shasum -a 256 datei.txt
# abc123...  datei.txt

# SHA-512
shasum -a 512 datei.txt
```

**Checksumme prüfen:**

```zsh
# Checksummen-Datei erstellen
shasum -a 256 *.zip > checksums.sha256

# Prüfen
shasum -a 256 -c checksums.sha256
# file1.zip: OK
# file2.zip: OK
```

**HMAC (Hash mit Schlüssel):**

```zsh
# HMAC-SHA256
openssl dgst -sha256 -hmac "geheimer-schluessel" datei.txt

# Aus Datei
openssl dgst -sha256 -hmac "$(cat key.txt)" datei.txt
```

**String hashen:**

```zsh
# String statt Datei
echo -n "zu hashender text" | openssl sha256
echo -n "zu hashender text" | shasum -a 256

# Wichtig: -n verhindert Newline am Ende!
```

**Passwort-Hash erstellen:**

```zsh
# Für /etc/shadow-ähnliche Hashes
openssl passwd -6 "meinpasswort"    # SHA-512
openssl passwd -5 "meinpasswort"    # SHA-256
openssl passwd -1 "meinpasswort"    # MD5 (veraltet)

# Mit Salt
openssl passwd -6 -salt "randomsalt" "meinpasswort"
```

### 2.4    Zufallsdaten generieren

**Zufällige Bytes:**

```zsh
# 32 Bytes als Hex
openssl rand -hex 32
# a1b2c3d4e5f6...

# 32 Bytes als Base64
openssl rand -base64 32
# QUJDREVGR0hJSktMTU5PUFE=

# In Datei
openssl rand -out random.bin 256

# Für Passwörter (URL-safe)
openssl rand -base64 24 | tr -dc 'a-zA-Z0-9' | head -c 32
```

**Sichere Passwörter generieren:**

```zsh
# Zufälliges Passwort
openssl rand -base64 18
# kQ7xY9mN2pL4vR6t8wZa

# Alphanumerisch
openssl rand -base64 32 | tr -dc 'a-zA-Z0-9' | head -c 16

# Mit Sonderzeichen
openssl rand -base64 32 | head -c 20
```

**Alternative: macOS `security`:**

```zsh
# Zufälliges Passwort mit Keychain-Tool
security random-string 20

# UUID generieren
uuidgen
```

**Für Kryptographie:**

```zsh
# AES-256 Schlüssel (32 Bytes)
openssl rand -hex 32

# IV für AES (16 Bytes)
openssl rand -hex 16

# Salt
openssl rand -hex 8
```

## 3    Sichere Administration

### 3.1    `sudoedit` – Dateien sicher bearbeiten

`sudoedit` (oder `sudo -e`) bearbeitet Dateien sicher, ohne den Editor als root zu starten.

**Problem mit `sudo vim`:**

```zsh
# UNSICHER: vim läuft als root
sudo vim /etc/hosts
# vim-Plugins, Shell-Escapes etc. laufen alle als root!
```

**Lösung mit `sudoedit`:**

```zsh
# SICHER: Editor läuft als normaler User
sudoedit /etc/hosts
# oder
sudo -e /etc/hosts
```

**Wie es funktioniert:**

1. Erstellt temporäre Kopie der Datei
2. Öffnet Editor als normaler Benutzer
3. Kopiert Änderungen nach Schließen zurück (als root)

**Editor konfigurieren:**

```zsh
# In ~/.zshrc
export SUDO_EDITOR="nano"
# oder
export SUDO_EDITOR="vim"
export SUDO_EDITOR="code --wait"

# Alternativ
export VISUAL="nano"
export EDITOR="nano"
```

**Vorteile:**

| sudo vim | sudoedit |
| -------- | -------- |
| Editor als root | Editor als User |
| Plugins als root | Plugins als User |
| Shell-Escape = root | Shell-Escape = User |
| Unsicher | Sicher |

### 3.2    `/etc/sudoers` und `visudo`

Die Datei `/etc/sudoers` kontrolliert sudo-Berechtigungen. Sie darf **nur** mit `visudo` bearbeitet werden.

**visudo verwenden:**

```zsh
# Konfiguration bearbeiten (prüft Syntax!)
sudo visudo

# Mit spezifischem Editor
sudo EDITOR=nano visudo

# Syntax einer Datei prüfen
sudo visudo -c
sudo visudo -cf /etc/sudoers.d/custom
```

**Warum visudo?**

- Prüft Syntax vor dem Speichern
- Verhindert gleichzeitige Bearbeitung
- Fehlerhafte Syntax = kein sudo mehr möglich!

**sudoers-Syntax:**

```
# Grundformat
WER  WO = (ALS_WER)  WAS

# Beispiele
root    ALL=(ALL:ALL) ALL
%admin  ALL=(ALL) ALL
max     ALL=(ALL) NOPASSWD: ALL
```

**Felder erklärt:**

| Feld | Bedeutung | Beispiel |
| ---- | --------- | -------- |
| WER | Benutzer oder %gruppe | `max`, `%admin` |
| WO | Hosts | `ALL`, `localhost` |
| ALS_WER | Zielbenutzer:gruppe | `(ALL)`, `(root)` |
| WAS | Erlaubte Befehle | `ALL`, `/usr/bin/apt` |

**Praktische Beispiele:**

```sudoers
# Benutzer max darf alles ohne Passwort
max ALL=(ALL) NOPASSWD: ALL

# Gruppe developers darf Docker ohne Passwort
%developers ALL=(ALL) NOPASSWD: /usr/bin/docker, /usr/bin/docker-compose

# Benutzer backup darf nur rsync als root
backup ALL=(root) NOPASSWD: /usr/bin/rsync

# Benutzer webadmin darf Webserver neustarten
webadmin ALL=(root) NOPASSWD: /usr/bin/systemctl restart nginx, /usr/bin/systemctl restart apache2

# Umgebungsvariablen erhalten
Defaults env_keep += "HOME"
Defaults env_keep += "SSH_AUTH_SOCK"
```

**Include-Dateien:**

```zsh
# Separate Dateien in /etc/sudoers.d/
sudo visudo -f /etc/sudoers.d/developers

# In /etc/sudoers aktiviert durch:
@includedir /etc/sudoers.d
# oder (ältere Syntax)
#includedir /etc/sudoers.d
```

**Aliase definieren:**

```sudoers
# Benutzer-Aliase
User_Alias ADMINS = alice, bob, carol
User_Alias DEVELOPERS = dev1, dev2, dev3

# Befehls-Aliase
Cmnd_Alias SERVICES = /usr/bin/systemctl restart *, /usr/bin/systemctl status *
Cmnd_Alias DOCKER = /usr/bin/docker, /usr/bin/docker-compose
Cmnd_Alias NETWORKING = /sbin/ifconfig, /sbin/route, /usr/bin/ping

# Verwendung
ADMINS ALL=(ALL) ALL
DEVELOPERS ALL=(root) NOPASSWD: DOCKER, SERVICES
```

### 3.3    Einschränkungen mit `sudo`

**Befehle einschränken:**

```sudoers
# Nur bestimmte Befehle
max ALL=(root) /usr/bin/apt update, /usr/bin/apt upgrade

# Mit Wildcards
max ALL=(root) /usr/bin/systemctl restart nginx*

# Ohne Argumente
max ALL=(root) /usr/bin/reboot ""

# Bestimmte Argumente verbieten
max ALL=(root) /usr/bin/rm, !/usr/bin/rm -rf /
```

**NOPASSWD vs PASSWD:**

```sudoers
# Ohne Passwort
max ALL=(ALL) NOPASSWD: /usr/bin/docker

# Mit Passwort (Standard)
max ALL=(ALL) PASSWD: /usr/bin/visudo

# Gemischt
max ALL=(ALL) PASSWD: ALL, NOPASSWD: /usr/bin/docker
```

**Verbote (!):**

```sudoers
# Alles außer bestimmte Befehle
max ALL=(ALL) ALL, !/bin/su, !/bin/bash, !/usr/bin/passwd root

# Shell-Escape verhindern
max ALL=(ALL) NOEXEC: /usr/bin/vim
```

**NOEXEC – Shell-Escapes verhindern:**

```sudoers
# Verhindert dass vim Shell startet
max ALL=(root) NOEXEC: /usr/bin/vim
```

**Logging:**

```sudoers
# Alle Befehle loggen
Defaults log_input, log_output
Defaults logfile="/var/log/sudo.log"

# I/O-Logging
Defaults iolog_dir="/var/log/sudo-io"
```

**Timeout-Einstellungen:**

```sudoers
# Passwort-Timeout (Minuten)
Defaults timestamp_timeout=15

# Sofort Passwort verlangen
Defaults timestamp_timeout=0

# Nie wieder fragen (Session)
Defaults timestamp_timeout=-1

# Anzahl Passwort-Versuche
Defaults passwd_tries=3
```

**Sicherheits-Empfehlungen:**

```sudoers
# Sichere PATH-Einstellung
Defaults secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"

# Root-Passwort bei Authentifizierung verlangen
Defaults rootpw

# TTY erforderlich (verhindert Skript-Missbrauch)
Defaults requiretty

# Insults bei falschem Passwort (Spaß)
Defaults insults
```

**Sudo-Berechtigungen prüfen:**

```zsh
# Eigene Berechtigungen anzeigen
sudo -l

# Ausgabe:
# User max may run the following commands on hostname:
#     (ALL) ALL
#     (root) NOPASSWD: /usr/bin/docker

# Für anderen Benutzer
sudo -l -U alice
```

**Sudo-Session beenden:**

```zsh
# Timestamp zurücksetzen (Passwort wird wieder verlangt)
sudo -k

# Timestamp komplett löschen
sudo -K
```

**Best Practices:**

1. **Minimal notwendige Rechte**: Nur spezifische Befehle erlauben
2. **Keine Shell-Befehle**: `/bin/bash`, `/bin/su` vermeiden
3. **NOEXEC nutzen**: Bei Editoren und Programmen mit Shell-Escape
4. **Separate Dateien**: `/etc/sudoers.d/` für Organisation
5. **Logging aktivieren**: Für Audit-Trail
6. **Regelmäßig prüfen**: `sudo -l` und `visudo -c`

**Cheatsheet sudoers:**

```sudoers
# Format
USER HOST=(RUNAS) COMMAND

# Beispiele
root        ALL=(ALL:ALL) ALL           # root darf alles
%admin      ALL=(ALL) ALL               # Gruppe admin darf alles
max         ALL=(ALL) NOPASSWD: ALL     # max ohne Passwort
deploy      ALL=(root) /usr/bin/docker  # deploy nur docker
backup      ALL=(root) NOPASSWD: /usr/bin/rsync  # backup rsync ohne PW

# Verbote
max         ALL=(ALL) ALL, !/bin/su     # alles außer su

# Aliase
User_Alias DEVS = alice, bob
Cmnd_Alias DOCKER = /usr/bin/docker
DEVS        ALL=(root) NOPASSWD: DOCKER

# Sicherheit
Defaults    requiretty
Defaults    timestamp_timeout=5
Defaults    secure_path="..."
```
