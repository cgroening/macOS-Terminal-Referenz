# tmux & screen

Terminal-Multiplexer ermöglichen mehrere Terminal-Sitzungen in einem Fenster. Sie sind unverzichtbar für Remote-Arbeit, da Sessions auch nach Verbindungsabbruch weiterlaufen.

## 1    tmux Grundlagen

`tmux` (Terminal Multiplexer) ist der moderne Standard für Terminal-Multiplexing. Es ist leistungsfähiger und aktiver entwickelt als das ältere `screen`.

### 1.1    Installation

```zsh
# macOS (Homebrew)
brew install tmux

# Version prüfen
tmux -V
```

### 1.2    Konzepte

tmux organisiert sich in drei Ebenen:

```
Server
  └── Session (benannte Sitzung)
        └── Window (Tab/Fenster)
              └── Pane (geteilter Bereich)
```

| Begriff | Beschreibung |
| ------- | ------------ |
| **Server** | Hintergrundprozess, verwaltet alle Sessions |
| **Session** | Benannte Arbeitsumgebung mit mehreren Windows |
| **Window** | Einzelnes Terminal (wie ein Tab) |
| **Pane** | Geteilter Bereich innerhalb eines Windows |

### 1.3    Erste Schritte

```zsh
# Neue Session starten
tmux

# Neue benannte Session
tmux new -s arbeit

# Session verlassen (läuft weiter)
# Prefix + d  (Ctrl+b, dann d)

# Sessions auflisten
tmux ls

# Zu Session verbinden
tmux attach -t arbeit
tmux a -t arbeit          # Kurzform

# Letzte Session
tmux attach
tmux a
```

### 1.4    Prefix-Taste

Alle tmux-Befehle beginnen mit dem **Prefix** (Standard: `Ctrl+b`), gefolgt von einer Taste.

```
Ctrl+b, dann Befehlstaste
```

**Beispiel:** Um ein neues Fenster zu erstellen: `Ctrl+b`, loslassen, dann `c`.

**Notation in dieser Referenz:**

- `Prefix + x` bedeutet: Ctrl+b drücken, loslassen, dann x drücken
- `Prefix` kann in der Konfiguration geändert werden (oft zu `Ctrl+a`)

## 2    Fenster, Panes, Sessions

### 2.1    Session-Befehle

**Tastenkürzel (innerhalb tmux):**

| Kürzel | Beschreibung |
| ------ | ------------ |
| `Prefix + d` | Session detachen (verlassen) |
| `Prefix + s` | Session-Liste anzeigen |
| `Prefix + $` | Session umbenennen |
| `Prefix + (` | Vorherige Session |
| `Prefix + )` | Nächste Session |

**Kommandozeile:**

```zsh
# Neue Session
tmux new -s name

# Mit bestimmtem Startverzeichnis
tmux new -s projekt -c ~/projekte/webapp

# Session beenden
tmux kill-session -t name

# Alle Sessions beenden
tmux kill-server

# Session umbenennen
tmux rename-session -t alt neu
```

### 2.2    Window-Befehle (Fenster)

Windows sind wie Tabs innerhalb einer Session.

**Tastenkürzel:**

| Kürzel | Beschreibung |
| ------ | ------------ |
| `Prefix + c` | Neues Window erstellen |
| `Prefix + ,` | Window umbenennen |
| `Prefix + &` | Window schließen (mit Bestätigung) |
| `Prefix + n` | Nächstes Window |
| `Prefix + p` | Vorheriges Window |
| `Prefix + 0-9` | Zu Window 0–9 wechseln |
| `Prefix + w` | Window-Liste anzeigen |
| `Prefix + l` | Letztes Window |
| `Prefix + '` | Window nach Nummer auswählen |
| `Prefix + f` | Window nach Name suchen |

**Kommandozeile:**

```zsh
# Neues Window in bestehender Session
tmux new-window -t session:

# Window mit Name
tmux new-window -t session: -n "logs"

# Window schließen
tmux kill-window -t session:2
```

### 2.3    Pane-Befehle (Bereiche)

Panes teilen ein Window in mehrere Bereiche.

