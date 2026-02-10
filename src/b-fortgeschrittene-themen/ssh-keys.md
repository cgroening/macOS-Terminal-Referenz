# SSH-Keys

SSH-Keys ermöglichen sichere, passwortlose Authentifizierung bei Remote-Servern. Sie sind sicherer als Passwörter und essentiell für automatisierte Workflows.

## 1    SSH-Keys erstellen & verwalten

### 1.1    Funktionsweise

SSH verwendet asymmetrische Kryptografie:

| Schlüssel | Datei | Verbleib | Funktion |
| --------- | ----- | -------- | -------- |
| **Private Key** | `~/.ssh/id_ed25519` | Lokal (geheim!) | Zum Signieren |
| **Public Key** | `~/.ssh/id_ed25519.pub` | Server | Zum Verifizieren |

Der Private Key verlässt niemals den lokalen Rechner. Der Public Key wird auf alle Server kopiert, zu denen man sich verbinden möchte.

### 1.2    Algorithmen

| Algorithmus | Empfehlung | Anmerkung |
| ----------- | ---------- | --------- |
| **Ed25519** | ✅ Empfohlen | Modern, schnell, sicher, kurze Keys |
| **RSA** | ⚠️ Akzeptabel | Mindestens 4096 Bit, für ältere Systeme |
| **ECDSA** | ❌ Vermeiden | Potenzielle Schwächen |
| **DSA** | ❌ Veraltet | Nicht mehr verwenden |

### 1.3    Key erstellen

**Ed25519 (empfohlen):**

```zsh
ssh-keygen -t ed25519 -C "kommentar@example.com"
```

**RSA (für Kompatibilität):**

```zsh
ssh-keygen -t rsa -b 4096 -C "kommentar@example.com"
```

**Interaktiver Dialog:**

```
Generating public/private ed25519 key pair.
Enter file in which to save the key (/Users/max/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/max/.ssh/id_ed25519
Your public key has been saved in /Users/max/.ssh/id_ed25519.pub
```

**Optionen:**

| Option | Beschreibung |
| ------ | ------------ |
| `-t TYPE` | Algorithmus (ed25519, rsa, ecdsa) |
| `-b BITS` | Schlüssellänge (nur RSA: 4096) |
| `-C "text"` | Kommentar (meist E-Mail) |
| `-f DATEI` | Ausgabedatei |
| `-N "phrase"` | Passphrase (leer = keine) |

**Beispiele:**

```zsh
# Standard Ed25519 Key
ssh-keygen -t ed25519 -C "max@example.com"

# Key mit bestimmtem Namen
ssh-keygen -t ed25519 -C "github" -f ~/.ssh/github_ed25519

# Key ohne Passphrase (für Automatisierung)
ssh-keygen -t ed25519 -C "automation" -f ~/.ssh/automation -N ""

# RSA Key für ältere Systeme
ssh-keygen -t rsa -b 4096 -C "legacy-server" -f ~/.ssh/legacy_rsa
```

### 1.4    Passphrase

Die Passphrase verschlüsselt den Private Key:

| Mit Passphrase | Ohne Passphrase |
| -------------- | --------------- |
| Key ist verschlüsselt | Key ist unverschlüsselt |
| Bei Diebstahl geschützt | Sofort nutzbar bei Diebstahl |
| Muss bei Verwendung eingegeben werden | Keine Eingabe nötig |
| SSH-Agent speichert temporär | Für Automatisierung geeignet |

**Empfehlung:** Immer Passphrase verwenden, SSH-Agent für Komfort nutzen.

**Passphrase ändern:**

```zsh
ssh-keygen -p -f ~/.ssh/id_ed25519
```

### 1.5    Dateien und Berechtigungen

**Standard-Speicherort:** `~/.ssh/`

| Datei | Beschreibung | Berechtigung |
| ----- | ------------ | ------------ |
| `id_ed25519` | Private Key | `600` (nur Besitzer) |
| `id_ed25519.pub` | Public Key | `644` (lesbar) |
| `authorized_keys` | Erlaubte Keys | `600` |
| `known_hosts` | Bekannte Server | `644` |
| `config` | SSH-Konfiguration | `600` |

**Berechtigungen setzen:**

