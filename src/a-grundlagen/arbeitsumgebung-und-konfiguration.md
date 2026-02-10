# Arbeitsumgebung und Konfiguration

## 1    Der `which`-Befehl

Der Befehl `which` zeigt den vollständigen Pfad des Kommandos, das übergeben wird:

```zsh
which <befehlsname>
```

Gibt man einen Befehl im Terminal ein, sucht die Shell in allen Verzeichnissen der `PATH`-Variable nach einer Datei mit diesem Namen. `which` tut dies ebenfalls. Jedoch wird der Befehl nicht ausgeführt sondern sein Speicherort ausgegeben.

**Beispiel:**

```zsh
which brew
```

liefert:

```
/opt/homebrew/bin/brew
```

Wird nach einem Shell-Builtin (einem eingebauten Befehl, welcher kein separates Programm ist) wie beispielsweise `cd` oder `echo` gesucht, wird folgendes ausgegeben:

```
cd: shell built-in command
```

**Vergleich mit ähnlichen Befehlen:**

| Befehl         | Zweck                                                              |
| ------------------ | ---------------------------------------------------------------------- |
| `which <cmd>`      | Zeigt, wo auf dem Dateisystem ein ausführbares Programm gefunden wird. |
| `command -v <cmd>` | Zeigt, wie die Shell den Befehl auflöst (auch für Builtins/Aliase).    |
| `type <cmd>`       | Noch detailliertere Information über den Befehlstyp.                   |

## 2    Umgebungsvariablen

Anzeige einer Umgebungsvariable:

```zsh
echo $PATH
```

Erweiterung von `$PATH`:

```zsh
export PATH=$PATH:/usr/local/bin
```

### 2.1    Dauerhafte Speicherung

Nach einem Neustart sind zusätzlich hinzugefügte Pfade jedoch wieder weg. Sollen sie dauerhaft in der `PATH`-Variable bleiben, muss der `export`-Befehl in die `.zshrc`-Datei geschrieben werden (siehe auch Abschnitt "[[../SUMMARY#2.5 Die `.zshrc`-Datei|Die `.zshrc`-Datei]]").

Wird die `.zshrc`-Datei jedoch mehr neugeladen (`source ~/.zshrc`), werden zusätzliche Pfade erneut hinzugefügt und kommt somit mehrfach vor. Die kann zu Problemen führen. Dieses Verhalten vermeidet man, indem vorher geprüft wird, ob der Pfad schon vorhanden ist:

```zsh
# --- Custom PATH entries ------------------------------------------

# Define paths in array
CUSTOM_PATHS=(
	"/Applications/Visual Studio Code.app/Contents/Resources/app/bin"
	# "/add/additional/paths/here" (without comma; semicolon)
)

# Add all paths from CUSTOM_PATHS to PATH if it doesn't exist
for dir in "${CUSTOM_PATHS[@]}"; do
	if [[ -d "$dir" && ":$PATH:" != *":$dir:"* ]]; then
		export PATH="$PATH:$dir"
	fi
done

# ------------------------------------------------------------------
```

### 2.2    `~` vs. `$HOME`

| Symbol  | Bedeutung                                                    | Typ                                             | Beispielwert |
| ------- | ------------------------------------------------------------ | ----------------------------------------------- | ------------ |
| `~`     | Kürzel für das Home-Verzeichnis des aktuellen Benutzers      | Shell-Abkürzung (Shell Expansion)               | `/Users/max` |
| `$HOME` | Umgebungsvariable, die den Pfad zum Home-Verzeichnis enthält | Variable im Shell-Umfeld (Environment Variable) | `/Users/max` |

**`~` (Tilde):*

- Wird von der Shell (`zsh`, `bash` etc.) automatisch expandiert.
- Funktioniert nur an positionsabhängigen Stellen (z.B. nicht innerhalb von Anführungszeichen " ").
- Wird häufig für Navigation verwendet:

```zsh
cd ~
cd ~/Downloads
ls ~
```

Die Shell ersetzt `~` intern automatisch durch `/Users/<username>`.

Sonderformen:

```zsh
~user  # Home-Verzeichnis eines anderen Users (wenn zugreifbar)
~+     # aktuelles Verzeichnis
~-     # vorheriges Verzeichnis
```

**`$HOME`:**

