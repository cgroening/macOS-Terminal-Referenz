# Shell-Scripting

## 1    Motivation & Abgrenzung: Shell-Scripting vs. Python

Auch wenn man Python beherrscht, lohnt es sich, Shell-Scripting zu lernen. Beide haben unterschiedliche Stärken und Einsatzzwecke und werden in der Praxis sehr häufig kombiniert, statt sich gegenseitig zu ersetzen.

**Grundlegende Unterschiede:**

| Thema                          | Shell-Scripting                                   | Python                                                |
| ------------------------------ | ------------------------------------------------- | ----------------------------------------------------- |
| **Zweck**                      | Automatisierung von System- und Terminalaufgaben  | Universelle Programmiersprache für komplexe Logik     |
| **Startzeit**                  | Extrem schnell, sofort verfügbar                  | Interpreter-Start kostet mehr Zeit                    |
| **Verfügbarkeit**              | Überall vorinstalliert (Unix, Linux, macOS)       | Nicht immer vorinstalliert (abhängig vom System)      |
| **Datei- und Systemsteuerung** | Sehr direkt, ideal für kleine Automatisierungen   | Gut, aber oft umfangreichere Syntax/Module nötig      |
| **Textverarbeitung**           | Überragend durch `sed`, `awk`, `grep`, Pipes      | Möglich, aber weniger kompakt                         |
| **Komplexe Programme**         | Schwer wartbar bei großer Logik                   | Optimale Wahl für komplexe Software                   |
| **Konfiguration / DevOps**     | Standard für Deployment, Cronjobs, Server-Skripte | Standard für Automatisierung, APIs, Datenverarbeitung |
## 2    was kann shell-scripting besser als python?

### 2.1    **1. Schnelle Systembefehle und Administrative Tasks:**

Beispiel: fünf Dateien verschieben und einen Dienst neu starten:

```zsh
mv *.log /backup/logs && systemctl restart apache2
```

In Python ist es deutlich umständlicher:

```python
import glob
import shutil
import subprocess

# Find all .log files
for file in glob.glob('*.log'):
    shutil.move(file, '/backup/logs')

# Restart service
result = subprocess.run(
    ['systemctl', 'restart', 'apache2'], capture_output=True, text=True
)

if result.returncode == 0:
    print('Service started.')
else:
    print('Error: ', result.stderr)
```

Hier kann man klar sehen, dass Shell-Scripting bei einfachen Systemaufgaben schneller und präziser ist, während Python mehr Kontrolle und Fehlerbehandlung ermöglicht, aber auch mehr Code erfordert.

#### 2.1.1    **2. Pipelines und Stream-Verarbeitung:**

Shell + Tools sind darauf optimiert, Streams zu verarbeiten, z. B.:

```zsh
ps aux | grep python | awk '{print $2}' | xargs kill
```

In Python müsste man Prozesse einlesen, parsen, filtern, Subprozesse starten …

#### 2.1.2    **3. Automatisierung in Systemstart, Cron, Deployment:**

- cronjobs
- `launchd`
- `systemd`
- Installationsskripte (`install.sh` sind Standard)

#### 2.1.3    **4. Universelle Verfügbarkeit**

Shell steht auf jedem Server bereit. Python nicht immer.

Shell-Scripting ist

- essentiell für DevOps, Sysadmins, Server & Deployment;
- perfekt für kleine Tools, Setup-Skripte und wiederkehrende Aufgaben;
- unverzichtbar für CI/CD & Cloud und
- enorm zeitsparend bei Routine-Aufgaben.

### 2.2    Was kann Python besser als Shell?

Python ist ideal für:

- komplexe Logik & Algorithmen
- APIs & Webservices
- Datenanalyse & Machine Learning
- GUI-Tools
- saubere Tests & Wartbarkeit

**Beispiel: JSON-Parsing in Python:**

```python
import json
data = json.loads(json_string)
```

In Shell wäre das schwierig und fehleranfälliger. Es ist `jq` erforderlich (Kommandozeilenwerkzeug zur Verarbeitung von JSON-Daten).

