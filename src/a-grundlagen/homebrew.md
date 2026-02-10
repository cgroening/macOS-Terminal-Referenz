# Packetmanagement mit Homebrew

Das Software-Installationssystem [Homebrew](https://brew.sh/) bezeichnet sich selbst als "The Missing Package Manager for macOS (or Linux)" und ist unter macOS der inoffizielle Standard für die Installation von Software über das Terminal. Homebrew macht die Kommandozeile zu einer echten Linux-ähnlichen Umgebung. Es verwaltet Programme, Bibliotheken und Entwicklerwerkzeuge – alles über einfache Befehle.

Installation von Homebrew:

```zsh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Überprüfung, ob alles korrekt eingerichtet ist:

```zsh
brew doctor
```

**Wesentliche Vorteile von Homebrew:**

- Sehr schnelle Installation von Software (deutlich schneller als manuell)
- Ermöglicht ein konsistentes Setup über mehrere Rechner hinweg
- Hat eine riesige Community und Tausende Pakete

## 1    Grundlegende Befehle

| Befehl                      | Beschreibung                     |
| --------------------------- | -------------------------------- |
| `brew install <paket>`      | Installiert ein Paket            |
| `brew uninstall <paket>`    | Entfernt ein Paket               |
| `brew list`                 | Zeigt installierte Pakete        |
| `brew update`               | Aktualisiert Homebrew selbst     |
| `brew upgrade`              | Aktualisiert installierte Pakete |
| `brew search <suchbegriff>` | Sucht nach Paketen               |
| `brew info <paket>`         | Zeigt Infos, Version, Pfad etc.  |
| `brew cleanup`              | Löscht alte Versionen und Cache  |

GUI-Apps über Homebrew Cask:

```zsh
brew install --cask visual-studio-code
brew install --cask google-chrome
brew install --cask rectangle
```

`--cask` = für Anwendungen mit Benutzeroberfläche.

## 2    Paketpfade & Umgebungsvariablen

Homebrew installiert standardmäßig nach:

- `/usr/local` (bei Intel-Macs)
- `/opt/homebrew` (bei Apple Silicon)

Über eine Umgebungsvariable in der `~/.zshrc` kann man den Installationspfad anpassen:

```zsh
export PATH="/opt/homebrew/bin:$PATH"
```

## 3    Backup und Wiederherstellung

**Liste aller installierten Pakete erstellen:**

```zsh
brew bundle dump --file=~/brewfile.txt
```

Um die vorhandene Datei zu überschreiben:


```zsh
brew bundle dump --file=~/brewfile.txt --force
```

**Wiederherstellung der Installation:**

```zsh
brew bundle --file=~/brewfile.txt
```

## 4    Kombinationsbeispiel: Homebrew + Alias + Pipe

**Auf veraltete Pakete prüfen:**

```zsh
alias brewoutdated="brew outdated | grep -vE 'python|node'"
```

**Automatisches Aufräumen:**

```zsh
alias brewclean="brew cleanup && brew autoremove && brew doctor"
```