**Tastenkürzel:**

| Kürzel | Beschreibung |
| ------ | ------------ |
| `Prefix + %` | Vertikal teilen (links/rechts) |
| `Prefix + "` | Horizontal teilen (oben/unten) |
| `Prefix + x` | Pane schließen (mit Bestätigung) |
| `Prefix + o` | Zum nächsten Pane wechseln |
| `Prefix + ;` | Zum letzten Pane wechseln |
| `Prefix + Pfeiltasten` | Zu Pane in Richtung wechseln |
| `Prefix + q` | Pane-Nummern anzeigen |
| `Prefix + q + 0-9` | Zu Pane mit Nummer wechseln |
| `Prefix + z` | Pane zoomen (Vollbild toggle) |
| `Prefix + !` | Pane in eigenes Window verschieben |
| `Prefix + {` | Pane nach links/oben tauschen |
| `Prefix + }` | Pane nach rechts/unten tauschen |
| `Prefix + Space` | Zwischen Layouts wechseln |

**Pane-Größe ändern:**

| Kürzel | Beschreibung |
| ------ | ------------ |
| `Prefix + Ctrl+Pfeiltaste` | Größe in kleinen Schritten |
| `Prefix + Alt+Pfeiltaste` | Größe in großen Schritten |

Oder im Command-Mode (`Prefix + :`):

```
resize-pane -D 10    # 10 Zeilen nach unten
resize-pane -U 5     # 5 Zeilen nach oben
resize-pane -L 10    # 10 Spalten nach links
resize-pane -R 10    # 10 Spalten nach rechts
```

### 2.4    Layouts

tmux bietet vordefinierte Layouts:

| Layout | Beschreibung |
| ------ | ------------ |
| `even-horizontal` | Alle Panes nebeneinander, gleiche Breite |
| `even-vertical` | Alle Panes untereinander, gleiche Höhe |
| `main-horizontal` | Ein großes Pane oben, Rest unten |
| `main-vertical` | Ein großes Pane links, Rest rechts |
| `tiled` | Alle Panes gleichmäßig verteilt |

```zsh
# Layout wählen
Prefix + Space              # Durch Layouts rotieren
Prefix + Alt+1              # even-horizontal
Prefix + Alt+2              # even-vertical
Prefix + Alt+3              # main-horizontal
Prefix + Alt+4              # main-vertical
Prefix + Alt+5              # tiled
```

### 2.5    Copy-Mode

Der Copy-Mode ermöglicht Scrollen und Textauswahl.

| Kürzel | Beschreibung |
| ------ | ------------ |
| `Prefix + [` | Copy-Mode starten |
| `q` oder `Escape` | Copy-Mode beenden |
| `Pfeiltasten` / `hjkl` | Navigation |
| `Ctrl+u` / `Ctrl+d` | Halbe Seite hoch/runter |
| `g` / `G` | Anfang / Ende |
| `/` | Vorwärts suchen |
| `?` | Rückwärts suchen |
| `n` / `N` | Nächster / Vorheriger Treffer |
| `Space` | Auswahl starten |
| `Enter` | Auswahl kopieren und beenden |
| `Prefix + ]` | Einfügen |

**Mit vi-Modus (empfohlen):**

```zsh
# In ~/.tmux.conf
setw -g mode-keys vi

# Dann im Copy-Mode:
# v     = Auswahl starten
# y     = Kopieren
# V     = Zeilenweise auswählen
```

### 2.6    Command-Mode

Der Command-Mode erlaubt tmux-Befehle direkt einzugeben:

```
Prefix + :
```

Nützliche Befehle:

```
:new-session -s name           # Neue Session
:kill-session                  # Aktuelle Session beenden
:source ~/.tmux.conf           # Konfiguration neu laden
:list-keys                     # Alle Tastenkürzel anzeigen
:set -g option value           # Option setzen
:setw -g option value          # Window-Option setzen
```

## 3    Konfiguration

Die tmux-Konfiguration liegt in `~/.tmux.conf`.

### 3.1    Grundlegende Einstellungen