### 2.3    Warum beides kombinieren?

Man muss sich nicht entscheiden: Häufig wird beides verwendet ⇒ Best-of-Both-Worlds.

**Python aus einem Shell-Script heraus aufrufen:**

```python
#!/bin/zsh
echo "Starte Datenverarbeitung..."
python3 analyse.py input.csv
echo "Fertig!"
```

**Shell aus Python heraus verwenden:**

```python
import subprocess
subprocess.run(["ls", "-la"])
```

**Python als Tool + Shell als Wrapper (häufigste Variante):**

```python
#!/bin/zsh
INPUT=$1
python3 process.py "$INPUT" | tee results.txt
```

- Shell übernimmt Dateihandling und Prozesslogik
- Python rechnet, verarbeitet, analysiert

### 2.4    Zusammenfassung

Es ist wie ein Schraubendrehen vs. Akkuschrauber:

- Beides sind Werkzeuge.
- Shell = leicht, schnell, überall verfügbar
- Python = leistungsstark, modular, bei großen Aufgaben überlegen

**Empfehlung:**

- Shell für kleine Automatisierungen (10–50 Zeilen)
- Python für komplexe Logik, APIs, Datenverarbeitung, ML
- Beides kombinieren für professionelle DevOps-Workflows

## 3    Grundlagen des Shell-Scripting

Ein Shell-Skript ist eine Textdatei mit einer Folge von Befehlen, die nacheinander ausgeführt werden. Skripte ermöglichen die Automatisierung wiederkehrender Aufgaben und können beliebig komplex werden.

**Minimales Beispiel:**

```zsh
#!/bin/zsh
echo "Hallo Welt"
```

### 3.1    Aliase vs. Funktionen

Sowohl Aliase als auch Funktionen dienen dazu, häufig verwendete Befehle abzukürzen. Sie unterscheiden sich jedoch in ihren Möglichkeiten.

**Aliase:**

Ein Alias ist eine einfache Textersetzung. Er eignet sich für kurze, statische Befehlsketten.

```zsh
# Definition
alias ll="ls -lah"
alias gs="git status"
alias ..="cd .."

# Verwendung
ll
gs
..
```

**Einschränkungen von Aliasen:**

- Keine Argumente an beliebiger Stelle möglich
- Keine Kontrollstrukturen (`if`, `for`, `while`)
- Keine lokalen Variablen

```zsh
# Das funktioniert NICHT wie erwartet:
alias greet="echo Hallo $1"  # $1 wird sofort ausgewertet, nicht beim Aufruf
```

**Funktionen:**

Funktionen sind flexibler und ermöglichen komplexere Logik mit Argumenten, Variablen und Kontrollstrukturen.

```zsh
# Definition
greet() {
    echo "Hallo $1!"
}

# Verwendung
greet "Welt"    # Ausgabe: Hallo Welt!
greet "Max"     # Ausgabe: Hallo Max!
```

**Beispiel mit mehreren Argumenten:**

```zsh
mkcd() {
    mkdir -p "$1" && cd "$1"
}

# Verwendung
mkcd neuer_ordner  # Erstellt Ordner und wechselt hinein
```

**Beispiel mit Kontrollstruktur:**

```zsh
backup() {
    if [[ -z "$1" ]]; then
        echo "Fehler: Kein Dateiname angegeben"
        return 1
    fi
    cp "$1" "$1.bak"
    echo "Backup erstellt: $1.bak"
}
```

**Vergleich:**

| Merkmal | Alias | Funktion |
| ------- | ----- | -------- |
| Argumente verarbeiten | ❌ Nein | ✅ Ja (`$1`, `$2`, …) |
| Kontrollstrukturen | ❌ Nein | ✅ Ja |
| Lokale Variablen | ❌ Nein | ✅ Ja (`local`) |
| Mehrzeilige Logik | ❌ Nein | ✅ Ja |
| Rückgabewerte | ❌ Nein | ✅ Ja (`return`) |
| Einfache Ersetzung | ✅ Ideal | Überdimensioniert |

**Faustregel:**