```zsh
# Verzeichnis
chmod 700 ~/.ssh

# Private Keys
chmod 600 ~/.ssh/id_*
chmod 600 ~/.ssh/*_ed25519
chmod 600 ~/.ssh/*_rsa

# Public Keys
chmod 644 ~/.ssh/*.pub

# Konfigurationsdateien
chmod 600 ~/.ssh/config
chmod 600 ~/.ssh/authorized_keys
chmod 644 ~/.ssh/known_hosts
```

SSH verweigert Keys mit zu offenen Berechtigungen!

### 1.6    Public Key auf Server kopieren

**Methode 1: ssh-copy-id (empfohlen)**

```zsh
ssh-copy-id user@server

# Bestimmten Key
ssh-copy-id -i ~/.ssh/github_ed25519.pub user@server
```

**Methode 2: Manuell**

```zsh
# Public Key anzeigen
cat ~/.ssh/id_ed25519.pub

# Auf Server: Key in authorized_keys einfügen
ssh user@server
mkdir -p ~/.ssh
chmod 700 ~/.ssh
echo "PUBLICKEY" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

**Methode 3: Einzeiler**

```zsh
cat ~/.ssh/id_ed25519.pub | ssh user@server "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```

### 1.7    Keys verwalten

**Alle Keys auflisten:**

```zsh
ls -la ~/.ssh/

# Fingerprints anzeigen
for key in ~/.ssh/id_*; do
    [[ -f "$key" && "$key" != *.pub ]] && ssh-keygen -lf "$key"
done
```

**Key-Fingerprint anzeigen:**

```zsh
# MD5 (älteres Format)
ssh-keygen -l -E md5 -f ~/.ssh/id_ed25519.pub

# SHA256 (Standard)
ssh-keygen -l -f ~/.ssh/id_ed25519.pub
```

**Public Key aus Private Key extrahieren:**

```zsh
ssh-keygen -y -f ~/.ssh/id_ed25519 > ~/.ssh/id_ed25519.pub
```

**Key löschen:**

```zsh
# Beide Dateien entfernen
rm ~/.ssh/id_ed25519 ~/.ssh/id_ed25519.pub

# Aus authorized_keys auf Server entfernen
ssh user@server
nano ~/.ssh/authorized_keys  # Zeile entfernen
```

### 1.8    Mehrere Keys

Für verschiedene Zwecke separate Keys erstellen:

```zsh
# Persönlicher Key
ssh-keygen -t ed25519 -C "personal" -f ~/.ssh/personal_ed25519

# Arbeit
ssh-keygen -t ed25519 -C "work" -f ~/.ssh/work_ed25519

# GitHub
ssh-keygen -t ed25519 -C "github" -f ~/.ssh/github_ed25519

# Automation (ohne Passphrase)
ssh-keygen -t ed25519 -C "automation" -f ~/.ssh/automation_ed25519 -N ""
```

Die Zuordnung erfolgt über `~/.ssh/config` (siehe Abschnitt 5.2).

## 2    SSH-Konfiguration (~/.ssh/config)

Die SSH-Konfiguration vereinfacht Verbindungen und ermöglicht hostspezifische Einstellungen.

### 2.1    Grundstruktur

```
Host ALIAS
    Option Wert
    Option Wert
```

**Einfaches Beispiel:**

```
Host server
    HostName 192.168.1.100
    User admin
    Port 22
```

Statt `ssh admin@192.168.1.100` genügt nun `ssh server`.

### 2.2    Wichtige Optionen

| Option | Beschreibung | Beispiel |
| ------ | ------------ | -------- |
| `Host` | Alias oder Pattern | `server`, `*.example.com` |
| `HostName` | Echter Hostname/IP | `192.168.1.100` |
| `User` | Benutzername | `admin` |
| `Port` | SSH-Port | `22`, `2222` |
| `IdentityFile` | Private Key | `~/.ssh/work_ed25519` |
| `IdentitiesOnly` | Nur angegebene Keys | `yes` |
| `ForwardAgent` | Agent-Forwarding | `yes` |
| `ProxyJump` | Jump-Host | `bastion` |
| `LocalForward` | Port-Forwarding lokal | `8080 localhost:80` |
| `RemoteForward` | Port-Forwarding remote | `9090 localhost:8080` |
| `ServerAliveInterval` | Keep-alive Intervall | `60` |
| `ServerAliveCountMax` | Keep-alive Versuche | `3` |
| `Compression` | Komprimierung | `yes` |
| `AddKeysToAgent` | Key zum Agent | `yes` |
| `UseKeychain` | macOS Keychain | `yes` |

