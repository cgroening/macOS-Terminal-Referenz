# Aliase, Pipes, Weiterleitungen und Verkettungen

## 1    Aliase

Ein Alias ist ein benutzerdefinierter Kurzbefehl. Er ersetzt längere und/oder häufig genutzt Terminal befehle durch ein Kürzel.

### 1.1    Typische Aliase (Beispiele)

```zsh
alias ll="ls -lah"
alias la="ls -A"
alias gs="git status"
alias gp="git pull"
alias v="nvim"
alias ..="cd .."
alias ...="cd ../.."
```

Es sind auch dynamische Aliase möglich:

```zsh
alias now='date "+%Y-%m-%d %H:%M:%S"'
alias weather='curl wttr.in'
```

### 1.2    Dauerhafte Speicherung von Aliasen

Damit die Aliase bei jedem Start verfügbar sind, müssen sie in die `~/.zshrc` eingefügt werden (siehe auch Abschnitt "[[../SUMMARY#2.5 Die `.zshrc`-Datei|Die `.zshrc`-Datei]]").

Öffnen von `~/.zshrc`:

```zsh
nano ~/.zshrc
```

Anschließend zum Beispiel:

```zsh
# Eigene Aliase
alias editrc="nano ~/.zshrc"
alias reloadrc="source ~/.zshrc"
alias openfl="open -a ForkLift ."

```

Neuladen:

```zsh
source ~/.zshrc
```

## 2    Pipes (`|`) – Kombination von Kommandos

Mit dem Pipe-Zeichen (`|`) wird die Ausgabe eines Befehls als Eingabe für den nächsten Befehl weitergeleitet. Auf diese Weise kann man kleine Werkzeuge zu sehr mächtigen Befehlen kombinieren.

### 2.1    Beispiele

**Alle Dateien auflisten und nach Dateiendung `.txt` filtern:**

```zsh
ls | grep txt
```

* `ls`: listet alle Dateien und Ordner im aktuellen Verzeichnis auf
* `grep txt`: filtert die Ausgabe und zeigt nur Zeilen an, die „txt“ enthalten (d. h. `.txt`-Dateien)

**Prozesse anzeigen, die "Safari" enthalten:**

```zsh
ps aux | grep Safari
```

* `ps aux`: zeigt alle laufenden Prozesse im System mit Benutzer, PID, CPU- und Speicherverbrauch
* `grep Safari`: filtert die Prozessliste und zeigt nur Zeilen, in denen "Safari" vorkommt

**Zählen, wie viele Zeilen im Log das Wort "ERROR" enthalten:**

```zsh
cat access.log | grep "ERROR" | wc -l
```

- `cat access.log`: gibt den gesamten Inhalt der Datei `access.log` aus
- `grep "ERROR"`: filtert alle Zeilen, die das Wort „ERROR“ enthalten
- `wc -l`: zählt die Anzahl der Zeilen der gefilterten Ausgabe (d. h. die Anzahl der Fehlermeldungen)

Pipes können beliebig verkettet werden. Somit sind sie in Kombination mit `grep`, `awk`, `sed`, `sort`, `uniq`, `cut` usw. sehr mächtig.

### 2.2    Relevante Befehle für Pipes

| **Befehl** | **Beschreibung**                                                                                | **Beispiel**                        |
| ---------- | ----------------------------------------------------------------------------------------------- | ----------------------------------- |
| grep       | Filtert Zeilen nach Textmustern; unterstützt reguläre Ausdrücke                                 | `dmesg \| grep error`               |
| awk        | Verarbeitet Textzeilen spaltenweise, ermöglicht Filter, Formatierungen und erlaubt Berechnungen | `ls -l \| awk '{print $9, $5}'`     |
| sed        | Stream-Editor zum Ersetzen und Bearbeiten von Text innerhalb eines Datenstroms                  | `cat file.txt \| sed 's/foo/bar/g'` |
| sort       | Sortiert Zeilen alphabetisch oder numerisch.                                                    | `cat names.txt \| sort`             |
| uniq       | Entfernt doppelte Zeilen (oft nach sort verwendet)                                              | `sort file.txt \| uniq`             |
| cut        | Schneidet Spalten aus Textzeilen heraus (z. B. bei CSV-Dateien).                                | `cat users.csv \| cut -d',' -f1`    |

#### 2.2.1    `grep`

Durchsucht Textzeilen nach Mustern oder Wörtern. Unterstützt reguläre Ausdrücke.

**Syntax:**

```zsh
grep [Optionen] Muster [Datei(en)]
```