- **Alias:** Für einfache Abkürzungen ohne Argumente
- **Funktion:** Sobald Argumente, Logik oder Variablen benötigt werden

**Speicherort:**

Beide werden typischerweise in `~/.zshrc` definiert. Bei vielen Aliasen und Funktionen empfiehlt sich eine Auslagerung:

```zsh
# In ~/.zshrc
source ~/.config/zsh/aliases.zsh
source ~/.config/zsh/functions.zsh
```

Einbindung in `.zshrc`:

```zsh
SCRIPT_DIR="/parent/folder/of/zshrc"
source "$SCRIPT_DIR/aliases.zsh"
source "$SCRIPT_DIR/functions.zsh"
```

## 4    Variablen

Variablen speichern Werte, die im Skript wiederverwendet werden können.

### 4.1    Variablenzuweisung

> [!WARNING]
> Bei der Zuweisung von Werten, dürfen sich keine Leerzeichen um das `=` befinden!

```zsh
# Richtig
name="Max"
zahl=42
pfad="/Users/max/Documents"

# FALSCH – führt zu Fehlern
name = "Max"    # Fehler: "name" wird als Befehl interpretiert
```

**Variablen verwenden:**

```zsh
name="Max"
echo "Hallo $name"           # Ausgabe: Hallo Max
echo "Hallo ${name}!"        # Ausgabe: Hallo Max!
echo "Pfad: ${name}_backup"  # Geschweifte Klammern bei Verkettung
```

**Befehlsausgabe in Variable speichern:**

```zsh
# Moderne Syntax (empfohlen)
datum=$(date +%Y-%m-%d)
dateien=$(ls -1 | wc -l)

# Alte Syntax (Backticks) – funktioniert, aber weniger lesbar
datum=`date +%Y-%m-%d`

echo "Heute ist $datum"
echo "Anzahl Dateien: $dateien"
```

**Rechnen mit Variablen:**

```zsh
a=5
b=3

# Arithmetische Auswertung
summe=$((a + b))
produkt=$((a * b))

echo "Summe: $summe"       # Ausgabe: Summe: 8
echo "Produkt: $produkt"   # Ausgabe: Produkt: 15
```

### 4.2    Environment-Variablen vs. lokale Variablen

**Lokale Variablen:**

Lokale Variablen existieren nur in der aktuellen Shell oder Funktion. Sie werden nicht an Kindprozesse vererbt.

```zsh
meine_var="lokal"
echo $meine_var  # Funktioniert

# In einem Subprozess:
bash -c 'echo $meine_var'  # Ausgabe: (leer)
```

**Environment-Variablen (exportiert):**

Mit `export` wird eine Variable zur Environment-Variable. Sie wird an alle Kindprozesse vererbt.

```zsh
export MEINE_VAR="global"
echo $MEINE_VAR  # Funktioniert

# In einem Subprozess:
bash -c 'echo $MEINE_VAR'  # Ausgabe: global
```

**Wichtige System-Environment-Variablen:**

| Variable | Beschreibung |
| -------- | ------------ |
| `$HOME` | Home-Verzeichnis des Benutzers |
| `$PATH` | Suchpfade für ausführbare Programme |
| `$USER` | Aktueller Benutzername |
| `$SHELL` | Pfad zur aktuellen Shell |
| `$PWD` | Aktuelles Arbeitsverzeichnis |
| `$OLDPWD` | Vorheriges Verzeichnis |
| `$EDITOR` | Standard-Texteditor |
| `$LANG` | Spracheinstellung |

**Alle Environment-Variablen anzeigen:**

```zsh
env
printenv
```

**Lokale Variablen in Funktionen:**

Mit `local` wird eine Variable auf die Funktion beschränkt:

```zsh
test_funktion() {
    local lokale_var="nur hier sichtbar"
    globale_var="überall sichtbar"
    echo "In Funktion: $lokale_var"
}

test_funktion
echo "Außerhalb: $lokale_var"   # Ausgabe: (leer)
echo "Außerhalb: $globale_var"  # Ausgabe: überall sichtbar
```

## 5    Strings