### 2.3    Vollständige Beispielkonfiguration

```
# ~/.ssh/config

# === Globale Einstellungen ===
Host *
    AddKeysToAgent yes
    UseKeychain yes
    IdentitiesOnly yes
    ServerAliveInterval 60
    ServerAliveCountMax 3

# === Arbeit ===
Host work
    HostName work-server.company.com
    User max.mustermann
    IdentityFile ~/.ssh/work_ed25519

Host work-*
    User max.mustermann
    IdentityFile ~/.ssh/work_ed25519

Host work-web
    HostName 10.0.1.10

Host work-db
    HostName 10.0.1.20

Host work-app
    HostName 10.0.1.30

# === Persönlich ===
Host home
    HostName home.dyndns.org
    User max
    Port 2222
    IdentityFile ~/.ssh/personal_ed25519

Host pi
    HostName 192.168.1.50
    User pi
    IdentityFile ~/.ssh/personal_ed25519

# === GitHub ===
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/github_ed25519

# Zweiter GitHub Account
Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/work_github_ed25519

# === GitLab ===
Host gitlab.com
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/gitlab_ed25519

# === Jump Host / Bastion ===
Host bastion
    HostName bastion.company.com
    User jump
    IdentityFile ~/.ssh/work_ed25519

Host internal-*
    ProxyJump bastion
    User admin
    IdentityFile ~/.ssh/work_ed25519

Host internal-web
    HostName 10.10.1.10

Host internal-db
    HostName 10.10.1.20

# === Port Forwarding ===
Host tunnel-db
    HostName dbserver.company.com
    User admin
    LocalForward 5432 localhost:5432
    IdentityFile ~/.ssh/work_ed25519

# === Alte/Legacy Server ===
Host legacy
    HostName old-server.example.com
    User root
    IdentityFile ~/.ssh/legacy_rsa
    PubkeyAcceptedAlgorithms +ssh-rsa
    HostkeyAlgorithms +ssh-rsa
```

### 2.4    Wildcards und Patterns

| Pattern | Beschreibung |
| ------- | ------------ |
| `*` | Beliebige Zeichen |
| `?` | Ein beliebiges Zeichen |
| `!` | Negation |

```
# Alle Hosts
Host *
    ServerAliveInterval 60

# Alle .company.com Hosts
Host *.company.com
    User admin
    IdentityFile ~/.ssh/work_ed25519

# Alle außer bestimmte
Host * !github.com !gitlab.com
    IdentityFile ~/.ssh/default_ed25519
```

### 2.5    Jump Hosts (ProxyJump)

Verbindung über einen Zwischenserver (Bastion Host):

```
# Klassisch (alt)
Host internal
    ProxyCommand ssh -W %h:%p bastion

# Modern (empfohlen)
Host internal
    ProxyJump bastion
```

**Mehrere Hops:**

```
Host deep-internal
    ProxyJump bastion,middle-server
```

**Kommandozeile:**

```zsh
ssh -J bastion internal-server
ssh -J bastion,middle deep-server
```

### 2.6    Port Forwarding

**Local Forward (Zugriff auf Remote-Dienst):**

```
# Remote-Datenbank lokal verfügbar machen
Host tunnel-postgres
    HostName dbserver.com
    LocalForward 5432 localhost:5432
```

```zsh
ssh tunnel-postgres
# Dann: psql -h localhost -p 5432
```

**Remote Forward (Lokalen Dienst remote verfügbar):**

```
# Lokalen Webserver remote verfügbar machen
Host expose-local
    HostName server.com
    RemoteForward 8080 localhost:3000
```

**Dynamic Forward (SOCKS Proxy):**

```
Host socks-proxy
    HostName server.com
    DynamicForward 1080
```

```zsh
ssh socks-proxy
# Browser: SOCKS5 Proxy auf localhost:1080
```

