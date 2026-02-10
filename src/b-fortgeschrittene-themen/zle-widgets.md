# ZLE / ZLE-Widgets

Der Zsh Line Editor (ZLE) ist das Herzstück der Kommandozeilen-Bearbeitung in zsh. Er ermöglicht umfangreiche Anpassungen der Eingabe, eigene Tastenkürzel und mächtige Widgets.

## 1    Grundlagen & Architektur

### 1.1    Was ist ZLE?

ZLE ist der eingebaute Zeileneditor von zsh, vergleichbar mit readline in bash. Er verarbeitet alle Tastatureingaben und stellt Funktionen bereit für:

- Cursor-Bewegung und Textbearbeitung
- History-Navigation
- Tab-Completion
- Vi- und Emacs-Modus
- Benutzerdefinierte Widgets

### 1.2    Keymaps

ZLE organisiert Tastenbelegungen in **Keymaps**:

| Keymap | Beschreibung |
| ------ | ------------ |
| `emacs` | Emacs-Modus (Standard) |
| `viins` | Vi-Insert-Modus |
| `vicmd` | Vi-Command-Modus |
| `visual` | Vi-Visual-Modus |
| `isearch` | Inkrementelle Suche |
| `command` | Befehlseingabe (nach `M-x`) |
| `main` | Alias für aktive Keymap |

**Aktuelle Keymap anzeigen:**

```zsh
bindkey -l          # Alle Keymaps auflisten
bindkey -M emacs    # Bindungen der emacs-Keymap
bindkey -M viins    # Bindungen des vi-Insert-Modus
```

**Keymap wechseln:**

```zsh
# Emacs-Modus (Standard)
bindkey -e

# Vi-Modus
bindkey -v
```

### 1.3    Widgets

Ein **Widget** ist eine Funktion, die an eine Taste gebunden werden kann. ZLE stellt über 150 eingebaute Widgets bereit.

**Eingebaute Widgets anzeigen:**

```zsh
zle -la             # Alle Widgets auflisten
zle -la | wc -l     # Anzahl
zle -la | grep hist # Widgets mit "hist" im Namen
```

**Wichtige eingebaute Widgets:**

| Widget | Beschreibung |
| ------ | ------------ |
| `accept-line` | Zeile ausführen (Enter) |
| `backward-char` | Cursor links |
| `forward-char` | Cursor rechts |
| `backward-word` | Wort zurück |
| `forward-word` | Wort vor |
| `beginning-of-line` | Zeilenanfang |
| `end-of-line` | Zeilenende |
| `backward-delete-char` | Backspace |
| `delete-char` | Delete |
| `kill-line` | Bis Zeilenende löschen |
| `backward-kill-line` | Bis Zeilenanfang löschen |
| `kill-word` | Wort löschen |
| `yank` | Einfügen (aus Kill-Ring) |
| `undo` | Rückgängig |
| `redo` | Wiederholen |
| `clear-screen` | Bildschirm löschen |
| `history-search-backward` | History rückwärts suchen |
| `history-search-forward` | History vorwärts suchen |
| `expand-or-complete` | Tab-Completion |
| `self-insert` | Zeichen einfügen |

### 1.4    Aktuelle Bindungen anzeigen

```zsh
# Alle Bindungen
bindkey

# Bestimmte Taste
bindkey "^A"        # Was macht Ctrl+A?
bindkey "^[[A"      # Was macht Pfeil-Hoch?

# Widget suchen
bindkey | grep kill
```

### 1.5    Escape-Sequenzen

Tasten werden als Escape-Sequenzen dargestellt:

| Notation | Bedeutung | Beispiel |
| -------- | --------- | -------- |
| `^X` | Ctrl+X | `^A` = Ctrl+A |
| `^[` | Escape / Alt | `^[b` = Alt+B |
| `\e` | Escape (alternativ) | `\eb` = Alt+B |
| `^[[` | CSI (Terminal-Sequenz) | `^[[A` = Pfeil hoch |
| `^[O` | SS3 (Terminal-Sequenz) | `^[OA` = Pfeil hoch (alt) |

**Terminal-Sequenzen ermitteln:**