```bash
# ~/.tmux.conf

# Prefix ändern (Ctrl+a statt Ctrl+b)
unbind C-b
set -g prefix C-a
bind C-a send-prefix

# Schnelleres Escape (wichtig für vim)
set -sg escape-time 0

# History vergrößern
set -g history-limit 50000

# Nummerierung bei 1 beginnen
set -g base-index 1
setw -g pane-base-index 1

# Automatisch umnummerieren
set -g renumber-windows on

# Terminal-Farben
set -g default-terminal "screen-256color"
set -ga terminal-overrides ",xterm-256color:Tc"

# Maus aktivieren
set -g mouse on

# Vi-Modus für Copy-Mode
setw -g mode-keys vi

# Focus-Events (für vim etc.)
set -g focus-events on
```

### 3.2    Tastenkürzel anpassen

```bash
# Intuitivere Split-Befehle
bind | split-window -h -c "#{pane_current_path}"
bind - split-window -v -c "#{pane_current_path}"
unbind '"'
unbind %

# Neues Window im aktuellen Pfad
bind c new-window -c "#{pane_current_path}"

# Pane-Navigation mit Alt+Pfeiltasten (ohne Prefix)
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D

# Window-Navigation mit Shift+Pfeiltasten
bind -n S-Left previous-window
bind -n S-Right next-window

# Konfiguration neu laden
bind r source-file ~/.tmux.conf \; display "Config reloaded!"

# Panes synchronisieren (gleiche Eingabe in alle Panes)
bind S setw synchronize-panes
```

### 3.3    Statusleiste anpassen

```bash
# Statusleiste aktivieren
set -g status on

# Position
set -g status-position bottom

# Update-Intervall (Sekunden)
set -g status-interval 5

# Farben
set -g status-style 'bg=#333333 fg=#ffffff'

# Links: Session-Name
set -g status-left '#[fg=#000000,bg=#ffffff] #S #[default] '
set -g status-left-length 30

# Rechts: Datum und Uhrzeit
set -g status-right '#[fg=#ffffff] %Y-%m-%d %H:%M '
set -g status-right-length 50

# Window-Liste
set -g status-justify left

# Aktives Window hervorheben
setw -g window-status-current-style 'fg=#000000 bg=#00ff00 bold'
setw -g window-status-current-format ' #I:#W#F '

# Inaktive Windows
setw -g window-status-style 'fg=#888888'
setw -g window-status-format ' #I:#W#F '
```

### 3.4    Pane-Darstellung

```bash
# Aktiver Pane-Rahmen
set -g pane-active-border-style 'fg=#00ff00'

# Inaktiver Pane-Rahmen
set -g pane-border-style 'fg=#555555'

# Pane-Nummern-Anzeige
set -g display-panes-time 2000
set -g display-panes-colour '#555555'
set -g display-panes-active-colour '#00ff00'
```

### 3.5    Vollständige Beispielkonfiguration