### 5.1    Strings über mehrere Zeilen

**Methode 1: Here-Document (heredoc):**

```zsh
cat << EOF
Dies ist ein mehrzeiliger Text.
Er kann Variablen enthalten: $USER
Und mehrere Zeilen umfassen.
EOF
```

**In Variable speichern:**

```zsh
text=$(cat << EOF
Zeile 1
Zeile 2
Zeile 3
EOF
)

echo "$text"
```

**Ohne Variablenexpansion (Anführungszeichen um EOF):**

```zsh
cat << 'EOF'
Hier wird $USER NICHT ersetzt.
Alles bleibt literal.
EOF
```

**Methode 2: Anführungszeichen:**

```zsh
text="Zeile 1
Zeile 2
Zeile 3"

echo "$text"
```

**Methode 3: String-Verkettung:**

```zsh
text="Zeile 1\n"
text+="Zeile 2\n"
text+="Zeile 3"

echo -e "$text"  # -e interpretiert \n als Zeilenumbruch
```

### 5.2    String-Interpolation

**Einfache Anführungszeichen vs. doppelte Anführungszeichen:**

```zsh
name="Max"

# Doppelte Anführungszeichen: Variablen werden ersetzt
echo "Hallo $name"    # Ausgabe: Hallo Max

# Einfache Anführungszeichen: Alles literal
echo 'Hallo $name'    # Ausgabe: Hallo $name
```

**Geschweifte Klammern für Eindeutigkeit:**

```zsh
frucht="Apfel"

echo "$fruchtmus"     # Fehler: Variable $fruchtmus existiert nicht
echo "${frucht}mus"   # Korrekt: Apfelmus
```

**Befehlssubstitution im String:**

```zsh
echo "Heute ist $(date +%A)"           # Ausgabe: Heute ist Montag
echo "Es gibt $(ls | wc -l) Dateien"   # Ausgabe: Es gibt 42 Dateien
```

**Arithmetik im String:**

```zsh
echo "5 + 3 = $((5 + 3))"  # Ausgabe: 5 + 3 = 8
```

**String-Manipulationen in zsh:**

```zsh
text="Hallo Welt"

# Länge
echo ${#text}              # Ausgabe: 10

# Substring (0-basiert)
echo ${text:0:5}           # Ausgabe: Hallo
echo ${text:6}             # Ausgabe: Welt

# Ersetzen
echo ${text/Welt/Max}      # Ausgabe: Hallo Max
echo ${text//l/L}          # Alle ersetzen: HaLLo WeLt

# Groß-/Kleinschreibung (zsh)
echo ${text:u}             # Ausgabe: HALLO WELT
echo ${text:l}             # Ausgabe: hallo welt
```

## 6    Argumente verarbeiten

Skripte und Funktionen können Argumente entgegennehmen, die beim Aufruf übergeben werden.

### 6.1    Zugriff über `$1`, `$2` …

Die übergebenen Argumente sind über nummerierte Variablen zugänglich:

| Variable | Bedeutung |
| -------- | --------- |
| `$0` | Name des Skripts |
| `$1` | Erstes Argument |
| `$2` | Zweites Argument |
| `$3` … `$9` | Weitere Argumente |
| `${10}` | Ab 10: Geschweifte Klammern nötig |
| `$#` | Anzahl der Argumente |

**Beispiel:**

```zsh
#!/bin/zsh
# Datei: greet.sh

echo "Skriptname: $0"
echo "Erstes Argument: $1"
echo "Zweites Argument: $2"
echo "Anzahl Argumente: $#"
```

**Aufruf:**

```zsh
./greet.sh Hallo Welt
# Ausgabe:
# Skriptname: ./greet.sh
# Erstes Argument: Hallo
# Zweites Argument: Welt
# Anzahl Argumente: 2
```

**Prüfen ob Argument vorhanden:**

```zsh
#!/bin/zsh

if [[ -z "$1" ]]; then
    echo "Fehler: Kein Argument angegeben"
    echo "Verwendung: $0 <name>"
    exit 1
fi

echo "Hallo $1!"
```

