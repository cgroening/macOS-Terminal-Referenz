# Terminals und Erweiterungen

Im folgenden werden neben dem Standard-Terminal weitere beliebte Terminal-Apps für macOS vorgestellt. Es wird kurz erklärt, was sie besonders machen und welche Vor- und Nachteile sie haben.

## 1    Begriffserklärungen

In diesem Abschnitt wird erklärt, was Terminals, Shells, Frameworks und Prompts sind und wie sie aufeinander aufbauen.

### 1.1    Terminal

**Beschreibung:**

Das Fensterprogramm, das du auf deinem Computer öffnest, um Textbefehle einzugeben. Ein Terminal ist die Benutzeroberfläche, die mit einer Shell kommuniziert.

**Beispiele:**

- Apple Terminal (Standard in macOS)
- iTerm2
- Warp

**Aufgabe:**

- Zeigt Eingaben, Ausgaben, Farben, Tabs usw.
-  Es führt selbst keine Befehle aus, sondern _reicht sie an die Shell weiter_.

### 1.2    Shell

**Beschreibung:**

Die Kommandozeilen-Umgebung, die Befehle interpretiert und ausführt. Sie ist das eigentliche "Gehirn" hinter dem Terminal.

**Beispiele:**

- bash (ältere Standardshell)
- zsh (Standard auf macOS seit Catalina)
- fish (freundlich & modern)
- nushell (strukturiert & datenorientiert)
- PowerShell (Microsoft, aber auch für macOS/Linux verfügbar)

**Aufgabe:**

- Befehle entgegennehmen, interpretieren und ausführen
- Skripte schreiben, Variablen verwalten, Prozesse steuern

### 1.3    Framework

**Beschreibung:**

Ein Erweiterungssystem für eine Shell (meist für zsh), das Plugins, Themes und Konfigurationen organisiert. Es macht die Shell komfortabler, modularer und automatisierter.

**Beispiele:**

- Oh My Zsh (riesige Plugin-Sammlung für zsh)
- Prezto (leichtgewichtige, schnellere Alternative zu Oh My Zsh)
- Zinit, Zplug, Antigen, Znap (Plugin-Manager für zsh)

**Aufgabe:**

- Plugins laden (z. B. für Git, Docker, Syntax-Highlighting, Autocomplete)
- Themes aktivieren
- Die Shell beim Start konfigurieren

### 1.4    Prompt

**Beschreibung:**

Der visuelle Teil der Shell, d. h. der Zeile, die eingegeben wird. Ein Prompt zeigt z. B. Ordner, Git-Status, Uhrzeit, Exit-Code oder andere Informationen.

**Beispiele:**

- **Starship** (universeller, moderner Prompt für alle Shells)
- **Powerlevel10k** (extrem anpassbar, für zsh)
- **Pure** (minimalistisch)
- **Spaceship** (optisch sehr ansprechend, Git-Infos integriert)

**Aufgabe:**

- Das "Gesicht" der Shell
- Zeigt Informationen und Statusindikatoren in Echtzeit

### 1.5    Zusammenspiel von Terminal, Shell, Framework und Prompt

```
┌──────────────────────────────────────────┐
│      Terminal (z. B. iTerm2, Warp)       │
│       → öffnet und zeigt die Shell       │
│                                          │
│ ┌──────────────────────────────────────┐ │
│ │     Shell (z. B. zsh oder bash)      │ │
│ │          → für Befehle aus           │ │
│ │                                      │ │
│ │ ┌──────────────────────────────────┐ │ │
│ │ │   Framework (z. B. Oh My Zsh)    │ │ │
│ │ │  → verwaltet Plugins und Themes  │ │ │
│ │ │                                  │ │ │
│ │ │ ┌──────────────────────────────┐ │ │ │
│ │ │ │   Prompt (z. B. Starship)    │ │ │ │
│ │ │ │   → Zeigt Info und Design    │ │ │ │
│ │ │ └──────────────────────────────┘ │ │ │
│ │ └──────────────────────────────────┘ │ │
│ └──────────────────────────────────────┘ │
└──────────────────────────────────────────┘
```

| Ebene         | Beispiele         | Hauptfunktion                       | Läuft ...                         | Wichtig für               |
| ------------- | ----------------- | ----------------------------------- | --------------------------------- | ------------------------- |
| **Terminal**  | iTerm2, Warp      | Darstellung, Tabs, Farben, UI       | unabhängig                        | Benutzeroberfläche        |
| **Shell**     | zsh, bash         | Befehlseingabe & Ausführung         | im Terminal                       | Logik & Skripting         |
| **Framework** | Oh My Zsh, Prezto | Plugin-Management & Themes          | in der Shell                      | Komfort & Automatisierung |
| **Prompt**    | Starship, Pure    | Visuelles Erscheinungsbild & Status | im Framework oder direkt in Shell | Übersicht & Design        |