### 2.7    Mehrere GitHub/GitLab Accounts

```
# Persönlicher GitHub Account
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/github_personal

# Arbeits-GitHub Account
Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/github_work
```

**Verwendung:**

```zsh
# Persönlich (Standard)
git clone git@github.com:user/repo.git

# Arbeit
git clone git@github-work:company/repo.git

# In bestehendem Repo Remote ändern
git remote set-url origin git@github-work:company/repo.git
```

### 2.8    Reihenfolge und Priorität

SSH liest die Config von oben nach unten. Der **erste Match** für jede Option gewinnt.

```
# RICHTIG: Spezifisch vor allgemein
Host work-server
    User admin

Host *
    User default

# FALSCH: Allgemein überschreibt spezifisch
Host *
    User default

Host work-server
    User admin  # Wird ignoriert!
```

**Empfohlene Struktur:**

1. Spezifische Hosts
2. Gruppen-Patterns (`work-*`, `*.company.com`)
3. Globale Einstellungen (`Host *`)

## 3    SSH-Agent, Konfiguration und Sicherheit

### 3.1    SSH-Agent Grundlagen

Der SSH-Agent speichert entschlüsselte Private Keys im Speicher, sodass die Passphrase nicht bei jeder Verbindung eingegeben werden muss.

**Funktionsweise:**

```
┌─────────────────────────────────────────────────────┐
│                    SSH-Agent                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  │
│  │ Key 1       │  │ Key 2       │  │ Key 3       │  │
│  │ (entschl.)  │  │ (entschl.)  │  │ (entschl.)  │  │
│  └─────────────┘  └─────────────┘  └─────────────┘  │
└─────────────────────────────────────────────────────┘
        ↑                   ↑                ↑
   ssh server1         ssh server2      git push
   (kein Passwort)     (kein Passwort)  (kein Passwort)
```

### 3.2    Agent unter macOS

macOS startet den SSH-Agent automatisch. Die Integration mit der Keychain speichert Passphrasen dauerhaft.

**Keychain-Integration aktivieren:**

```
# ~/.ssh/config
Host *
    AddKeysToAgent yes
    UseKeychain yes
```

**Key manuell zur Keychain hinzufügen:**

```zsh
# Key zum Agent hinzufügen (Passphrase in Keychain speichern)
ssh-add --apple-use-keychain ~/.ssh/id_ed25519

# Alle Standard-Keys laden
ssh-add --apple-load-keychain
```

> [!WARNING]
> Ältere macOS-Versionen verwenden `-K` statt `--apple-use-keychain` und `-A` statt `--apple-load-keychain`.

### 3.3    Agent-Befehle

| Befehl | Beschreibung |
| ------ | ------------ |
| `ssh-add` | Standard-Keys hinzufügen |
| `ssh-add DATEI` | Bestimmten Key hinzufügen |
| `ssh-add -l` | Geladene Keys auflisten |
| `ssh-add -L` | Public Keys der geladenen Keys |
| `ssh-add -d DATEI` | Key entfernen |
| `ssh-add -D` | Alle Keys entfernen |
| `ssh-add -t SEKUNDEN` | Key mit Zeitlimit |

**Beispiele:**

```zsh
# Key hinzufügen
ssh-add ~/.ssh/work_ed25519

# Alle Keys anzeigen
ssh-add -l

# Key für 1 Stunde hinzufügen
ssh-add -t 3600 ~/.ssh/sensitive_ed25519

# Alle Keys entfernen (Sicherheit)
ssh-add -D
```

### 3.4    Agent Forwarding

Agent Forwarding erlaubt die Nutzung lokaler Keys auf Remote-Servern, ohne die Keys dorthin zu kopieren.

```
Lokal          →     Server A     →     Server B
(Agent mit Keys)     (kein Key)        (kein Key)
                     nutzt lokalen     nutzt lokalen
                     Agent             Agent via A
```

**Aktivieren:**

```
# ~/.ssh/config
Host trusted-server
    ForwardAgent yes
```

Oder per Kommandozeile:

```zsh
ssh -A trusted-server
```