### 6.2    `$@` und `$*`

Beide repräsentieren alle übergebenen Argumente, verhalten sich aber unterschiedlich:

**`$@` – Jedes Argument separat (empfohlen):**

```zsh
#!/bin/zsh
# Datei: args.sh

echo "Mit \$@:"
for arg in "$@"; do
    echo "  Argument: '$arg'"
done
```

**Aufruf:**

```zsh
./args.sh "Hallo Welt" foo bar
# Ausgabe:
#   Argument: 'Hallo Welt'
#   Argument: 'foo'
#   Argument: 'bar'
```

**`$*` – Alle Argumente als ein String:**

```zsh
#!/bin/zsh

echo "Mit \$*:"
for arg in "$*"; do
    echo "  Argument: '$arg'"
done
```

**Aufruf:**

```zsh
./args.sh "Hallo Welt" foo bar
# Ausgabe:
#   Argument: 'Hallo Welt foo bar'
```

**Zusammenfassung:**

| Syntax | Bedeutung |
| ------ | --------- |
| `"$@"` | Jedes Argument als separates Wort (Leerzeichen in Argumenten bleiben erhalten) |
| `"$*"` | Alle Argumente als ein einziger String |
| `$@` / `$*` | Ohne Anführungszeichen: Wörter werden bei Leerzeichen getrennt |

In der Regel wird `"$@"` verwendet.

**Alle Argumente an anderen Befehl weitergeben:**

```zsh
#!/bin/zsh
# Wrapper-Skript

echo "Starte Programm mit allen Argumenten..."
/pfad/zum/programm "$@"
```

### 6.3    Optionen parsen (`getopts`)

Für Skripte mit Optionen (z. B. `-v`, `-f datei`) bietet `getopts` eine strukturierte Lösung.

**Grundsyntax:**

```zsh
while getopts "vf:o:" opt; do
    case $opt in
        v) verbose=true ;;
        f) input_file="$OPTARG" ;;
        o) output_file="$OPTARG" ;;
        ?) echo "Ungültige Option"; exit 1 ;;
    esac
done
```

**Erklärung:**

- `v` – Option ohne Wert (Flag)
- `f:` – Option mit erforderlichem Wert (Doppelpunkt)
- `$OPTARG` – Enthält den Wert der Option

**Vollständiges Beispiel:**

```zsh
#!/bin/zsh
# Datei: prozess.sh

# Standardwerte
verbose=false
input_file=""
output_file="output.txt"

# Hilfe-Funktion
show_help() {
    echo "Verwendung: $0 [-v] [-f eingabe] [-o ausgabe]"
    echo ""
    echo "Optionen:"
    echo "  -v          Ausführliche Ausgabe"
    echo "  -f DATEI    Eingabedatei (erforderlich)"
    echo "  -o DATEI    Ausgabedatei (Standard: output.txt)"
    echo "  -h          Diese Hilfe anzeigen"
}

# Optionen parsen
while getopts "vf:o:h" opt; do
    case $opt in
        v) verbose=true ;;
        f) input_file="$OPTARG" ;;
        o) output_file="$OPTARG" ;;
        h) show_help; exit 0 ;;
        ?) show_help; exit 1 ;;
    esac
done

# Restliche Argumente (nach den Optionen)
shift $((OPTIND - 1))
remaining_args="$@"

# Validierung
if [[ -z "$input_file" ]]; then
    echo "Fehler: Eingabedatei erforderlich (-f)"
    exit 1
fi

# Hauptlogik
if $verbose; then
    echo "Eingabe: $input_file"
    echo "Ausgabe: $output_file"
    echo "Weitere Argumente: $remaining_args"
fi

echo "Verarbeite $input_file..."
```

**Aufruf:**

```zsh
./prozess.sh -v -f input.txt -o result.txt zusatz1 zusatz2
```

## 7    Rückgabewerte und Exit-Codes

Jeder Befehl in Unix gibt einen Exit-Code zurück, der Erfolg oder Misserfolg signalisiert.

### 7.1    `exit` und `$?`

**Exit-Codes:**