```zsh
# Methode 1: cat
cat -v
# Dann Taste drücken, z.B. Pfeil hoch zeigt: ^[[A

# Methode 2: read
read -k
# Taste drücken

# Methode 3: Ctrl+V in zsh
# Ctrl+V drücken, dann Taste → zeigt Sequenz
```

**Häufige Terminal-Sequenzen:**

| Taste | Sequenz |
| ----- | ------- |
| Pfeil hoch | `^[[A` |
| Pfeil runter | `^[[B` |
| Pfeil rechts | `^[[C` |
| Pfeil links | `^[[D` |
| Home | `^[[H` oder `^[OH` |
| End | `^[[F` oder `^[OF` |
| Delete | `^[[3~` |
| Page Up | `^[[5~` |
| Page Down | `^[[6~` |
| F1–F12 | `^[OP` bis `^[[24~` |

### 1.6    ZLE-Variablen

Innerhalb eines Widgets verfügbare Variablen:

| Variable | Beschreibung |
| -------- | ------------ |
| `$BUFFER` | Gesamte Eingabezeile |
| `$LBUFFER` | Text links vom Cursor |
| `$RBUFFER` | Text rechts vom Cursor |
| `$CURSOR` | Cursor-Position (0-basiert) |
| `$WIDGET` | Name des aktuellen Widgets |
| `$LASTWIDGET` | Name des vorherigen Widgets |
| `$KEYS` | Gedrückte Tasten |
| `$KEYMAP` | Aktive Keymap |
| `$PREBUFFER` | Vorherige Zeilen (Multiline) |
| `$REGION_ACTIVE` | Ist Region aktiv? |
| `$MARK` | Position der Markierung |

## 2    Eigene Widgets schreiben

### 2.1    Widget-Grundstruktur

```zsh
# Widget-Funktion definieren
my-widget() {
    # Code hier
}

# Als Widget registrieren
zle -N my-widget

# An Taste binden
bindkey "^X^M" my-widget
```

### 2.2    Einfache Beispiele

**Text einfügen:**

```zsh
# Aktuelles Datum einfügen
insert-date() {
    LBUFFER+=$(date +%Y-%m-%d)
}
zle -N insert-date
bindkey "^Xd" insert-date

# Timestamp einfügen
insert-timestamp() {
    LBUFFER+=$(date +"%Y-%m-%d %H:%M:%S")
}
zle -N insert-timestamp
bindkey "^Xt" insert-timestamp
```

**Buffer manipulieren:**

```zsh
# Zeile in Großbuchstaben
uppercase-line() {
    BUFFER=${BUFFER:u}
}
zle -N uppercase-line
bindkey "^Xu" uppercase-line

# Zeile in Kleinbuchstaben
lowercase-line() {
    BUFFER=${BUFFER:l}
}
zle -N lowercase-line
bindkey "^Xl" lowercase-line

# Wort unter Cursor in Großbuchstaben
uppercase-word() {
    local word="${LBUFFER##* }"
    LBUFFER="${LBUFFER% *} ${word:u}"
}
zle -N uppercase-word
```

**Sudo voranstellen:**

```zsh
prepend-sudo() {
    if [[ $BUFFER != sudo\ * ]]; then
        BUFFER="sudo $BUFFER"
        CURSOR+=5
    fi
}
zle -N prepend-sudo
bindkey "^[s" prepend-sudo   # Alt+S
```

### 2.3    Mit History arbeiten

```zsh
# Letzten Befehl mit sudo wiederholen
sudo-last-command() {
    BUFFER="sudo $(fc -ln -1)"
    zle accept-line
}
zle -N sudo-last-command
bindkey "^[!" sudo-last-command

# Letztes Argument des vorherigen Befehls
insert-last-arg() {
    zle insert-last-word
}
zle -N insert-last-arg
bindkey "^[." insert-last-arg   # Alt+. (oft schon belegt)
```

### 2.4    Externe Befehle einbinden