```bash
# ~/.tmux.conf - Empfohlene Konfiguration

# === Grundeinstellungen ===
set -g prefix C-a                    # Prefix: Ctrl+a
unbind C-b
bind C-a send-prefix

set -sg escape-time 0                # Kein Delay nach Escape
set -g history-limit 50000           # Große History
set -g base-index 1                  # Windows ab 1 nummerieren
setw -g pane-base-index 1            # Panes ab 1 nummerieren
set -g renumber-windows on           # Automatisch umnummerieren
set -g mouse on                      # Maus aktivieren
setw -g mode-keys vi                 # Vi-Modus
set -g focus-events on               # Focus-Events

# === Terminal ===
set -g default-terminal "screen-256color"
set -ga terminal-overrides ",*256col*:Tc"

# === Tastenkürzel ===
# Splits (im aktuellen Verzeichnis)
bind | split-window -h -c "#{pane_current_path}"
bind - split-window -v -c "#{pane_current_path}"
bind c new-window -c "#{pane_current_path}"

# Pane-Navigation (Alt+Pfeiltasten)
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D

# Pane-Größe (Prefix + Shift+Pfeiltasten)
bind -r H resize-pane -L 5
bind -r J resize-pane -D 5
bind -r K resize-pane -U 5
bind -r L resize-pane -R 5

# Window-Navigation
bind -n S-Left previous-window
bind -n S-Right next-window

# Reload
bind r source-file ~/.tmux.conf \; display "Reloaded!"

# Copy-Mode (vi-style)
bind -T copy-mode-vi v send-keys -X begin-selection
bind -T copy-mode-vi y send-keys -X copy-selection-and-cancel
bind -T copy-mode-vi r send-keys -X rectangle-toggle

# macOS Clipboard
bind -T copy-mode-vi y send-keys -X copy-pipe-and-cancel "pbcopy"

# === Statusleiste ===
set -g status on
set -g status-position bottom
set -g status-interval 5
set -g status-style 'bg=#1a1a1a fg=#808080'

set -g status-left '#[fg=#1a1a1a,bg=#87d700,bold] #S #[default] '
set -g status-left-length 30

set -g status-right '#[fg=#808080] %d.%m.%Y #[fg=#c0c0c0]%H:%M '
set -g status-right-length 50

setw -g window-status-format ' #I:#W '
setw -g window-status-current-format '#[fg=#1a1a1a,bg=#87d700,bold] #I:#W '

# === Panes ===
set -g pane-border-style 'fg=#404040'
set -g pane-active-border-style 'fg=#87d700'

# === Messages ===
set -g message-style 'bg=#87d700 fg=#1a1a1a bold'
set -g display-time 2000
```

### 3.6    Konfiguration neu laden

```zsh
# Innerhalb tmux
Prefix + :
source ~/.tmux.conf

# Oder mit Tastenkürzel (wenn konfiguriert)
Prefix + r

# Von außerhalb
tmux source-file ~/.tmux.conf
```

## 4    screen-Vergleich

`screen` ist der ältere Terminal-Multiplexer (seit 1987). Es ist auf vielen Systemen vorinstalliert.

### 4.1    screen Grundlagen

```zsh
# Neue Session
screen

# Benannte Session
screen -S arbeit

# Session verlassen (detach)
Ctrl+a, dann d

# Sessions auflisten
screen -ls

# Wieder verbinden
screen -r arbeit

# Verbinden (auch wenn attached)
screen -x arbeit
```

### 4.2    screen-Befehle

| Kürzel       | Beschreibung               |
| ------------ | -------------------------- |
| `Ctrl+a d`   | Detach                     |
| `Ctrl+a c`   | Neues Window               |
| `Ctrl+a n`   | Nächstes Window            |
| `Ctrl+a p`   | Vorheriges Window          |
| `Ctrl+a "`   | Window-Liste               |
| `Ctrl+a 0-9` | Zu Window wechseln         |
| `Ctrl+a A`   | Window umbenennen          |
| `Ctrl+a k`   | Window beenden             |
| `Ctrl+a S`   | Horizontal teilen          |
| `Ctrl+a      | `                          |
| `Ctrl+a Tab` | Zwischen Regionen wechseln |
| `Ctrl+a X`   | Aktuelle Region schließen  |
| `Ctrl+a [`   | Copy-Mode                  |
| `Ctrl+a ]`   | Einfügen                   |
| `Ctrl+a ?`   | Hilfe                      |

### 4.3    screen-Konfiguration

Die Konfiguration liegt in `~/.screenrc`:

```bash
# ~/.screenrc

# Startmeldung deaktivieren
startup_message off

# Scrollback-Buffer
defscrollback 10000

# Visual Bell statt Audio
vbell on

# Statusleiste
hardstatus alwayslastline
hardstatus string '%{= kG}[ %{G}%H %{g}][%= %{= kw}%?%-Lw%?%{r}(%{W}%n*%f%t%?(%u)%?%{r})%{w}%?%+Lw%?%?%= %{g}][%{B} %m-%d %{W}%c %{g}]'

# Shell
shell -$SHELL
```

### 4.4    Vergleich tmux vs. screen