| Code | Bedeutung |
| ---- | --------- |
| `0` | Erfolg |
| `1` | Allgemeiner Fehler |
| `2` | Falsche Verwendung (z. B. fehlende Argumente) |
| `126` | Befehl nicht ausführbar |
| `127` | Befehl nicht gefunden |
| `128+N` | Beendet durch Signal N |
| `130` | Beendet durch Ctrl+C (SIGINT) |

**`$?` – Exit-Code des letzten Befehls:**

```zsh
ls /existiert
echo $?  # Ausgabe: 0 (Erfolg)

ls /existiert_nicht
echo $?  # Ausgabe: 1 (Fehler)
```

**`exit` in Skripten:**

```zsh
#!/bin/zsh

if [[ ! -f "$1" ]]; then
    echo "Fehler: Datei nicht gefunden"
    exit 1
fi

echo "Datei gefunden"
exit 0
```

**`return` in Funktionen:**

Funktionen verwenden `return` statt `exit`:

```zsh
pruefe_datei() {
    if [[ -f "$1" ]]; then
        return 0  # Erfolg
    else
        return 1  # Fehler
    fi
}

if pruefe_datei "test.txt"; then
    echo "Datei existiert"
else
    echo "Datei nicht gefunden"
fi
```

**Exit-Code direkt in Bedingung:**

```zsh
if grep -q "suchbegriff" datei.txt; then
    echo "Gefunden"
else
    echo "Nicht gefunden"
fi
```

### 7.2    Fehlerbehandlung

**Befehlsverkettung mit `&&` und `||`:**

```zsh
# && – Nächster Befehl nur bei Erfolg
mkdir neuer_ordner && cd neuer_ordner && echo "Erfolgreich"

# || – Nächster Befehl nur bei Fehler
cd /existiert_nicht || echo "Verzeichnis nicht gefunden"

# Kombination
cd /verzeichnis && echo "OK" || echo "Fehler"
```

**Prüfung mit `if`:**

```zsh
#!/bin/zsh

if ! command -v git &> /dev/null; then
    echo "Git ist nicht installiert"
    exit 1
fi

echo "Git ist verfügbar"
```

**Fehlerausgabe umleiten:**

```zsh
# Nur Fehler unterdrücken
befehl 2>/dev/null

# Alles unterdrücken
befehl &>/dev/null

# Fehler in Datei schreiben
befehl 2>> fehler.log
```

## 8    Ausführen und Debuggen

### 8.1    `chmod +x`

Bevor ein Skript direkt ausgeführt werden kann, muss es als ausführbar markiert werden:

```zsh
# Ausführbar machen
chmod +x mein_skript.sh

# Ausführen
./mein_skript.sh
```

**Alternativ ohne chmod:**

```zsh
zsh mein_skript.sh
bash mein_skript.sh
```

**Berechtigungen prüfen:**

```zsh
ls -l mein_skript.sh
# -rwxr-xr-x  1 user  staff  123 Jan 1 12:00 mein_skript.sh
#  ^^^
#  Die x's zeigen Ausführbarkeit an
```

### 8.2    `set -x` Debugging

Mit `set -x` wird jeder ausgeführte Befehl vor der Ausführung angezeigt:

```zsh
#!/bin/zsh
set -x  # Debug-Modus aktivieren

name="Max"
echo "Hallo $name"
datum=$(date)
echo "Datum: $datum"
```

**Ausgabe:**

```
+name=Max
+echo 'Hallo Max'
Hallo Max
+date
+datum='Mon Jan 1 12:00:00 CET 2024'
+echo 'Datum: Mon Jan 1 12:00:00 CET 2024'
Datum: Mon Jan 1 12:00:00 CET 2024
```

**Debug-Modus ein-/ausschalten:**

```zsh
set -x  # Aktivieren
# ... Debug-relevanter Code ...
set +x  # Deaktivieren
```

**Nur beim Aufruf aktivieren:**

```zsh
zsh -x mein_skript.sh
bash -x mein_skript.sh
```

### 8.3    Fehlerbehandlung (`set -e`, `trap`)