**Häufige Optionen:**
- `-i`: Groß-/Kleinschreibung ignorieren
- `-r`: rekursiv durchsuchen
- `-n`: Zeilennummern anzeigen

**Beispiele:**

```zsh
grep "error" /var/log/system.log
ps aux | grep Safari
```

Siehe auch [[../SUMMARY#3 3 5 grep -Befehl|grep-Beispiele]].
#### 2.2.2    `awk`

Zeilen- und spaltenorientierter Textprozessor, ideal für Auswertungen oder Filterung.

**Syntax:**

```zsh
awk '{Aktion}' [Datei]
```

**Beispiele:**

```zsh
ls -l | awk '{print $9, $5}'      # Dateiname + Größe
df -h | awk '{print $1, $5}'      # Laufwerk + Auslastung
```

#### 2.2.3    `sed`

Stream-Editor zum automatischen Bearbeiten von Texten in Datenströmen.

**Syntax:**

```zsh
sed 's/ALT/NEU/g' [Datei]
```

**Häufige Optionen:**
- `-i`: Änderungen direkt in der Datei speichern
- `-n`: unterdrückt Standardausgabe

**Beispiele:**

```zsh
cat config.txt | sed 's/enabled=false/enabled=true/g'
sed -i '' 's/http:/https:/g' urls.txt
```

#### 2.2.4    `sort`

Sortiert Zeilen alphabetisch oder numerisch.

**Syntax:**

```zsh
sort [Optionen] [Datei]
```

**Syntax:**

- `-n`: numerisch sortieren
- `-r`: Reihenfolge umkehren

**Beispiele:**

```zsh
cat names.txt | sort
ls -l | sort -k5 -n
```

#### 2.2.5    `uniq`

Entfernt aufeinanderfolgende Duplikate aus sortierten Listen.

**Syntax:**

```zsh
uniq [Optionen] [Datei]
```

**Häufige Optionen:**

- `-c`: zählt Vorkommen
- `-d`: zeigt nur doppelte Einträge

**Beispiele:**

```zsh
sort names.txt | uniq
sort names.txt | uniq -c
```

#### 2.2.6    `cut`

Extrahiert Spalten aus Textzeilen – besonders nützlich bei CSV-Dateien.

**Syntax:**

```zsh
cut -d[Trennzeichen] -f[Feldnummer] [Datei]
```

**Häufige Optionen:**

- `-d`: legt das Trennzeichen fest (z. B. `,` oder `:`)
- `-f`: bestimmt die Spaltennummer(n)

**Beispiele:**

```zsh
cat users.csv | cut -d',' -f1     # erste Spalte (Name)
ps aux | cut -c1-10               # Zeichen 1–10 jeder Zeile
```

## 3    Weiterleitungen (`>`, `>>`, `<`)

Weiterleitungen bestimmen, wohin die Eingaben oder Ausgaben eines Befehls gehen.

### 3.1    Arten von Weiterleitungen

| Symbol | Bedeutung                                 | Beispiel                          |
| ------ | ----------------------------------------- | --------------------------------- |
| `>`    | Ausgabe in Datei schreiben (überschreibt) | `ls > dateien.txt`                |
| `>>`   | Ausgabe anhängen                          | `echo "neuer Eintrag" >> log.txt` |
| `<`    | Eingabe aus Datei lesen                   | `sort < unsortiert.txt`           |
| `2>`   | Fehlermeldungen in Datei schreiben        | `ls xyz 2> fehler.txt`            |
| `&>`   | Ausgabe **und** Fehler umleiten           | `script.sh &> output.txt`         |

### 3.2    Beispiele

**Die Namen aller JPEG-Dateien in eine Textdatei schreiben:**

```zsh
find . -name "*.jpg" > bilder.txt
```

**Gefundene Warnungen aus einem Log an eine Textdatei anhängen:**

```zsh
grep "WARN" log.txt >> warnungen.txt
```

**Aus `input.txt` lesen und in `output.txt` schreiben:**

```zsh
cat < input.txt > output.txt
```

**Stumme Ausführung eines Befehls (keine Ausgabe, d. h. keine sichtbare Fehlermeldung):**

```zsh
befehl &> /dev/null
```

## 4    Verkettung von Befehlen – AND (`&&`) und OR (`||`)

```zsh
mkdir ordner123 && cd ordner123  # Ordner erstellen und hineinwechseln
cd ordner123 || mkdir ordner123  # In Ordner wechseln oder erstellen, wenn er nicht existiert
```