```zsh
# fzf für History-Suche
fzf-history() {
    local selected
    selected=$(fc -ln 1 | fzf --tac --no-sort)
    if [[ -n "$selected" ]]; then
        BUFFER="$selected"
        CURSOR=$#BUFFER
    fi
    zle redisplay
}
zle -N fzf-history
bindkey "^R" fzf-history

# fzf für Dateiauswahl
fzf-file() {
    local file
    file=$(fzf)
    if [[ -n "$file" ]]; then
        LBUFFER+="${(q)file}"  # Quoted einfügen
    fi
    zle redisplay
}
zle -N fzf-file
bindkey "^Xf" fzf-file

# fzf für Verzeichniswechsel
fzf-cd() {
    local dir
    dir=$(find . -type d 2>/dev/null | fzf)
    if [[ -n "$dir" ]]; then
        cd "$dir"
        zle reset-prompt
    fi
}
zle -N fzf-cd
bindkey "^Xc" fzf-cd
```

### 2.5    Clipboard-Integration

```zsh
# Auswahl in Clipboard kopieren (macOS)
copy-to-clipboard() {
    echo -n "$BUFFER" | pbcopy
    zle -M "Copied to clipboard"
}
zle -N copy-to-clipboard
bindkey "^Xy" copy-to-clipboard

# Aus Clipboard einfügen
paste-from-clipboard() {
    LBUFFER+=$(pbpaste)
}
zle -N paste-from-clipboard
bindkey "^Xp" paste-from-clipboard

# Kill-Ring nach Clipboard
copy-kill-ring() {
    echo -n "$CUTBUFFER" | pbcopy
    zle -M "Kill buffer copied"
}
zle -N copy-kill-ring
```

### 2.6    Widgets mit Parametern

```zsh
# Widget das andere Widgets aufruft
surround-with-quotes() {
    BUFFER="\"$BUFFER\""
    CURSOR=$#BUFFER
}
zle -N surround-with-quotes
bindkey "^X\"" surround-with-quotes

# Flexibler: Wrapper-Funktion
surround-with() {
    local open="$1" close="$2"
    BUFFER="${open}${BUFFER}${close}"
    CURSOR=$#BUFFER
}

surround-parens()   { surround-with '(' ')'; }
surround-brackets() { surround-with '[' ']'; }
surround-braces()   { surround-with '{' '}'; }
surround-single()   { surround-with "'" "'"; }
surround-double()   { surround-with '"' '"'; }

zle -N surround-parens
zle -N surround-brackets
zle -N surround-braces
zle -N surround-single
zle -N surround-double

bindkey "^X(" surround-parens
bindkey "^X[" surround-brackets
bindkey "^X{" surround-braces
bindkey "^X'" surround-single
bindkey '^X"' surround-double
```

### 2.7    Rückgabewerte und Fehlerbehandlung

```zsh
# Widget mit Fehlermeldung
safe-rm() {
    if [[ -z "$BUFFER" ]]; then
        zle -M "Buffer ist leer"
        return 1
    fi

    if [[ "$BUFFER" == rm\ * ]]; then
        BUFFER="${BUFFER/rm /rm -i }"
        zle -M "rm → rm -i (interaktiv)"
    fi
}
zle -N safe-rm
bindkey "^Xr" safe-rm
```

## 3    Erweiterte Shortcuts / ZLE-Bindings

### 3.1    bindkey Syntax

```zsh
bindkey [optionen] "taste" widget
```

| Option | Beschreibung |
| ------ | ------------ |
| `-e` | Emacs-Keymap aktivieren |
| `-v` | Vi-Keymap aktivieren |
| `-M keymap` | Binding in bestimmter Keymap |
| `-r "taste"` | Binding entfernen |
| `-s "taste" "string"` | Makro (String einfügen) |
| `-l` | Keymaps auflisten |
| `-L` | Bindungen als bindkey-Befehle |

### 3.2    Tastenkombinationen definieren

```zsh
# Einzelne Taste
bindkey "^A" beginning-of-line

# Tastensequenz
bindkey "^X^F" fzf-file

# Mit Escape/Alt
bindkey "^[b" backward-word     # Alt+B
bindkey "\eb" backward-word     # Alternative Notation

# Pfeiltasten
bindkey "^[[A" history-search-backward
bindkey "^[[B" history-search-forward

# Funktionstasten
bindkey "^[OP" my-f1-widget     # F1
bindkey "^[[15~" my-f5-widget   # F5
```

### 3.3    Makros (String-Bindungen)

Mit `-s` wird ein String eingefügt statt ein Widget aufgerufen:

```zsh
# Pipe zu less
bindkey -s "^Xl" " | less"

# Pipe zu grep
bindkey -s "^Xg" " | grep "

# Redirect Stderr
bindkey -s "^Xe" " 2>&1"

# Redirect zu /dev/null
bindkey -s "^Xn" " > /dev/null 2>&1"

# Git Status
bindkey -s "^Gs" "git status\n"

# Git Diff
bindkey -s "^Gd" "git diff\n"

# Häufige Befehle
bindkey -s "^Ll" "ll\n"
bindkey -s "^Lc" "clear\n"
```

> [!TIP]
> `\n` führt den Befehl direkt aus.

### 3.4    Keymap-spezifische Bindungen

```zsh
# Nur im Vi-Insert-Modus
bindkey -M viins "jk" vi-cmd-mode

# Nur im Vi-Command-Modus
bindkey -M vicmd "H" beginning-of-line
bindkey -M vicmd "L" end-of-line

# Nur im Emacs-Modus
bindkey -M emacs "^Xe" edit-command-line
```

### 3.5    Bindung entfernen

```zsh
# Binding entfernen
bindkey -r "^X^K"

# In bestimmter Keymap
bindkey -M viins -r "^A"
```

### 3.6    Alle Bindungen exportieren/importieren

```zsh
# Aktuelle Bindungen als Befehle ausgeben
bindkey -L > ~/bindkeys-backup.zsh

# Wiederherstellen
source ~/bindkeys-backup.zsh
```

## 4    Erweiterte Tastenkombinationen

### 4.1    Emacs-Modus Referenz

**Navigation:**

| Kürzel | Widget | Beschreibung |
| ------ | ------ | ------------ |
| `Ctrl+A` | `beginning-of-line` | Zeilenanfang |
| `Ctrl+E` | `end-of-line` | Zeilenende |
| `Ctrl+F` | `forward-char` | Zeichen vor |
| `Ctrl+B` | `backward-char` | Zeichen zurück |
| `Alt+F` | `forward-word` | Wort vor |
| `Alt+B` | `backward-word` | Wort zurück |

**Bearbeitung:**

| Kürzel | Widget | Beschreibung |
| ------ | ------ | ------------ |
| `Ctrl+D` | `delete-char` | Zeichen löschen |
| `Ctrl+H` | `backward-delete-char` | Backspace |
| `Ctrl+W` | `backward-kill-word` | Wort rückwärts löschen |
| `Alt+D` | `kill-word` | Wort vorwärts löschen |
| `Ctrl+K` | `kill-line` | Bis Zeilenende löschen |
| `Ctrl+U` | `backward-kill-line` | Bis Zeilenanfang löschen |
| `Ctrl+Y` | `yank` | Einfügen |
| `Alt+Y` | `yank-pop` | Vorheriges aus Kill-Ring |
| `Ctrl+T` | `transpose-chars` | Zeichen tauschen |
| `Alt+T` | `transpose-words` | Wörter tauschen |
| `Alt+U` | `up-case-word` | Wort GROSS |
| `Alt+L` | `down-case-word` | Wort klein |
| `Alt+C` | `capitalize-word` | Wort Kapitalisieren |

**History:**

| Kürzel | Widget | Beschreibung |
| ------ | ------ | ------------ |
| `Ctrl+P` | `up-line-or-history` | Vorheriger Befehl |
| `Ctrl+N` | `down-line-or-history` | Nächster Befehl |
| `Ctrl+R` | `history-incremental-search-backward` | History suchen rückwärts |
| `Ctrl+S` | `history-incremental-search-forward` | History suchen vorwärts |
| `Alt+<` | `beginning-of-buffer-or-history` | Erste History |
| `Alt+>` | `end-of-buffer-or-history` | Letzte History |
| `Alt+.` | `insert-last-word` | Letztes Argument |

**Sonstiges:**

| Kürzel | Widget | Beschreibung |
| ------ | ------ | ------------ |
| `Ctrl+L` | `clear-screen` | Bildschirm löschen |
| `Ctrl+_` | `undo` | Rückgängig |
| `Ctrl+X Ctrl+U` | `undo` | Rückgängig (alternativ) |
| `Ctrl+X Ctrl+E` | `edit-command-line` | In Editor öffnen |
| `Tab` | `expand-or-complete` | Completion |
| `Ctrl+G` | `send-break` | Abbrechen |