| Merkmal | tmux | screen |
| ------- | ---- | ------ |
| **Erste Version** | 2007 | 1987 |
| **Aktive Entwicklung** | Ja | Minimal |
| **Vorinstalliert** | Selten | Oft |
| **Prefix** | Ctrl+b | Ctrl+a |
| **Vertikale Splits** | Native | Mit Patch |
| **Scripting** | Sehr gut | Begrenzt |
| **Konfiguration** | Flexibel | Einfach |
| **Statusleiste** | Sehr anpassbar | Begrenzt |
| **Pane-Handling** | Ausgezeichnet | Rudimentär |
| **Copy-Mode** | Vi/Emacs | Emacs-style |
| **Session-Sharing** | Einfach | Möglich |

### 4.5    Wann welches Tool?

**tmux verwenden wenn:**

- Modernes System mit Homebrew/apt
- Komplexe Layouts mit vielen Panes
- Umfangreiche Anpassungen gewünscht
- Scripting/Automatisierung benötigt

**screen verwenden wenn:**

- Auf älteren/minimalen Systemen arbeiten
- Kein tmux installiert und keine Root-Rechte
- Einfache Anforderungen (nur Sessions)
- Gewohnte Umgebung

**Empfehlung:** Auf eigenen Systemen tmux installieren und verwenden. screen-Kenntnisse sind nützlich für Server ohne tmux.

## 5    Remote-Session-Management

Der Hauptvorteil von Terminal-Multiplexern: Sessions überleben Verbindungsabbrüche.

### 5.1    Typischer SSH-Workflow

```zsh
# 1. Mit Server verbinden
ssh user@server

# 2. tmux starten oder verbinden
tmux new -s arbeit
# oder
tmux attach -t arbeit

# 3. Arbeiten...

# 4. Sauber trennen (Session läuft weiter)
Prefix + d

# 5. SSH beenden
exit

# 6. Später wieder verbinden
ssh user@server
tmux attach -t arbeit
# Alles ist noch da!
```

### 5.2    Automatische tmux-Session

In `~/.bashrc` oder `~/.zshrc` auf dem Server:

```bash
# Automatisch tmux starten bei SSH-Login
if [[ -z "$TMUX" ]] && [[ -n "$SSH_CONNECTION" ]]; then
    tmux attach -t default || tmux new -s default
fi
```

**Vorsicht:** Kann Probleme bei SCP/SFTP verursachen. Besser:

```bash
# Nur bei interaktiver Shell
if [[ -z "$TMUX" ]] && [[ -n "$SSH_CONNECTION" ]] && [[ $- == *i* ]]; then
    tmux attach -t default 2>/dev/null || tmux new -s default
fi
```

### 5.3    SSH-Verbindung in tmux

```zsh
# Lokale tmux-Session
tmux new -s remote-arbeit

# Innerhalb tmux: SSH-Verbindung
ssh user@server

# Wenn Verbindung abbricht: tmux-Session lokal intakt
# Einfach erneut SSH starten
```

### 5.4    Mehrere Server verwalten

```zsh
# Session für jeden Server
tmux new -s server1 -d "ssh user@server1"
tmux new -s server2 -d "ssh user@server2"

# Zwischen Sessions wechseln
Prefix + s

# Oder: Ein Window pro Server
tmux new -s servers
tmux new-window -t servers -n "web" "ssh user@webserver"
tmux new-window -t servers -n "db" "ssh user@dbserver"
tmux new-window -t servers -n "app" "ssh user@appserver"
```

### 5.5    Session-Sharing

Mehrere Benutzer können dieselbe Session sehen (Pair Programming, Support):

**tmux:**

```zsh
# Benutzer 1: Session erstellen
tmux new -s shared

# Benutzer 2: Gleiche Session (beide sehen dasselbe)
tmux attach -t shared

# Benutzer 2: Eigene Ansicht (unabhängige Window-Auswahl)
tmux new -t shared -s user2
```

**screen:**

```zsh
# Multi-User aktivieren (in .screenrc oder zur Laufzeit)
multiuser on
acladd username

# Anderer Benutzer verbindet
screen -x user/sessionname
```

### 5.6    Persistente Layouts

Session beim Start mit vordefiniertem Layout:

**Script-basiert:**