> [!NOTE]
> > **Terminal** zeigt → **Shell** führt aus → **Framework** organisiert → **Prompt** präsentiert

### 1.6    Beispiele für Kombinationen von Terminal, Shell, Framework und Prompt

| Nutzerprofil                            | Terminal       | Shell       | Framework     | Prompt                                   |
| --------------------------------------- | -------------- | ----------- | ------------- | ---------------------------------------- |
| **macOS-Standardnutzer**                | Apple Terminal | zsh         | –             | Standard zsh Prompt                      |
| **Entwickler & Power-User**             | iTerm2 / Warp  | zsh         | – / Oh My Zsh | – / Warp Built-in Prompt / Powerlevel10k |
| **Cross-Plattform-Entwickler**          | Tabby          | bash / fish | –             | Starship                                 |
| **Minimalist / Performance-Enthusiast** | Alacritty      | zsh         | Prezto / Znap | Starship                                 |
| **Team & KI-orientiert**                | Warp           | zsh         | Oh My Zsh     | Warp Built-in Prompt oder Starship       |

## 2    Terminals für macOS

### 2.1    Apple Terminal

[Webseite](https://support.apple.com/de-de/guide/terminal/welcome/mac), kein GitHub

**Beschreibung:**

Das vorinstallierte Terminal von macOS. Es ist schlicht, zuverlässig und tief ins System integriert.

**Vorteile:**

- Keine Installation erforderlich :luc_arrow_right: direkt einsatzbereit
- Sehr stabil und ressourcenschonend
- Vollständig kompatibel mit macOS (z. B. Shell-Integration, Sicherheit, Schriftarten)

**Nachteile:**

- Keine modernen Komfortfunktionen (Tabs, Split Views, Themes sind rudimentär)
- Wenig anpassbar ohne externe Tools
- Kein "modernes" UI oder interaktive Features

### 2.2    iTerm2

[Webseite](https://iterm2.com), [GitHub](https://github.com/gnachman/iTerm2)

**Beschreibung:**

Ein extrem beliebter und mächtiger Terminal-Ersatz, der seit Jahren als Standard für Power-User gilt.

**Vorteile:**

- Tabs, Split Panes, Hotkeys und Profile
- Sehr anpassbar (Themes, Transparenz, Triggers, Status Bar)
- Mächtige Such- und Verlaufsfunktionen
- Unterstützung für Maus-Events und sogar Inline-Bilder
- Kostenlos und Open Source

**Nachteile:**

- Die vielen Funktionen und Optionen können anfangs überfordern

### 2.3    Warp

[Warp](https://www.warp.dev), [GitHub](https://github.com/warpdotdev/Warp)

**Beschreibung:**

Ein modernes Terminal mit Rust-Backend und graphischer Shell-Erweiterung. Warp bietet Funktionen für Terminalarbeit mit KI, Autocomplete und Teamunterstützung.

**Vorteile:**

- Sehr modernes UI mit Markdown, Blöcken und Befehlshistorie
- KI-Autocomplete & Chat-Integration
- Kollaborations-Features (Team-Sharing von Commands, Workflows)
- Sehr hohe Leistung dank Rust

**Nachteile:**

- Proprietär (nicht Open Source)
- Cloud-Features erfordern Account

### 2.4    Alacritty

[Webseite](https://alacritty.org), [GitHub](https://github.com/alacritty/alacritty)

**Beschreibung:**

Minimalistisches, GPU-beschleunigtes Terminal, das auf maximale Performance und Einfachheit ausgelegt ist.

**Vorteile:**

- Extrem schnell (GPU-Rendering)
- Cross-Plattform und Open Source
- Konfiguration über YAML-Datei – einfach für Entwickler

**Nachteile:**

- Keine GUI-Einstellungen (alles per Config-Datei)
- Keine Tabs oder Splits nativ (nur über Multiplexer wie tmux)
- Weniger Komfortfunktionen

### 2.5    Kitty

[Webseite](https://sw.kovidgoyal.net/kitty/), [GitHub](https://github.com/kovidgoyal/kitty)

**Beschreibung:**

Modernes, GPU-beschleunigtes Terminal mit Fokus auf Geschwindigkeit und Erweiterbarkeit.

**Vorteile:**

- Schnell, leicht und optisch modern
- Unterstützung für Tabs, Splits, Grafiken und Emojis
- Erweiterbar durch Python-Skripte

**Nachteile:**

- Komplexe Konfiguration
- Weniger benutzerfreundlich als andere Terminals

### 2.6    Hyper

[Webseite](https://hyper.is), [GitHub](https://github.com/vercel/hyper)

**Beschreibung:**

Auf Electron basierendes Terminal mit Webtechnologien (HTML, CSS, JS).

**Vorteile:**

- Sehr anpassbar dank Plugins und Themes (JavaScript-basiert)
- Attraktives, modernes Design
- Ideal für Webentwickler, die JS beherrschen

**Nachteile:**

- Electron-basiert → höherer RAM-Verbrauch als native Terminals
- Teilweise instabil bei langen Sessions
- Weniger performant

### 2.7    Tabby

[Webseite](https://tabby.sh), [GitHub](https://github.com/Eugeny/tabby)

**Beschreibung:**

Mmoderner, plattformübergreifender Terminal-Emulator mit Fokus auf Benutzerfreundlichkeit, Design und Erweiterbarkeit. Geschrieben in TypeScript/Electron.

**Vorteile:**

- Sehr schönes, modernes UI
- Tabs, Splits, SSH-Manager, Serial Support
- Plugins und Themes (JS-basiert)
- Cross-Plattform (macOS, Linux, Windows)

**Nachteile:**

- Electron-basiert → höherer RAM-Verbrauch als native Terminals
- Nicht so schnell wie native Terminals (z. B. iTerm2 oder Alacritty)
- Weniger stabil bei extrem langen Sessions

### 2.8    Ghostty

[Webseite](https://ghostty.org), [GitHub](https://github.com/ghostty-org/ghostty)

**Beschreibung:**

Moderner Terminal-Emulator von Mitchell Hashimoto (HashiCorp-Gründer), geschrieben in Zig. Ghostty kombiniert hohe Performance durch GPU-Beschleunigung mit einer plattformnativen UI (Swift/AppKit auf macOS, GTK4 auf Linux).

**Vorteile:**

- Sehr schnell durch GPU-Rendering (Metal auf macOS, OpenGL auf Linux)
- Plattformnative UI – fühlt sich wie eine echte Mac-App an
- Exzellente Defaults – funktioniert ohne Konfiguration out-of-the-box
- Einfache Konfiguration über Key-Value-Paare in `~/.config/ghostty/config`
- Tabs, Splits, hunderte eingebaute Themes
- Unterstützt Kitty Graphics Protocol und Kitty Keyboard Protocol
- Nerd Fonts funktionieren direkt ohne Setup
- Open Source

**Nachteile:**

- Relativ neu (Version 1.0 Ende 2024)
- Kein Windows-Support (nur macOS und Linux)
- Weniger etabliert und dokumentiert als iTerm2

### 2.9    Vergleich

| Terminal                                                                         | Open Source | Leistung | Anpass-<br>barkeit | Ober-<br>fläche | Moderne Features<br>(KI, Blöcke, Teams) | Schwierig-<br>keitsgrad |
| -------------------------------------------------------------------------------- | ----------- | -------- | ------------------ | --------------- | --------------------------------------- | ----------------------- |
| **[Apple Terminal](https://support.apple.com/de-de/guide/terminal/welcome/mac)** | ✅           | 🟢       | 🔴                 | Klassisch       | 🔴                                      | 🟢                      |
| **[iTerm2](https://iterm2.com)**                                                 | ✅           | 🟢       | 🟢🟢🟢             | Klassisch       | 🔴                                      | 🟡                      |
| **[Warp](https://www.warp.dev)**                                                 | ❌           | 🟢🟢     | 🟡                 | Modern          | 🟢🟢🟢                                  | 🟢                      |
| **[Alacritty](https://alacritty.org)**                                           | ✅           | 🟢🟢🟢   | 🟡                 | Modern          | 🔴                                      | 🟡                      |
| **[Kitty](https://sw.kovidgoyal.net/kitty/)**                                    | ✅           | 🟢🟢     | 🟢                 | Modern          | 🔴                                      | 🟡                      |
| **[Hyper](https://hyper.is)**                                                    | ✅           | 🟡       | 🟢🟢               | Modern          | 🟡                                      | 🟢                      |
| **[Tabby](https://tabby.sh)**                                                    | ✅           | 🟡       | 🟢🟢               | Sehr Modern     | 🟡                                      | 🟢                      |
| **[Ghostty](https://ghostty.org)**                                               | ✅           | 🟢🟢🟢   | 🟡                 | Modern/Nativ    | 🟡                                      | 🟢                      |

**Zusammenfassung:**

- **Apple Terminal**
  ([Webseite](https://support.apple.com/de-de/guide/terminal/welcome/mac), kein GitHub)
  Standard-Terminal von macOS, stabil und einfach, aber ohne moderne Features.

- **iTerm2**
  ([Webseite](https://iterm2.com), [GitHub](https://github.com/gnachman/iTerm2))
  Sehr funktionsreich: Tabs, Splits, Triggers, Profile, Open Source.

- **Warp**
  ([Warp](https://www.warp.dev), [GitHub](https://github.com/warpdotdev/Warp))
  Rust-basiertes Terminal mit KI, Blöcken, Workflows und Team-Sharing.

- **Alacritty**
  ([Webseite](https://alacritty.org), [GitHub](https://github.com/alacritty/alacritty))
  GPU-beschleunigt, blitzschnell und minimalistisch, ideal mit tmux.

- **Kitty**
  ([Webseite](https://sw.kovidgoyal.net/kitty/), [GitHub](https://github.com/kovidgoyal/kitty))
  Schnelles, GPU-basiertes Terminal mit Tabs, Splits und Python-Erweiterungen.

- **Hyper**
  ([Webseite](https://hyper.is), [GitHub](https://github.com/vercel/hyper))
  Electron-Terminal mit Webtechnologien, sehr anpassbar durch Plugins.

- **Tabby**
  ([Webseite](https://tabby.sh), [GitHub](https://github.com/Eugeny/tabby))
  Electron-basiert, schönes Design, Plugins, Themes, SSH-/Serial-Manager.

- **Ghostty** ([Webseite](https://ghostty.org), [GitHub](https://github.com/ghostty-org/ghostty))
  Zig-basiert, GPU-beschleunigt, plattformnative UI, exzellente Defaults.

**Nutzungsempfehlung nach Anwendungsfall:**

| **Anwendungsfall**                        | **Empfohlenes Terminal** |
| ----------------------------------------- | ------------------------ |
| **Einsteiger & Minimalisten**             | Apple Terminal           |
| **Power-User & Entwickler**               | iTerm2                   |
| **Teamarbeit & KI-Unterstützung**         | Warp                     |
| **Maxiamle Performance & Tiling**         | Alacritty oder Kitty     |
| **Web-/JS-Entwickler**                    | Hyper oder Tabby         |
| **SSH & Multi-Session-Management**        | Tabby                    |
| **Native macOS-Experience & Performance** | Ghostty                  |

## 3    Shells für macOS

### 3.1    zsh (Z Shell)

[Webseite](https://www.zsh.org), [GitHub](https://github.com/zsh-users/zsh)

**Beschreibung:**

Standard-Shell auf macOS seit Catalina (v. 10.15; 2019). Zsh ist mächtig, skriptfähig und stark erweiterbar.

**Vorteile:**

- Modern, interaktive Features (Tab-Completion, Globbing)
- Viele Plugins und Themes verfügbar (z. B. via Oh My Zsh)
- Kompatibel zu bash-Skripten

**Nachteile:**

- Einige ältere bash-Skripte erfordern Anpassungen
- Steile Lernkurve bei komplexen Konfigurationen

### 3.2    bash

[Webseite](https://www.gnu.org/software/bash/), [GitHub](https://github.com/bminor/bash)

**Beschreibung:**

Ältere Standardshell, sehr verbreitet und auf fast allen Unix-Systemen verfügbar.

**Vorteile:**

- Stabil und gut dokumentiert
- Große Community & viele Tutorials
- Skripting sehr zuverlässig

**Nachteile:**

- Weniger moderne Features als zsh oder fish
- Interaktive Nutzung weniger komfortabel

### 3.3    fish (Friendly Interactive Shell)

[Webseite](https://fishshell.com), [GitHub](https://github.com/fish-shell/fish-shell)

**Beschreibung:**

Benutzerfreundliche, moderne Shell, fokussiert auf einfache Nutzung und Autovervollständigung.

**Vorteile:**

- Auto-Suggestions & Syntax-Highlighting standardmäßig
- Einfach zu konfigurieren, keine Frameworks nötig
- Moderne Features out-of-the-box

**Nachteile:**

- Nicht 100 % POSIX-kompatibel → manche Skripte funktionieren nicht direkt
- Kleinere Community als zsh/bash

### 3.4    nushell

[Webseite](https://www.nushell.sh), [GitHub](https://github.com/nushell/nushell)

**Beschreibung:**

Datenorientierte Shell, die Tabellen und strukturierte Daten direkt verarbeiten kann.


**Vorteile:**

- Ideal für Workflows mit CSV, JSON, SQL-Daten
- Modernes CLI mit strukturierter Ausgabe
- Cross-Plattform

**Nachteile:**

- Neue Syntax → Umstieg für erfahrene bash/zsh-Nutzer aufwendig
- Weniger verbreitet, kleineres Ökosystem

### 3.5    PowerShell

[Webseite](https://learn.microsoft.com/powershell/), [GitHub](https://github.com/PowerShell/PowerShell)

**Beschreibung:**

Microsoft-Shell, ursprünglich für Windows, jetzt cross-platform. Fokus auf Skripterstellung und Automatisierung.

**Vorteile:**

- Mächtige Skriptmöglichkeiten, Object-Pipeline
- Plattformübergreifend (Windows, macOS, Linux)
- Große Community & Module verfügbar

**Nachteile:**

- Für macOS-Nutzer ungewohnt (anderer Syntaxstil)
- CLI-Optik weniger minimalistisch

### 3.6    Vergleich

| Shell                                                     | Open Source | Leistung | Interaktive Features | Skriptfähigkeit | Lernkurve |
| --------------------------------------------------------- | ----------- | -------- | -------------------- | --------------- | --------- |
| **[bash](https://www.gnu.org/software/bash/)**            | ✅           | 🟢🟢🟢   | 🟡                   | 🟢🟢🟢          | 🟡        |
| **[zsh](https://www.zsh.org)**                            | ✅           | 🟢🟢     | 🟢🟢                 | 🟢🟢🟢          | 🟡        |
| **[fish](https://fishshell.com)**                         | ✅           | 🟢🟢     | 🟢🟢🟢               | 🟢🟢            | 🟡        |
| **[nushell](https://www.nushell.sh)**                     | ✅           | 🟢🟢     | 🟢🟢                 | 🟢🟢            | 🟢        |
| **[PowerShell](https://learn.microsoft.com/powershell/)** | ✅           | 🟢🟢     | 🟢🟢                 | 🟢🟢🟢          | 🟢        |

**Zusammenfasung:**

- **zsh**
  ([Webseite](https://www.zsh.org), [GitHub](https://github.com/zsh-users/zsh))
  Erweiterte Shell mit Autovervollständigung, Themes, Plugins; beliebt für interaktives Arbeiten und Anpassung.

- **bash**
  ([Webseite](https://www.gnu.org/software/bash/), [GitHub](https://github.com/bminor/bash))
  Klassische Unix-Shell, sehr verbreitet, ideal für Scripting und Automatisierung, stabil und universell einsetzbar.

- **fish**
  ([Webseite](https://fishshell.com), [GitHub](https://github.com/fish-shell/fish-shell))
  Benutzerfreundlich, moderne Features wie Autosuggestions und Syntax Highlighting, weniger skriptorientiert, einfach zu lernen.

- **nushell**
  ([Webseite](https://www.nushell.sh), [GitHub](https://github.com/nushell/nushell))
  Daten-orientierte Shell für strukturiertes Arbeiten mit Tabellen, JSON, CSV; modernes CLI-Konzept.

- **PowerShell**
  ([Webseite](https://learn.microsoft.com/powershell/), [GitHub](https://github.com/PowerShell/PowerShell))
  Microsoft-Shell, sehr mächtig für Systemadministration, plattformübergreifend, stark auf Objekte statt Text fokussiert.

**Nutzungsempfehlung nach Anwendungsfall:**

| Anwendungsfall                                   | Empfohlene Shell  |
| ------------------------------------------------ | ----------------- |
| **Skripting & Automatisierung**                  | bash / PowerShell |
| **Interaktive Nutzung & Auto-Vervollständigung** | zsh / fish        |
| **Datenorientiertes Arbeiten (JSON, CSV)**       | nushell           |
| **Windows-spezifische Administration**           | PowerShell        |

## 4    Frameworks

Shell-Frameworks erleichtern die Verwaltung von Plugins, Themes und Konfigurationen für zsh (und teilweise andere Shells).

### 4.1    Oh My Zsh

[Webseite](https://ohmyz.sh), [GitHub](https://github.com/ohmyzsh/ohmyzsh)

**Beschreibung:**

Bekanntestes zsh-Framework, enthält hunderte Plugins und Themes.

**Vorteile:**

- Einfach zu installieren und konfigurieren
- Riesiges Plugin-Ökosystem
- Große Community

**Nachteile:**

- Etwas langsamer beim Starten bei vielen Plugins
- Überladen für Minimalisten

### 4.2    Prezto

[GitHub](https://github.com/sorin-ionescu/prezto)

**Beschreibung:**

Leichtgewichtige Alternative zu Oh My Zsh, schneller und schlanker.

**Vorteile:**

- Schneller als Oh My Zsh
- Modularer Aufbau
- Viele nützliche Plugins

**Nachteile:**

- Weniger Themes als bei Oh My Zsh
- Kleinere Community als bei Oh My Zsh

### 4.3    Zinit

[GitHub](https://github.com/zdharma-continuum/zinit)

**Beschreibung:**

Zinit (früher zplugin) ist ein sehr flexibler und leistungsstarker Zsh-Plugin-Manager, der gezieltes Laden von Plugins, Snippets und Themes erlaubt. Unterstützt auch Turbo-Features wie Lazy-Loading und asynchrones Laden.

**Vorteile:**

- Extrem flexibel, unterstützt komplexe Konfigurationen
- Sehr schnell durch Lazy-Loading von Plugins
- Unterstützt Snippets, Hooks und parallele Updates

**Nachteile:**

- Hohe Einarbeitungszeit
- Komplexere Konfiguration kann unübersichtlich werden

### 4.4    Zplug

[GitHub](https://github.com/zplug/zplug)

**Beschreibung:**

Zplug ist ein moderner Zsh-Plugin-Manager, der Plugin-Installation, Updates und Lazy-Loading vereinfacht. Plugins und Themes können zentral verwaltet werden.


**Vorteile:**

- Einfache Installation und Verwaltung von Plugins
- Unterstützt Lazy-Loading für Leistung
- Gute Community und aktive Weiterentwicklung

**Nachteile:**

- Weniger mächtig als Zinit bei komplexen Setups
- Dokumentation teilweise lückenhaft

### 4.5    Antigen

[GitHub](https://github.com/zsh-users/antigen)

**Beschreibung:**

Antigen ist ein minimalistischer Zsh-Plugin-Manager, der von Oh My Zsh inspiriert ist. Er lädt Plugins und Themes auf einfache Weise und eignet sich besonders für Nutzer, die schnell starten wollen.

**Vorteile:**

- Schnell und einfach zu konfigurieren
- Unterstützt Oh-My-Zsh-, Prezto- und andere Plugins
- Leichtgewichtig

**Nachteile:**

- Weniger Funktionen im Vergleich zu Zinit oder Zplug
- Eher für einfache bis mittlere Konfigurationen geeignet

### 4.6    Znap

[GitHub](https://github.com/marlonrichert/zsh-snap)

**Beschreibung:**

Znap ist ein moderner, minimalistischer Plugin-Manager für Zsh, der besonders auf Performance setzt. Plugins werden nur bei Bedarf geladen, Updates sind einfach und schnell.

**Vorteile:**

- Sehr schnell durch Lazy-Loading
- Einfaches Update-Management
- Minimalistische und saubere Konfiguration

**Nachteile:**

- Weniger bekannt, kleinere Community
- Weniger Features als Zinit für komplexe Setups

### 4.7    Vergleich

| Framework                                               | Open Source | Setup-Aufwand | Plugin-Unterstützung | Theme-Unterstützung | Wartung / Stabilität | Lernkurve |
| ------------------------------------------------------- | ----------- | ------------- | -------------------- | ------------------- | -------------------- | --------- |
| **[Oh My Zsh](https://ohmyz.sh)**                       | ✅           | 🟢            | 🟢🟢🟢               | 🟢🟢                | 🟢                   | 🟡        |
| **[Prezto](https://github.com/sorin-ionescu/prezto)**   | ✅           | 🟢🟢          | 🟢🟢                 | 🟢🟢                | 🟢🟢                 | 🟡        |
| **[Zinit](https://github.com/zdharma-continuum/zinit)** | ✅           | 🟡            | 🟢🟢🟢               | 🟢🟢                | 🟢🟢                 | 🟢        |
| **[Zplug](https://github.com/zplug/zplug)**             | ✅           | 🟢🟢          | 🟢🟢🟢               | 🟢🟢                | 🟢🟢                 | 🟢        |
| **[Antigen](https://github.com/zsh-users/antigen)**     | ✅           | 🟡            | 🟢🟢🟢               | 🟢🟢                | 🟢🟢                 | 🟢        |
| [**Znap**](https://github.com/marlonrichert/zsh-snap)   | ✅           | 🟢            | 🟢🟢🟢               | 🟢                  | 🟢🟢                 | 🟢        |

**Zusammenfassung:**

- **Oh My Zsh**
  ([Webseite](https://ohmyz.sh), [GitHub](https://github.com/ohmyzsh/ohmyzsh))
  Einsteigerfreundliches Framework für zsh, viele Plugins und Themes, einfache Installation, weit verbreitet.

- **Prezto**
  ([GitHub](https://github.com/sorin-ionescu/prezto))
  Schlankes, performantes zsh-Framework, schneller als Oh My Zsh, stabil, viele nützliche Defaults und Optimierungen.

- **Zinit**
  ([GitHub](https://github.com/zdharma-continuum/zinit))
  Extrem anpassbar, leistungsstarker Plugin-Manager für zsh, viele Optimierungen möglich, für Power-User.

- **Zplug**
  ([GitHub](https://github.com/zplug/zplug))
  Moderne Alternative zu Antigen/Zinit, Plugin-Management und Themes für zsh, leichtgewichtig, erweiterbar.

- **Antigen**
  ([GitHub](https://github.com/zsh-users/antigen))
  Plugin-Manager für zsh, hohe Flexibilität, unterstützt Themes, viele Plugins, gut für fortgeschrittene Nutzer.

- **Znap**
  ([GitHub](https://github.com/marlonrichert/zsh-snap))
  Minimalistischer, performanter Zsh-Plugin-Manager, der Plugins nur bei Bedarf lädt, schnelle Updates, einfache Konfiguration für fortgeschrittene Nutzer.

**Nutzungsempfehlung nach Anwendungsfall:**

| Anwendungsfall                     | Empfohlenes Framework |
| -------------------------------------- | ------------------------- |
| **Einsteiger, schnelle Installation**  | Oh My Zsh                 |
| **Schlank & performant**               | Prezto                    |
| **Viele Plugins & Themes**             | Antigen / Zinit / Zplug   |
| **Power-User, maximale Anpassbarkeit** | Zinit / Zplug             |

## 5    Prompts

Prompts sind die sichtbaren Eingabezeilen in der Shell, oft mit Statusinformationen, Farben und Symbolen.

### 5.1    Starship

[Webseite](https://starship.rs), [GitHub](https://github.com/starship/starship)

**Beschreibung:**

Universeller Prompt für alle Shells, modern, schnell und anpassbar.

**Vorteile:**

- Cross-Shell & Cross-Platform
- Sehr anpassbar mit einfacher Konfigurationsdatei
- Schnelle Performance

**Nachteile:**

- Manche Features müssen manuell konfiguriert werden
- Für komplette Anpassung Kenntnisse von TOML nötig

### 5.2    Powerlevel10k

[GitHub](https://github.com/romkatv/powerlevel10k)

**Beschreibung:**

Extrem anpassbarer Prompt für zsh, bekannt für Performance und Optik.

**Vorteile:**

- Interaktives Setup, sehr individuell
- Git-Status, Icons, Farben, Unicode-Zeichen
- Schnell trotz umfangreicher Features

**Nachteile:**

- Nur für zsh
- Einarbeitung nötig, wenn viele Features aktiviert werden

### 5.3    Pure

[GitHub](https://github.com/sindresorhus/pure)

**Beschreibung:**

Minimalistischer, sauberer Prompt für zsh und bash.


**Vorteile:**

- Minimalistisch & schnell
- Fokus auf Klarheit
- Zeigt Git-Status, aber keine unnötigen Extras

**Nachteile:**

- Weniger visuelle Features
- Kaum Anpassungsmöglichkeiten

### 5.4    Spaceship

[GitHub](https://github.com/spaceship-prompt/spaceship-prompt)

**Beschreibung:**

Optisch ansprechender zsh-Prompt, zeigt Git-Info, Node-Version, Docker-Status uvm.

**Vorteile:**

- Viele Funktionen direkt integriert
- Optisch sehr ansprechendes Design
- Open Source

**Nachteile:**

- Nur für zsh
- Kann bei umfangreichen Infos den Start verzögern

### 5.5    Vergleich

| **Prompt**                                                        | **Open Source** | **Geschwindigkeit** | **Anpassbarkeit** | **Shell-Kompatibilität** | **Featureumfang** | **Lernkurve** |
| ----------------------------------------------------------------- | --------------- | ------------------- | ----------------- | ------------------------ | ----------------- | ------------- |
| [Starship](https://starship.rs)                                   | ✅               | 🟢🟢🟢              | 🟢🟢🟢            | Alle gängigen            | 🟢🟢🟢            | 🟡            |
| [Powerlevel10k](https://github.com/romkatv/powerlevel10k)         | ✅               | 🟢🟢🟢              | 🟢🟢🟢            | zsh                      | 🟢🟢🟢            | 🟢            |
| [Pure](https://github.com/sindresorhus/pure)                      | ✅               | 🟢🟢🟢              | 🟢                | zsh                      | 🟢                | 🟡            |
| [Spaceship](https://github.com/spaceship-prompt/spaceship-prompt) | ✅               | 🟢🟢                | 🟢🟢              | zsh                      | 🟢🟢🟢            | 🟡🟢          |

**Zusammenfassung:**

- **Starship**
  ([Webseite](https://starship.rs), [GitHub](https://github.com/starship/starship))
  Cross-Shell Prompt, modern, schnell, sehr anpassbar, zeigt Git-Status, Systeminfo und weitere Features.

- **Powerlevel10k**
  ([GitHub](https://github.com/romkatv/powerlevel10k))
  Hochgradig anpassbarer zsh-Prompt, sehr performant, attraktive Optik, Git-Integration, beliebt bei Power-Usern.

- **Pure**
  ([GitHub](https://github.com/sindresorhus/pure))
  Minimalistischer zsh-Prompt, schnell, schlicht, zeigt Git-Status, ideal für Nutzer, die wenig Schnickschnack wollen.

- **Spaceship**
  ([GitHub](https://github.com/spaceship-prompt/spaceship-prompt))
  Funktionsreicher zsh-Prompt, viele Module und Themes, Git-Integration, modern und optisch ansprechend.

**Nutzungsempfehlung nach Anwendungsfall:**

| Anwendungsfall                            | Empfohlener Prompt |
| ----------------------------------------- | ------------------ |
| **Cross-Shell, moderne Features**         | Starship           |
| **Git-Status & maximale Optik (zsh)**     | Powerlevel10k      |
| **Minimalistisch & schnell**              | Pure               |
| **Viele Features & ansprechendes Design** | Spaceship          |

## 6    Installation von Oh My Zsh und Starship

### 6.1    Oh My Zsh

**Installation:**

```zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

> [!NOTE]
> Durch die Installation wird die Datei `~/.zshrc` überschrieben! Zuvor wird jedoch ein Backup als `~/.zshrc.pre-oh-my-zsh` erstellt. Alle benutzerdefinierten Inhalte (Aliase, Funktionen, usw.) müssen manuell in die neue Datei kopiert werden.

**Anpassung:**

Über Konfigurationsdatei:

```
~/.zshrc
```

**Deinstallation:**

Mit dem folgenden Befehl wird Oh My Zsh gelöscht und die Einstellungen vom Zeitpunkt vor der Installation (:luc_arrow_right: ursprüngliche `~/.zshrc`) werden wieder hergestellt:

```zsh
uninstall_oh_my_zsh
```

Sie auch: [Uninstalling Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh#uninstalling-oh-my-zsh).

#### 6.1.1    Plugins für Oh My Zsh

**Vollständige Liste aller Plugins:**

https://github.com/ohmyzsh/ohmyzsh/wiki/plugins

**Besonders praktische Plugins:**

1. [zsh-autosuggestions, zsh-syntax-highlighting, zsh-fast-syntax-highlighting und zsh-autocomplete.md](https://gist.github.com/n1snt/454b879b8f0b7995740ae04c5fb5b7df)
   Verbessert die Effizienz beim Tippen im Terminal:

	- zsh-autosuggestions schlägt automatisch vorherige Befehle vor
	- zsh-syntax-highlighting und zsh-fast-syntax-highlighting heben die Eingabe farblich hervor
	- zsh-autocomplete ergänzt Befehle und Dateinamen in Echtzeit

```zsh
git clone https://github.com/zsh-users/zsh-autosuggestions.git $ZSH_CUSTOM/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
git clone https://github.com/zdharma-continuum/fast-syntax-highlighting.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/fast-syntax-highlighting
git clone --depth 1 -- https://github.com/marlonrichert/zsh-autocomplete.git $ZSH_CUSTOM/plugins/zsh-autocomplete
```

2. [sudo](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/sudo)
   Schreibt `sudo` vor den aktuellen und vorherigen Befehl durch zweimalige Drücken der `ESC`-Taste.

3. [web-search](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/web-search)
   Starte eine Websuche vom Terminal aus, z. B. mit dem Befehl `google`.

4. [copyfile](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/copyfile)
   Kopiert den Inhalt einer Datei in die Zwischenablage.

5. [copybuffer](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/copybuffer)
   Mit `STRG+O` wird der aktuelle Text/Befehl in die Zwischenablage kopiert.

6. [dirhistory](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/dirhistory)
   Es werden Tastaturkurzbefehle hinzugefügt um durch Ordner zu navigieren.

7. [jsontools](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/jsontools)
   Leserliche Darstellung von JSON-Daten.

### 6.2    Starship

**Installation:**

```zsh
brew install starship
```

**Anpassung:**

Über Konfigurationsdatei:

```
~/.config/starship.toml
```

Eine Erklärung, wie sich die aktuelle Prompt zusammensetzt liefert der folgende Befehl:

```zsh
starship explain
```

![[../_bilder/Terminal-Referenz für macOS - Teil 4 - Terminals, Shells & Erweiterungen-1.png]]

**Deinstallation:**

```zsh
brew uninstall starship
```

Siehe auch: [How do I uninstall Starship?](https://starship.rs/faq/#how-do-i-uninstall-starship)

> [!TIP]
> Für maximale Anpassungsmöglichkeiten empiehlt sich die Verwendung einer [Nerd Font](https://www.nerdfonts.com), welche zusätzliche Symbole liefert.