### 4.2    Vi-Modus Referenz

**Modus wechseln:**

| Kürzel | Beschreibung |
| ------ | ------------ |
| `Escape` | Insert → Command |
| `i` | Insert vor Cursor |
| `a` | Insert nach Cursor |
| `A` | Insert am Zeilenende |
| `I` | Insert am Zeilenanfang |
| `o` | Neue Zeile darunter |
| `O` | Neue Zeile darüber |

**Navigation (Command-Modus):**

| Kürzel | Beschreibung |
| ------ | ------------ |
| `h` / `l` | Links / Rechts |
| `j` / `k` | History runter / hoch |
| `w` / `b` | Wort vor / zurück |
| `e` | Wortende |
| `0` / `$` | Zeilenanfang / -ende |
| `^` | Erstes Nicht-Whitespace |
| `f{char}` | Vorwärts zu Zeichen |
| `F{char}` | Rückwärts zu Zeichen |
| `t{char}` | Vor Zeichen |
| `T{char}` | Nach Zeichen (rückwärts) |

**Bearbeitung (Command-Modus):**

| Kürzel | Beschreibung |
| ------ | ------------ |
| `x` | Zeichen löschen |
| `X` | Zeichen davor löschen |
| `dd` | Ganze Zeile löschen |
| `D` | Bis Zeilenende löschen |
| `dw` | Wort löschen |
| `cw` | Wort ändern |
| `cc` | Zeile ändern |
| `C` | Bis Zeilenende ändern |
| `r{char}` | Zeichen ersetzen |
| `R` | Replace-Modus |
| `yy` | Zeile kopieren |
| `yw` | Wort kopieren |
| `p` / `P` | Einfügen nach/vor |
| `u` | Undo |
| `Ctrl+R` | Redo |

**History (Command-Modus):**

| Kürzel | Beschreibung |
| ------ | ------------ |
| `/pattern` | Vorwärts suchen |
| `?pattern` | Rückwärts suchen |
| `n` / `N` | Nächster / Vorheriger Treffer |

### 4.3    Eigene Keymap erstellen

```zsh
# Neue Keymap basierend auf emacs
bindkey -N mymap emacs

# Anpassungen
bindkey -M mymap "^Xd" insert-date
bindkey -M mymap "^Xs" prepend-sudo

# Keymap aktivieren
bindkey -A mymap main
```

### 4.4    Contextuelle Bindings

```zsh
# Binding nur bei bestimmter Eingabe
# Beispiel: Tab verhält sich unterschiedlich

smart-tab() {
    if [[ -z "$LBUFFER" ]]; then
        # Leere Zeile: 4 Spaces einfügen
        LBUFFER+="    "
    elif [[ "$LBUFFER" =~ '^ +$' ]]; then
        # Nur Whitespace: weitere Spaces
        LBUFFER+="    "
    else
        # Sonst: normale Completion
        zle expand-or-complete
    fi
}
zle -N smart-tab
bindkey "^I" smart-tab
```

## 5    Beispiele & Use-Cases

### 5.1    Produktivitäts-Widgets

**Befehl in Editor öffnen:**

```zsh
# Bereits eingebaut, aber oft nicht gebunden
autoload -U edit-command-line
zle -N edit-command-line
bindkey "^Xe" edit-command-line
bindkey "^X^E" edit-command-line
```

**Git-Integration:**

```zsh
# Git Branch wechseln mit fzf
fzf-git-branch() {
    local branch
    branch=$(git branch --all | grep -v HEAD | fzf --preview 'git log --oneline -20 {1}')
    branch=${branch#remotes/origin/}
    branch=${branch## }
    if [[ -n "$branch" ]]; then
        BUFFER="git checkout $branch"
        zle accept-line
    fi
}
zle -N fzf-git-branch
bindkey "^Gb" fzf-git-branch

# Git Status anzeigen (ohne Ausführung)
show-git-status() {
    zle -M "$(git status -s 2>/dev/null || echo 'Not a git repo')"
}
zle -N show-git-status
bindkey "^Gs" show-git-status

# Git Add mit fzf
fzf-git-add() {
    local files
    files=$(git status -s | fzf -m | awk '{print $2}')
    if [[ -n "$files" ]]; then
        BUFFER="git add ${files//$'\n'/ }"
        CURSOR=$#BUFFER
    fi
    zle redisplay
}
zle -N fzf-git-add
bindkey "^Ga" fzf-git-add
```