```zsh
#!/bin/zsh
# ~/scripts/dev-session.sh

SESSION="dev"

# Prüfen ob Session existiert
tmux has-session -t $SESSION 2>/dev/null

if [ $? != 0 ]; then
    # Neue Session mit erstem Window
    tmux new-session -d -s $SESSION -n "editor"

    # Editor-Window: vim
    tmux send-keys -t $SESSION:editor "cd ~/projekt && vim" Enter

    # Zweites Window: Server
    tmux new-window -t $SESSION -n "server"
    tmux send-keys -t $SESSION:server "cd ~/projekt && npm run dev" Enter

    # Drittes Window: Terminal (gesplittet)
    tmux new-window -t $SESSION -n "term"
    tmux split-window -h -t $SESSION:term
    tmux send-keys -t $SESSION:term.1 "cd ~/projekt" Enter
    tmux send-keys -t $SESSION:term.2 "cd ~/projekt && git status" Enter

    # Zum Editor-Window wechseln
    tmux select-window -t $SESSION:editor
fi

# Zur Session verbinden
tmux attach -t $SESSION
```

**Mit tmuxinator (empfohlen für komplexe Setups):**

```zsh
# Installation
brew install tmuxinator

# Neues Projekt erstellen
tmuxinator new projekt
```

```yaml
# ~/.config/tmuxinator/projekt.yml
name: projekt
root: ~/projekt

windows:
  - editor:
      layout: main-vertical
      panes:
        - vim
        - git status
  - server:
      panes:
        - npm run dev
  - logs:
      panes:
        - tail -f logs/app.log
        - tail -f logs/error.log
```

```zsh
# Session starten
tmuxinator start projekt

# Session beenden
tmuxinator stop projekt
```

### 5.7    tmux und SSH-Agent

SSH-Agent-Forwarding kann in tmux problematisch sein. Lösung:

```bash
# In ~/.tmux.conf
set -g update-environment "SSH_AUTH_SOCK SSH_AGENT_PID"

# Oder: Symlink-Methode in ~/.zshrc
if [[ -n "$SSH_AUTH_SOCK" ]] && [[ "$SSH_AUTH_SOCK" != "$HOME/.ssh/ssh_auth_sock" ]]; then
    ln -sf "$SSH_AUTH_SOCK" "$HOME/.ssh/ssh_auth_sock"
fi
export SSH_AUTH_SOCK="$HOME/.ssh/ssh_auth_sock"
```

### 5.8    Nützliche Aliase

```bash
# In ~/.zshrc

# tmux-Aliase
alias ta='tmux attach -t'
alias tl='tmux list-sessions'
alias tn='tmux new -s'
alias tk='tmux kill-session -t'

# Schnell zu Session oder neue erstellen
t() {
    if [[ -n "$1" ]]; then
        tmux attach -t "$1" 2>/dev/null || tmux new -s "$1"
    else
        tmux attach 2>/dev/null || tmux new -s main
    fi
}
```

### 5.9    Cheatsheet

**Session-Management:**

```
tmux                         Neue Session
tmux new -s NAME             Benannte Session
tmux ls                      Sessions auflisten
tmux attach -t NAME          Zu Session verbinden
tmux kill-session -t NAME    Session beenden
Prefix + d                   Detach
Prefix + s                   Session-Auswahl
```

**Windows:**

```
Prefix + c                   Neues Window
Prefix + n / p               Nächstes / Vorheriges
Prefix + 0-9                 Zu Window wechseln
Prefix + ,                   Umbenennen
Prefix + &                   Schließen
Prefix + w                   Window-Liste
```

**Panes:**

```
Prefix + %                   Vertikal teilen
Prefix + "                   Horizontal teilen
Prefix + Pfeiltasten         Navigation
Prefix + z                   Zoom (Vollbild)
Prefix + x                   Schließen
Prefix + Space               Layout wechseln
Prefix + !                   Pane → Window
```

**Copy-Mode:**

```
Prefix + [                   Copy-Mode starten
q / Escape                   Beenden
Space / v                    Auswahl starten
Enter / y                    Kopieren
Prefix + ]                   Einfügen
```