**`set -e` – Bei Fehler abbrechen:**

Standardmäßig läuft ein Skript weiter, auch wenn ein Befehl fehlschlägt. Mit `set -e` wird bei jedem Fehler abgebrochen:

```zsh
#!/bin/zsh
set -e

echo "Schritt 1"
falsch_geschriebener_befehl  # Skript bricht hier ab
echo "Schritt 2"             # Wird nicht erreicht
```

**`set -u` – Fehler bei undefinierten Variablen:**

```zsh
#!/bin/zsh
set -u

echo $undefinierte_variable  # Skript bricht ab
```

**`set -o pipefail` – Pipe-Fehler erkennen:**

```zsh
#!/bin/zsh
set -o pipefail

# Ohne pipefail: Exit-Code ist 0 (von wc)
# Mit pipefail: Exit-Code ist 1 (von cat)
cat nicht_vorhanden.txt | wc -l
```

**Empfohlene Kombination:**

```zsh
#!/bin/zsh
set -euo pipefail
```

**`trap` – Aufräumen bei Beendigung:**

`trap` führt Befehle aus, wenn das Skript beendet wird (normal oder durch Fehler):

```zsh
#!/bin/zsh
set -e

# Temporäre Datei erstellen
temp_file=$(mktemp)
echo "Temp-Datei: $temp_file"

# Bei Beendigung aufräumen
trap "rm -f $temp_file; echo 'Aufgeräumt'" EXIT

# Hauptlogik
echo "Arbeite..."
sleep 2
echo "Fertig"

# temp_file wird automatisch gelöscht
```

**Verschiedene Signale abfangen:**

```zsh
#!/bin/zsh

cleanup() {
    echo "Räume auf..."
    # Aufräumarbeiten hier
}

trap cleanup EXIT      # Bei normalem Ende
trap cleanup SIGINT    # Bei Ctrl+C
trap cleanup SIGTERM   # Bei kill
```

**Praktisches Beispiel:**

```zsh
#!/bin/zsh
set -euo pipefail

# Temporäres Verzeichnis
work_dir=$(mktemp -d)
trap "rm -rf $work_dir" EXIT

echo "Arbeitsverzeichnis: $work_dir"

# Dateien im temp-Verzeichnis verarbeiten
cd "$work_dir"
curl -O https://example.com/datei.zip
unzip datei.zip
# ... weitere Verarbeitung ...

# work_dir wird automatisch gelöscht
```

### 8.4    Profiling (`time`, `dtruss`)

**`time` – Ausführungszeit messen:**

```zsh
time ./mein_skript.sh
```

**Ausgabe:**

```
./mein_skript.sh  0.05s user 0.02s system 89% cpu 0.078 total
```

| Wert | Bedeutung |
| ---- | --------- |
| `user` | CPU-Zeit im User-Mode |
| `system` | CPU-Zeit im Kernel-Mode |
| `cpu` | CPU-Auslastung in Prozent |
| `total` | Gesamte verstrichene Zeit |

**Einzelne Befehle messen:**

```zsh
time sleep 2
time find / -name "*.log" 2>/dev/null
```

**`dtruss` – Systemaufrufe verfolgen (macOS):**

`dtruss` ist das macOS-Äquivalent zu `strace` unter Linux. Es zeigt alle Systemaufrufe eines Prozesses:

```zsh
# Erfordert Root-Rechte
sudo dtruss ./mein_skript.sh
```

**Nur bestimmte Syscalls:**

```zsh
sudo dtruss -t open ./mein_skript.sh   # Nur open()-Aufrufe
sudo dtruss -t read ./mein_skript.sh   # Nur read()-Aufrufe
```

**Laufenden Prozess analysieren:**

```zsh
sudo dtruss -p <PID>
```

**Alternative: `dtrace`:**

Für komplexere Analysen bietet macOS `dtrace`:

```zsh
sudo dtrace -n 'syscall:::entry /execname == "zsh"/ { @[probefunc] = count(); }'
```

### 8.5    Shebang `#!/bin/zsh`