- Eine Umgebungsvariable, die den kompletten Pfad enthält.
- Wird von Programmen, Skripten und Konfigurationsdateien benötigt.
- Funktioniert auch innerhalb von Strings und Skripten:

```zsh
echo $HOME
cd $HOME/Documents
export PATH="$PATH:$HOME/bin"
```

In vielen Programmen ist `$HOME` die bevorzugte Form, weil sie immer eindeutig ist, auch wenn die Shell keine Tilde-Ersetzung durchführt.

> [!NOTE]
> **Zusammenfassung:**
>
> - `~` = Komfortabler **Shell-Shortcut** für das persönliche Verzeichnis
> - `$HOME` = Variable, die das Home-Verzeichnis systemweit speichert
> - Funktional oft identisch, technisch jedoch unterschiedlicher Mechanismus

## 3    Die `.zshrc`-Datei

Die Datei `.zshrc` ist die zentrale Konfigurationsdatei der Z-Shell (`zsh`). Sie wird bei jedem Start eines neuen interaktiven Terminalfensters automatisch geladen und ausgeführt. Darin lassen sich Befehle, Einstellungen, Umgebungsvariablen und Aliase dauerhaft speichern.

### 3.1    Speicherort

Die Datei liegt standardmäßig im Home-Verzeichnis des Benutzers:

```
~/.zshrc
```

Sollte sie nicht existieren, kann sie wie folgt erstellt werden:

```zsh
touch ~/.zshrc
```

### 3.2    Zweck

In dieser Datei steht alles, was bei einem Terminalstart automatisch ausgeführt werden soll.

| Typ                              | Beispiel                              |
| -------------------------------- | ------------------------------------- |
| **Aliase**                       | `alias ll='ls -lah'`                    |
| **Umgebungsvariablen**           | `export PATH="/opt/homebrew/bin:$PATH"` |
| **Eigene Funktionen**            | `greet() { echo "Hallo $USER"; }`       |
| **Prompt-Anpassungen**           | `PROMPT='%n@%m %1~ %# '`                |
| **Themen / Plugins (oh-my-zsh)** | `source $ZSH/oh-my-zsh.sh`              |
| **Autostart von Tools**          | `eval "$(brew shellenv)"`               |

### 3.3    Änderungen übernehmen

Damit Änderungen wirksam werden, muss das Terminal neugestartet oder die Datei neugeladen werden.

**Neuladen von `~/.zshrc`:**

```zsh
source ~/.zshrc
```

**Nützliches Aliase zum Bearbeiten und Neuladen von `~/.zshrc`:**

```zsh
alias editrc="nano ~/.zshrc"
alias reloadrc="source ~/.zshrc"
```

### 3.4    Vergleich mit anderen Startdateien

| Datei             | Ladezeitpunkt                             | Beschreibung                              |
| ----------------- | ----------------------------------------- | ----------------------------------------- |
| **`~/.zshrc`**    | bei jedem neuen interaktiven Shell-Start  | Hauptdatei für `zsh`-Konfiguration        |
| **`~/.zprofile`** | nur Login-Shells                          | ähnlich wie `.bash_profile`, selten nötig |
| **`~/.zlogin`**   | am Ende einer Login-Shell                 | wird nach `.zprofile` geladen             |
| **`~/.zshenv`**   | immer, auch bei nicht-interaktiven Shells | für globale Umgebungsvariablen            |
| **`~/.zlogout`**  | beim Verlassen einer Login-Shell          | Aufräumarbeiten (z. B. Logs, Cache)       |

> [!NOTE]
> Die `.zshrc` ist das Herzstück der Shell-Konfiguration. Alles, was in jedem Terminal automatisch verfügbar sein soll – Aliase, `PATH`, Funktionen, Themes – gehört in diese Datei.


> [!TIP]
> **Verschieben der `.zshrc`**:
>
> Die `.zshrc` kann wie folgt verschoben und durch einen Symlink ersetzt werden (z. B. wenn sie teil eines Repositories werden soll):
>
> ```zsh
> mv ~/.zshrc ~/dotfiles/zshrc
> ln -s ~/dotfiles/zshrc ~/.zshrc
> ```
>
> Skript einbinden
>
> ```zsh
> . ~/.config/zsh/aliases.zsh
> ```