**Verzeichnis-Navigation:**

```zsh
# Schnell zu häufigen Verzeichnissen
goto-projects() {
    local dir
    dir=$(find ~/projects -maxdepth 1 -type d | fzf)
    if [[ -n "$dir" ]]; then
        cd "$dir"
        zle reset-prompt
    fi
}
zle -N goto-projects
bindkey "^Xp" goto-projects

# Parent Directory
goto-parent() {
    cd ..
    zle reset-prompt
}
zle -N goto-parent
bindkey "^X." goto-parent

# Zurück zum vorherigen Verzeichnis
goto-previous() {
    cd - > /dev/null
    zle reset-prompt
}
zle -N goto-previous
bindkey "^X-" goto-previous
```

### 5.2    Textmanipulation

**Pipe-Operatoren schnell einfügen:**

```zsh
# Pipe-Menü
pipe-menu() {
    local pipes=(
        "| less"
        "| grep "
        "| head -20"
        "| tail -20"
        "| wc -l"
        "| sort"
        "| sort -u"
        "| xargs "
        "| awk '{print \$1}'"
        "| sed 's///g'"
        "> /dev/null 2>&1"
        "2>&1"
    )
    local selected
    selected=$(printf '%s\n' "${pipes[@]}" | fzf --prompt="Pipe: ")
    if [[ -n "$selected" ]]; then
        LBUFFER+=" $selected"
    fi
    zle redisplay
}
zle -N pipe-menu
bindkey "^X|" pipe-menu
```

**Pfad expandieren:**

```zsh
# ~ und Variablen expandieren
expand-path() {
    BUFFER="${(e)BUFFER}"
}
zle -N expand-path
bindkey "^Xx" expand-path

# Aktuelles Wort zu absolutem Pfad
expand-to-absolute() {
    local word="${LBUFFER##* }"
    local rest="${LBUFFER% *}"
    local abs="${word:A}"
    if [[ "$rest" == "$LBUFFER" ]]; then
        LBUFFER="$abs"
    else
        LBUFFER="$rest $abs"
    fi
}
zle -N expand-to-absolute
bindkey "^Xa" expand-to-absolute
```

**Wort-Operationen:**

```zsh
# Wort duplizieren
duplicate-word() {
    local word="${LBUFFER##* }"
    LBUFFER+=" $word"
}
zle -N duplicate-word
bindkey "^Xw" duplicate-word

# Wörter rückwärts
reverse-words() {
    local words=(${=BUFFER})
    BUFFER="${(j: :)${(Oa)words}}"
}
zle -N reverse-words
```

### 5.3    History-Erweiterungen

**History mit Preview:**

```zsh
fzf-history-widget() {
    local selected
    selected=$(fc -rl 1 | fzf +s --tac \
        --preview 'echo {}' \
        --preview-window down:3:wrap)
    if [[ -n "$selected" ]]; then
        # Nummer entfernen
        BUFFER="${selected#*  }"
        CURSOR=$#BUFFER
    fi
    zle redisplay
}
zle -N fzf-history-widget
bindkey "^R" fzf-history-widget
```

**Letzten Befehl modifizieren:**

```zsh
# Letzten Befehl holen und editierbar machen
recall-last-command() {
    BUFFER=$(fc -ln -1)
    CURSOR=$#BUFFER
}
zle -N recall-last-command
bindkey "^[r" recall-last-command
```

### 5.4    Prompt-Integration

```zsh
# Prompt neu zeichnen nach Widget
update-prompt() {
    # Prompt-relevante Variablen aktualisieren
    zle reset-prompt
}

# Nach cd immer Prompt aktualisieren
cd-and-update() {
    if [[ -n "$BUFFER" ]]; then
        zle accept-line
        zle reset-prompt
    fi
}
```

### 5.5    Debugging-Widgets