> [!CAUTION]
> Agent Forwarding nur für vertrauenswürdige Server aktivieren! Ein kompromittierter Server könnte den Agent missbrauchen.

**Sicherer: ProxyJump statt Agent Forwarding**

```
Host internal
    ProxyJump bastion
```

ProxyJump ist sicherer, da der Zwischenserver keinen Zugriff auf den Agent erhält.

### 3.5    Sicherheits-Best-Practices

**Key-Management:**

| Praxis | Beschreibung |
| ------ | ------------ |
| Passphrase verwenden | Schützt bei Diebstahl des Keys |
| Separate Keys | Pro Zweck/Dienst eigene Keys |
| Ed25519 verwenden | Modernster, sicherster Algorithmus |
| Keys rotieren | Regelmäßig neue Keys erstellen |
| Alte Keys entfernen | Nicht mehr benötigte Keys löschen |

**Berechtigungen:**

```zsh
# Einmalig: Alle Berechtigungen korrekt setzen
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_* ~/.ssh/config ~/.ssh/authorized_keys
chmod 644 ~/.ssh/*.pub ~/.ssh/known_hosts
```

**authorized_keys absichern:**

```zsh
# Optionen vor dem Key einschränken
# In ~/.ssh/authorized_keys auf dem Server:

# Nur von bestimmter IP
from="192.168.1.100" ssh-ed25519 AAAA... user@host

# Kein Agent-Forwarding, kein PTY
no-agent-forwarding,no-pty ssh-ed25519 AAAA... automation@host

# Nur bestimmten Befehl erlauben
command="/usr/local/bin/backup.sh" ssh-ed25519 AAAA... backup@host

# Kombination
from="10.0.0.*",no-agent-forwarding,no-port-forwarding ssh-ed25519 AAAA...
```

**Verfügbare Optionen:**

| Option | Beschreibung |
| ------ | ------------ |
| `from="PATTERN"` | Nur von bestimmten IPs/Hosts |
| `command="CMD"` | Nur diesen Befehl ausführen |
| `no-agent-forwarding` | Agent-Forwarding verbieten |
| `no-port-forwarding` | Port-Forwarding verbieten |
| `no-pty` | Kein Terminal (nur Befehle) |
| `no-X11-forwarding` | X11-Forwarding verbieten |
| `environment="VAR=val"` | Umgebungsvariable setzen |

### 3.6    known_hosts

Die Datei `~/.ssh/known_hosts` speichert Fingerprints bekannter Server und schützt vor Man-in-the-Middle-Angriffen.

**Erste Verbindung:**

```
The authenticity of host 'server.com (192.168.1.100)' can't be established.
ED25519 key fingerprint is SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

**Fingerprint verifizieren:**

```zsh
# Auf dem Server
ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub

# Vergleichen mit angezeigtem Fingerprint
```

**Host entfernen (bei legitimem Key-Wechsel):**

```zsh
ssh-keygen -R server.com
ssh-keygen -R 192.168.1.100
```

**Hashed known_hosts (mehr Privatsphäre):**

```
# ~/.ssh/config
Host *
    HashKnownHosts yes
```

### 3.7    Server-Konfiguration (sshd_config)

Auf dem Server in `/etc/ssh/sshd_config`:

```bash
# Nur Key-Authentifizierung
PubkeyAuthentication yes
PasswordAuthentication no
PermitEmptyPasswords no

# Root-Login verbieten oder einschränken
PermitRootLogin no
# Oder: PermitRootLogin prohibit-password

# Nur bestimmte User erlauben
AllowUsers admin deploy

# Nur bestimmte Gruppen
AllowGroups sshusers

# Agent-Forwarding global deaktivieren
AllowAgentForwarding no

# Idle-Timeout
ClientAliveInterval 300
ClientAliveCountMax 2

# Sichere Algorithmen (modern)
KexAlgorithms curve25519-sha256,curve25519-sha256@libssh.org
HostKeyAlgorithms ssh-ed25519,rsa-sha2-512,rsa-sha2-256
Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com
MACs hmac-sha2-512-etm@openssh.com,hmac-sha2-256-etm@openssh.com
```

Nach Änderungen:

```zsh
# Syntax prüfen
sudo sshd -t