Die erste Zeile eines Skripts (Shebang) bestimmt, welcher Interpreter verwendet wird:

```zsh
#!/bin/zsh
```

**Häufige Shebangs:**

| Shebang | Interpreter |
| ------- | ----------- |
| `#!/bin/zsh` | Z-Shell (macOS-Standard) |
| `#!/bin/bash` | Bash |
| `#!/bin/sh` | POSIX-Shell (portabel) |
| `#!/usr/bin/env zsh` | zsh aus PATH (flexibler) |
| `#!/usr/bin/env python3` | Python 3 |

**`/usr/bin/env` – Flexibler Ansatz:**

```zsh
#!/usr/bin/env zsh
```

Vorteile:

- Findet den Interpreter im `$PATH`
- Funktioniert auch wenn zsh nicht in `/bin/zsh` liegt
- Portabler zwischen verschiedenen Systemen

**Wichtig:**

- Der Shebang muss in der **ersten Zeile** stehen
- Vor `#!` dürfen **keine Zeichen** stehen (auch keine Leerzeichen)
- Die Zeile muss mit einem Zeilenumbruch enden

## 9    Formatierung der Ausgabe

Insbesondere beim Schreiben von Aliasen, Funktionen und Skripten kann der Einsatz Farbe und Fettmarkierungen hilfreich sein, um die Ausgabe übersichtlich zu halten.

**Beispiele:**

```zsh
echo "\033[31mRot\033[0m"          # Rot
echo "\033[32mGrün\033[0m"         # Grün
echo "\033[1;33mFett Gelb\033[0m"  # Fetter Text
echo -e "\033[34m$(pwd)\033[0m"    # Ausgabe von `pwd` in blau
```

- `\033[` leitet eine Escape-Sequenz ein
- `31` = Rot, `32` = Grün, `34` = Blau
- `1` = Fett
- `0m` = Zurücksetzen der Formatierung

**Häufig verwendete Farben:**

| Code | Farbe   | Stil   |
| ---- | ------- | ------ |
| `30` | Schwarz | normal |
| `31` | Rot     | normal |
| `32` | Grün    | normal |
| `33` | Gelb    | normal |
| `34` | Blau    | normal |
| `35` | Magenta | normal |
| `36` | Cyan    | normal |
| `37` | Weiß    | normal |

**Stile:**

**Erweiterte Stiltabelle:**

|Code|Stil|
|---|---|
|`0`|Reset/Normal|
|`1`|Fett|
|`2`|Gedimmt|
|`3`|Kursiv|
|`4`|Unterstrichen|
|`5`|Blinkend|
|`7`|Invertiert|
|`9`|Durchgestrichen|

> [!WARNING]
> Kursiv wird nicht von allen Terminals unterstützt. In manchen Terminals wird es als inverse Darstellung oder gar nicht angezeigt. Die meisten modernen Terminal-Emulatoren (iTerm2, Terminal.app ab neueren Versionen, etc.) unterstützen es jedoch.

## 10    Skript-Vorlage

```zsh
#!/usr/bin/env zsh
set -euo pipefail

# [Short description of the script]

# --- Configuration ---
readonly SCRIPT_DIR="$(cd "$(dirname "$0")" && pwd)"
readonly SCRIPT_NAME="$(basename "$0")"

# --- Functions ---
show_help() {
    cat << EOF
Usage: $SCRIPT_NAME [OPTIONS] <argument>

Description of the script.

Options:
  -h, --help    Show this help

Example:
  $SCRIPT_NAME -v input.txt
EOF
}

log() { echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*" }


# --- Main program ---
main() {
    if [[ $# -eq 0 ]]; then
        show_help
        exit 1
    fi

    # Parse command line options
    while getopts "h:" opt; do  # Add further flags here
        case $opt in
            h)  show_help; exit 0 ;;
            \?) echo "Invalid option: -$OPTARG" >&2; exit 1 ;;
            :)  echo "Option -$OPTARG requires an argument" >&2; exit 1 ;;
        esac
    done

    log "Script started"
    # ... Main logic here ...
    log "Script finished"
}

main "$@"
```