```zsh
# Aktuellen Buffer anzeigen
debug-buffer() {
    zle -M "BUFFER: '$BUFFER' | LBUFFER: '$LBUFFER' | CURSOR: $CURSOR"
}
zle -N debug-buffer
bindkey "^Xb" debug-buffer

# Letzte Tasten anzeigen
debug-keys() {
    zle -M "KEYS: '$KEYS' | WIDGET: '$WIDGET' | LASTWIDGET: '$LASTWIDGET'"
}
zle -N debug-keys
bindkey "^Xk" debug-keys

# Widget-Liste durchsuchen
search-widgets() {
    local widget
    widget=$(zle -la | fzf --prompt="Widget: ")
    if [[ -n "$widget" ]]; then
        zle -M "Widget: $widget"
    fi
}
zle -N search-widgets
bindkey "^X?" search-widgets
```

### 5.6    Vollständige .zshrc-Integration

```zsh
# ~/.zshrc - ZLE-Konfiguration

# === Grundeinstellungen ===
bindkey -e                              # Emacs-Modus

# === Standard-Verbesserungen ===
bindkey "^[[A" history-search-backward  # Pfeil hoch: History mit Prefix
bindkey "^[[B" history-search-forward   # Pfeil runter: History mit Prefix
bindkey "^[[H" beginning-of-line        # Home
bindkey "^[[F" end-of-line              # End
bindkey "^[[3~" delete-char             # Delete

# === Editor ===
autoload -U edit-command-line
zle -N edit-command-line
bindkey "^Xe" edit-command-line
bindkey "^X^E" edit-command-line

# === Eigene Widgets ===

# Sudo voranstellen
prepend-sudo() {
    [[ $BUFFER != sudo\ * ]] && BUFFER="sudo $BUFFER" && ((CURSOR+=5))
}
zle -N prepend-sudo
bindkey "^[s" prepend-sudo

# Datum einfügen
insert-date() { LBUFFER+=$(date +%Y-%m-%d); }
zle -N insert-date
bindkey "^Xd" insert-date

# Clipboard (macOS)
copy-buffer() { echo -n "$BUFFER" | pbcopy; zle -M "Copied"; }
paste-buffer() { LBUFFER+=$(pbpaste); }
zle -N copy-buffer
zle -N paste-buffer
bindkey "^Xy" copy-buffer
bindkey "^Xp" paste-buffer

# === Makros ===
bindkey -s "^Xl" " | less"
bindkey -s "^Xg" " | grep "
bindkey -s "^Xn" " > /dev/null 2>&1"

# === fzf-Integration (falls installiert) ===
if command -v fzf &> /dev/null; then
    # History
    fzf-history() {
        local cmd=$(fc -ln 1 | fzf --tac --no-sort)
        [[ -n "$cmd" ]] && BUFFER="$cmd" && CURSOR=$#BUFFER
        zle redisplay
    }
    zle -N fzf-history
    bindkey "^R" fzf-history

    # Dateien
    fzf-file() {
        local file=$(fzf)
        [[ -n "$file" ]] && LBUFFER+="${(q)file}"
        zle redisplay
    }
    zle -N fzf-file
    bindkey "^Xf" fzf-file

    # Verzeichnisse
    fzf-cd() {
        local dir=$(find . -type d 2>/dev/null | fzf)
        [[ -n "$dir" ]] && cd "$dir" && zle reset-prompt
    }
    zle -N fzf-cd
    bindkey "^Xc" fzf-cd
fi
```

### 5.7    Cheatsheet

**Widget erstellen:**

```zsh
widget-name() { ... }           # Funktion definieren
zle -N widget-name              # Als Widget registrieren
bindkey "^Xk" widget-name       # An Taste binden
```

**Wichtige Variablen:**

```zsh
$BUFFER                         # Gesamte Zeile
$LBUFFER                        # Links vom Cursor
$RBUFFER                        # Rechts vom Cursor
$CURSOR                         # Cursor-Position
```

**Bindkey:**

```zsh
bindkey                         # Alle Bindungen
bindkey "^X"                    # Was macht Ctrl+X?
bindkey "^Xk" widget            # Widget binden
bindkey -s "^Xk" "string"       # Makro binden
bindkey -r "^Xk"                # Binding entfernen
bindkey -M viins "jk" vi-cmd    # Keymap-spezifisch
```

**Escape-Sequenzen:**

```
^X   = Ctrl+X
^[   = Escape / Alt
^[[A = Pfeil hoch
^[[B = Pfeil runter
\n   = Enter (für Makros)
```