# Dienst neu starten
sudo systemctl restart sshd
# Oder macOS:
sudo launchctl stop com.openssh.sshd
sudo launchctl start com.openssh.sshd
```

### 3.8    Troubleshooting

**Verbindung debuggen:**

```zsh
# Verbose-Modus
ssh -v user@server      # Level 1
ssh -vv user@server     # Level 2
ssh -vvv user@server    # Level 3 (sehr detailliert)
```

**Häufige Probleme:**

| Problem | Ursache | Lösung |
| ------- | ------- | ------ |
| `Permission denied (publickey)` | Key nicht akzeptiert | Key in authorized_keys prüfen |
| `Bad permissions` | Falsche Dateirechte | `chmod 600 ~/.ssh/id_*` |
| `Host key verification failed` | Server-Key geändert | `ssh-keygen -R host` |
| `Connection refused` | SSH nicht aktiv / Port | Firewall, sshd-Status prüfen |
| `Too many authentication failures` | Zu viele Keys probiert | `IdentitiesOnly yes` |

**Agent prüfen:**

```zsh
# Agent läuft?
echo $SSH_AUTH_SOCK

# Keys geladen?
ssh-add -l

# Agent neu starten (falls nötig)
eval $(ssh-agent -s)
ssh-add
```

**Berechtigungen prüfen:**

```zsh
# Lokal
ls -la ~/.ssh/

# Auf Server
ls -la ~/.ssh/
ls -la ~/.ssh/authorized_keys
```

### 3.9    Nützliche Aliase und Funktionen

```bash
# ~/.zshrc

# SSH-Key Fingerprints anzeigen
alias ssh-fingerprints='for key in ~/.ssh/id_*; do [[ -f "$key" && "$key" != *.pub ]] && ssh-keygen -lf "$key"; done'

# Neuen Ed25519 Key erstellen
ssh-newkey() {
    local name="${1:-id_ed25519}"
    local comment="${2:-$(whoami)@$(hostname)}"
    ssh-keygen -t ed25519 -C "$comment" -f ~/.ssh/"$name"
}

# Key zu Server kopieren
ssh-copykey() {
    local key="${1:-~/.ssh/id_ed25519.pub}"
    local server="$2"
    if [[ -z "$server" ]]; then
        echo "Usage: ssh-copykey [keyfile] server"
        return 1
    fi
    ssh-copy-id -i "$key" "$server"
}

# SSH-Verbindung testen
ssh-test() {
    ssh -o BatchMode=yes -o ConnectTimeout=5 "$1" echo "OK" 2>&1
}
```

### 3.10    Cheatsheet

**Keys erstellen:**

```
ssh-keygen -t ed25519 -C "comment"           Neuer Ed25519 Key
ssh-keygen -t ed25519 -f ~/.ssh/name         Mit bestimmtem Namen
ssh-keygen -p -f ~/.ssh/id_ed25519           Passphrase ändern
ssh-keygen -y -f private > public            Public aus Private
ssh-keygen -lf ~/.ssh/id_ed25519             Fingerprint anzeigen
```

**Keys verteilen:**

```
ssh-copy-id user@server                      Key auf Server kopieren
ssh-copy-id -i ~/.ssh/key.pub user@server    Bestimmten Key kopieren
```

**Agent:**

```
ssh-add                                      Standard-Keys laden
ssh-add ~/.ssh/key                           Bestimmten Key laden
ssh-add -l                                   Geladene Keys anzeigen
ssh-add -d ~/.ssh/key                        Key entfernen
ssh-add -D                                   Alle Keys entfernen
ssh-add --apple-use-keychain ~/.ssh/key      In macOS Keychain (neu)
```

**Verbindung:**

```
ssh server                                   Mit Alias verbinden
ssh -i ~/.ssh/key user@server                Mit bestimmtem Key
ssh -J bastion internal                      Über Jump-Host
ssh -L 8080:localhost:80 server              Local Port Forward
ssh -v server                                Debug-Modus
```

**Wartung:**

```
ssh-keygen -R hostname                       Host aus known_hosts entfernen
chmod 700 ~/.ssh                             Verzeichnis-Berechtigung
chmod 600 ~/.ssh/id_* ~/.ssh/config          Datei-Berechtigungen
```
