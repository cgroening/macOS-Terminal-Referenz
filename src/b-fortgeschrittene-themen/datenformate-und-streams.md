# Datenformate & Streams

Dieses Kapitel behandelt Tools zur Verarbeitung strukturierter Datenformate (JSON, YAML, CSV) sowie Komprimierung und Archivierung.

## 1    JSON

JSON (JavaScript Object Notation) ist das Standard-Format für APIs und Konfigurationsdateien. `jq` ist das mächtigste Tool zur JSON-Verarbeitung auf der Kommandozeile.

### 1.1    `jq` – Installation und Grundlagen

**Installation:**

```zsh
brew install jq

# Version prüfen
jq --version
```

**Grundlegende Nutzung:**

```zsh
# JSON formatieren (Pretty Print)
echo '{"name":"Max","age":30}' | jq .

# Aus Datei lesen
jq . data.json

# Kompakt ausgeben
jq -c . data.json
```

**Beispiel-JSON für diese Referenz:**

```json
{
  "users": [
    {"id": 1, "name": "Alice", "email": "alice@example.com", "active": true},
    {"id": 2, "name": "Bob", "email": "bob@example.com", "active": false},
    {"id": 3, "name": "Carol", "email": "carol@example.com", "active": true}
  ],
  "meta": {
    "total": 3,
    "page": 1
  }
}
```

**Grundlegende Filter:**

```zsh
# Ganzes Dokument
jq '.' data.json

# Einzelnes Feld
jq '.meta' data.json
# {"total": 3, "page": 1}

# Verschachteltes Feld
jq '.meta.total' data.json
# 3

# Array
jq '.users' data.json

# Erstes Array-Element
jq '.users[0]' data.json

# Letztes Element
jq '.users[-1]' data.json

# Bereich (Slice)
jq '.users[0:2]' data.json
```

### 1.2    Filter und Selektoren

**Feld-Zugriff:**

```zsh
# Punkt-Notation
jq '.users[0].name' data.json
# "Alice"

# Ohne Quotes
jq -r '.users[0].name' data.json
# Alice

# Mehrere Felder
jq '.users[0] | .name, .email' data.json
# "Alice"
# "alice@example.com"
```

**Array-Operationen:**

```zsh
# Alle Elemente iterieren
jq '.users[]' data.json

# Bestimmtes Feld aller Elemente
jq '.users[].name' data.json
# "Alice"
# "Bob"
# "Carol"

# Als Array sammeln
jq '[.users[].name]' data.json
# ["Alice", "Bob", "Carol"]

# Array-Länge
jq '.users | length' data.json
# 3
```

**Filtern mit select:**

```zsh
# Nach Bedingung filtern
jq '.users[] | select(.active == true)' data.json

# Nach Wert filtern
jq '.users[] | select(.id > 1)' data.json

# String-Matching
jq '.users[] | select(.name | contains("o"))' data.json

# Regex
jq '.users[] | select(.email | test("@example"))' data.json

# Mehrere Bedingungen (AND)
jq '.users[] | select(.active == true and .id > 1)' data.json

# OR-Bedingung
jq '.users[] | select(.active == true or .id == 2)' data.json
```

**Map und Filter:**

```zsh
# Map über Array
jq '.users | map(.name)' data.json
# ["Alice", "Bob", "Carol"]

# Map mit Transformation
jq '.users | map(.name | ascii_upcase)' data.json
# ["ALICE", "BOB", "CAROL"]

# Filter (select als map)
jq '.users | map(select(.active))' data.json

# Kombiniert
jq '.users | map(select(.active)) | map(.email)' data.json
# ["alice@example.com", "carol@example.com"]
```

**Nützliche Operatoren:**

| Operator | Beschreibung              | Beispiel                 |
| -------- | ------------------------- | ------------------------ |
| `.`      | Identität (ganzes Objekt) | `jq '.'`                 |
| `.field` | Feld-Zugriff              | `jq '.name'`             |
| `.[]`    | Array-Iterator            | `jq '.users[]'`          |
| `.[n]`   | Index-Zugriff             | `jq '.[0]'`              |
| \|       | Pipe (verketten)          | jq '.users[] \| .name'   |
| `,`      | Mehrere Ausgaben          | `jq '.name, .age'`       |
| `?`      | Optional (kein Fehler)    | `jq '.missing?'`         |
| `//`     | Default-Wert              | `jq '.missing // "N/A"'` |

### 1.3    Transformation und Ausgabeformatierung

**Objekte konstruieren:**

```zsh
# Neues Objekt erstellen
jq '.users[] | {username: .name, mail: .email}' data.json

# Mit statischen Feldern
jq '.users[] | {type: "user", name: .name}' data.json

# Alle in Array
jq '[.users[] | {username: .name, mail: .email}]' data.json
```

**Werte modifizieren:**

```zsh
# Feld ändern
jq '.meta.page = 2' data.json

# Feld hinzufügen
jq '.meta.timestamp = "2024-01-15"' data.json

# Feld löschen
jq 'del(.meta.page)' data.json

# In Array-Elementen ändern
jq '.users[0].name = "Alicia"' data.json

# Alle Array-Elemente ändern
jq '.users[].active = true' data.json
```

**Ausgabeformatierung:**

```zsh
# Raw-Strings (ohne Quotes)
jq -r '.users[].name' data.json

# Kompakt (eine Zeile)
jq -c '.' data.json

# Tab-Einrückung
jq --tab '.' data.json

# Sortierte Keys
jq -S '.' data.json

# Als TSV (Tab-getrennt)
jq -r '.users[] | [.id, .name, .email] | @tsv' data.json
# 1    Alice    alice@example.com
# 2    Bob      bob@example.com

# Als CSV
jq -r '.users[] | [.id, .name, .email] | @csv' data.json
# 1,"Alice","alice@example.com"

# URI-Encoding
jq -r '.users[0].email | @uri' data.json

# Base64
jq -r '.users[0].name | @base64' data.json
```

**String-Interpolation:**

```zsh
# Template-String
jq -r '.users[] | "User: \(.name) <\(.email)>"' data.json
# User: Alice <alice@example.com>
# User: Bob <bob@example.com>
# User: Carol <carol@example.com>
```

**Eingebaute Funktionen:**

| Funktion         | Beschreibung        | Beispiel                        |
| ---------------- | ------------------- | ------------------------------- |
| `length`         | Länge               | `.users \| length`              |
| `keys`           | Objekt-Keys         | `.meta \| keys`                 |
| `values`         | Objekt-Werte        | `.meta \| values`               |
| `type`           | Typ ermitteln       | `.users \| type`                |
| `sort`           | Array sortieren     | `.users \| sort_by(.name)`      |
| `unique`         | Duplikate entfernen | `[.users[].active] \| unique`   |
| `reverse`        | Array umkehren      | `.users \| reverse`             |
| `first` / `last` | Erstes/Letztes      | `.users \| first`               |
| `add`            | Summe/Konkatenation | `[.users[].id] \| add`          |
| `min` / `max`    | Minimum/Maximum     | `[.users[].id] \| max`          |
| `group_by`       | Gruppieren          | `.users \| group_by(.active)`   |
| `ascii_downcase` | Kleinbuchstaben     | `.name \| ascii_downcase`       |
| `split`          | String teilen       | `.email \| split("@")`          |
| `join`           | Array verbinden     | `[.users[].name] \| join(", ")` |
| `contains`       | Enthält             | `.name \| contains("li")`       |
| `startswith`     | Beginnt mit         | `.email \| startswith("a")`     |
| `sub` / `gsub`   | Ersetzen            | `.name \| gsub("a"; "X")`       |

### 1.4    Praxisbeispiele (API-Responses verarbeiten)

**curl mit jq:**

```zsh
# API-Response formatieren
curl -s https://api.github.com/users/octocat | jq .

# Bestimmte Felder extrahieren
curl -s https://api.github.com/users/octocat | jq '{name, bio, location}'

# Array-Response verarbeiten
curl -s https://api.github.com/users/octocat/repos | jq '.[].name'

# Top 5 nach Stars
curl -s https://api.github.com/users/octocat/repos | \
    jq 'sort_by(.stargazers_count) | reverse | .[0:5] | .[].name'
```

**JSON-Konfiguration bearbeiten:**

```zsh
# package.json Version auslesen
jq -r '.version' package.json

# Version erhöhen
jq '.version = "2.0.0"' package.json > package.json.tmp && mv package.json.tmp package.json

# Dependency hinzufügen
jq '.dependencies.lodash = "^4.17.21"' package.json

# Script hinzufügen
jq '.scripts.test = "jest"' package.json
```

**Log-Dateien analysieren:**

```zsh
# JSON-Logs filtern (ein JSON pro Zeile)
cat app.log | jq 'select(.level == "error")'

# Fehler zählen
cat app.log | jq -s 'map(select(.level == "error")) | length'

# Nach Zeitraum filtern
cat app.log | jq 'select(.timestamp > "2024-01-15T00:00:00")'

# Gruppieren nach Level
cat app.log | jq -s 'group_by(.level) | map({level: .[0].level, count: length})'
```

**Mehrere JSON-Dateien:**

```zsh
# Alle JSON-Dateien zusammenführen
jq -s '.' *.json

# In ein Array
jq -s 'add' file1.json file2.json

# Felder aus mehreren Dateien
jq -s 'map(.name)' *.json
```

**Mit Variablen arbeiten:**

```zsh
# Variable übergeben
jq --arg name "Alice" '.users[] | select(.name == $name)' data.json

# Numerische Variable
jq --argjson id 2 '.users[] | select(.id == $id)' data.json

# Aus Umgebungsvariable
export USER_ID=1
jq --argjson id "$USER_ID" '.users[] | select(.id == $id)' data.json
```

**Slurp-Modus (mehrere Objekte als Array):**

```zsh
# Mehrere JSON-Zeilen als Array
echo -e '{"a":1}\n{"a":2}\n{"a":3}' | jq -s '.'
# [{"a":1},{"a":2},{"a":3}]

# Summe berechnen
echo -e '{"a":1}\n{"a":2}\n{"a":3}' | jq -s 'map(.a) | add'
# 6
```

## 2    YAML

YAML ist ein menschenlesbares Format, beliebt für Konfigurationsdateien (Docker, Kubernetes, CI/CD).

### 2.1    `yq` – Installation und Grundlagen

Es gibt zwei verschiedene `yq`-Tools. Wir verwenden hier Mike Farah's Version (Go-basiert):

**Installation:**

```zsh
brew install yq

# Version prüfen
yq --version
# yq (https://github.com/mikefarah/yq/) version v4.x
```

> [!NOTE]
> Es gibt auch ein Python-basiertes yq (`pip install yq`), das jq als Backend nutzt. Die Syntax unterscheidet sich leicht.

**Beispiel-YAML:**

```yaml
# config.yaml
server:
  host: localhost
  port: 8080
  ssl: true

database:
  host: db.example.com
  port: 5432
  name: myapp

users:
  - name: Alice
    role: admin
  - name: Bob
    role: user
```

**Grundlegende Nutzung:**

```zsh
# YAML formatiert ausgeben
yq '.' config.yaml

# Einzelnes Feld
yq '.server.host' config.yaml
# localhost

# Verschachtelt
yq '.database.port' config.yaml
# 5432
```

### 2.2    YAML lesen und bearbeiten

**Felder auslesen:**

```zsh
# Einfaches Feld
yq '.server.port' config.yaml

# Array-Element
yq '.users[0].name' config.yaml
# Alice

# Alle Array-Elemente
yq '.users[].name' config.yaml
# Alice
# Bob

# Mit Länge
yq '.users | length' config.yaml
# 2
```

**Werte ändern:**

```zsh
# Feld ändern (in-place)
yq -i '.server.port = 9090' config.yaml

# Ohne in-place (stdout)
yq '.server.port = 9090' config.yaml

# Neues Feld hinzufügen
yq -i '.server.timeout = 30' config.yaml

# Verschachteltes Feld hinzufügen
yq -i '.logging.level = "debug"' config.yaml

# Feld löschen
yq -i 'del(.server.ssl)' config.yaml
```

**Array-Operationen:**

```zsh
# Element hinzufügen
yq -i '.users += [{"name": "Carol", "role": "user"}]' config.yaml

# Element am Anfang
yq -i '.users = [{"name": "Admin", "role": "admin"}] + .users' config.yaml

# Element löschen
yq -i 'del(.users[1])' config.yaml

# Filtern
yq '.users[] | select(.role == "admin")' config.yaml
```

**Umgebungsvariablen einsetzen:**

```zsh
# Variable einsetzen
yq -i '.database.host = env(DB_HOST)' config.yaml

# Mit Default
yq '.database.host // "localhost"' config.yaml

# Aus Umgebung lesen
export DB_HOST="production.db.com"
yq -i '.database.host = env(DB_HOST)' config.yaml
```

**Kommentare und Formatierung:**

```zsh
# Kommentare erhalten
yq '.' config.yaml

# Kommentar hinzufügen
yq -i '.server.port line_comment="HTTP Port"' config.yaml

# Ausgabe-Stil
yq -o=json '.' config.yaml     # Als JSON
yq -o=props '.' config.yaml    # Als Properties
yq -o=xml '.' config.yaml      # Als XML
```

### 2.3    Konvertierung JSON ↔ YAML

**YAML zu JSON:**

```zsh
# Standard-Konvertierung
yq -o=json '.' config.yaml

# Kompakt
yq -o=json -I=0 '.' config.yaml

# In Datei speichern
yq -o=json '.' config.yaml > config.json
```

**JSON zu YAML:**

```zsh
# JSON einlesen und als YAML ausgeben
yq -P '.' data.json

# Von stdin
echo '{"name": "test", "value": 123}' | yq -P '.'
# name: test
# value: 123

# In Datei
yq -P '.' data.json > data.yaml
```

**Mehrere Dokumente:**

```zsh
# YAML mit mehreren Dokumenten (---)
yq eval-all '.' multi-doc.yaml

# Dokumente zählen
yq eval-all 'documentIndex' multi-doc.yaml | wc -l

# Bestimmtes Dokument
yq 'select(documentIndex == 0)' multi-doc.yaml
```

**Praktische Beispiele:**

```zsh
# Docker-Compose Port ändern
yq -i '.services.web.ports[0] = "9090:80"' docker-compose.yaml

# Kubernetes Replicas erhöhen
yq -i '.spec.replicas = 5' deployment.yaml

# GitHub Actions Workflow bearbeiten
yq -i '.jobs.build.runs-on = "ubuntu-22.04"' .github/workflows/ci.yaml

# Alle Image-Tags auslesen
yq '.spec.template.spec.containers[].image' deployment.yaml
```

## 3    CSV

CSV (Comma-Separated Values) ist das universelle Tabellenformat. `csvkit` bietet eine Suite von Tools zur CSV-Verarbeitung.

### 3.1    `csvkit` – Installation und Überblick

**Installation:**

```zsh
pip install csvkit --break-system-packages
# oder
brew install csvkit
```

**csvkit-Tools:**

| Tool | Beschreibung |
| ---- | ------------ |
| `csvlook` | Formatierte Tabellenansicht |
| `csvcut` | Spalten auswählen |
| `csvgrep` | Zeilen filtern |
| `csvstat` | Statistiken |
| `csvsort` | Sortieren |
| `csvjoin` | Tabellen verbinden |
| `csvstack` | Tabellen stapeln |
| `csvformat` | Format konvertieren |
| `in2csv` | Zu CSV konvertieren |
| `sql2csv` | SQL-Abfrage zu CSV |
| `csvsql` | SQL auf CSV ausführen |

**Beispiel-CSV:**

```csv
id,name,department,salary,start_date
1,Alice,Engineering,75000,2020-03-15
2,Bob,Marketing,65000,2019-07-22
3,Carol,Engineering,80000,2018-11-01
4,David,Sales,70000,2021-01-10
5,Eve,Marketing,72000,2020-09-05
```

### 3.2    `csvlook`, `csvcut`, `csvgrep`

**csvlook – Formatierte Anzeige:**

```zsh
# Tabelle anzeigen
csvlook employees.csv
# | id | name  | department  | salary | start_date |
# | -- | ----- | ----------- | ------ | ---------- |
# |  1 | Alice | Engineering | 75000  | 2020-03-15 |
# |  2 | Bob   | Marketing   | 65000  | 2019-07-22 |
# ...

# Erste 3 Zeilen
head -4 employees.csv | csvlook

# Spaltenbreite begrenzen
csvlook --max-column-width 15 employees.csv

# Ohne Header-Trennlinie
csvlook --no-header-row employees.csv
```

**csvcut – Spalten auswählen:**

```zsh
# Spaltennamen anzeigen
csvcut -n employees.csv
#   1: id
#   2: name
#   3: department
#   4: salary
#   5: start_date

# Spalten nach Nummer
csvcut -c 1,2,4 employees.csv

# Spalten nach Name
csvcut -c name,salary employees.csv

# Spalten ausschließen
csvcut -C id,start_date employees.csv

# Reihenfolge ändern
csvcut -c salary,name,department employees.csv
```

**csvgrep – Zeilen filtern:**

```zsh
# Nach Wert filtern
csvgrep -c department -m "Engineering" employees.csv

# Regex-Suche
csvgrep -c name -r "^[A-C]" employees.csv

# Invertieren (NOT)
csvgrep -c department -m "Marketing" -i employees.csv

# Mehrere Bedingungen (Pipe)
csvgrep -c department -m "Engineering" employees.csv | csvgrep -c salary -r "^[78]"
```

**Kombinationen:**

```zsh
# Engineering-Mitarbeiter, nur Name und Gehalt
csvgrep -c department -m "Engineering" employees.csv | csvcut -c name,salary | csvlook

# Ausgabe:
# | name  | salary |
# | ----- | ------ |
# | Alice | 75000  |
# | Carol | 80000  |
```

### 3.3    `csvstat`, `csvsort`, `csvjoin`

**csvstat – Statistiken:**

```zsh
# Vollständige Statistiken
csvstat employees.csv

# Nur bestimmte Spalte
csvstat -c salary employees.csv
# Type of data: Number
# Contains null values: False
# Unique values: 5
# Smallest value: 65000
# Largest value: 80000
# Sum: 362000
# Mean: 72400
# Median: 72000
# ...

# Nur bestimmte Statistik
csvstat --mean -c salary employees.csv
csvstat --sum -c salary employees.csv
csvstat --count employees.csv
```

**csvsort – Sortieren:**

```zsh
# Nach Spalte sortieren
csvsort -c salary employees.csv

# Absteigend
csvsort -c salary -r employees.csv

# Nach mehreren Spalten
csvsort -c department,salary employees.csv

# Numerisch sortieren (automatisch erkannt)
csvsort -c start_date employees.csv
```

**csvjoin – Tabellen verbinden:**

```zsh
# Beispiel: departments.csv
# id,department_name,budget
# 1,Engineering,500000
# 2,Marketing,300000
# 3,Sales,400000

# employees_dept.csv (mit department_id statt name)
# id,name,department_id,salary
# 1,Alice,1,75000
# 2,Bob,2,65000

# Inner Join
csvjoin -c department_id,id employees_dept.csv departments.csv

# Left Join
csvjoin --left -c department_id,id employees_dept.csv departments.csv

# Ausgabespalten wählen
csvjoin -c department_id,id employees_dept.csv departments.csv | csvcut -c name,department_name,salary
```

**csvstack – Tabellen stapeln:**

```zsh
# Zwei CSVs untereinander
csvstack file1.csv file2.csv

# Mit Quell-Kennzeichnung
csvstack -g "Q1,Q2" -n quarter file1.csv file2.csv
```

**csvsql – SQL auf CSV:**

```zsh
# SQL-Abfrage
csvsql --query "SELECT name, salary FROM employees WHERE salary > 70000" employees.csv

# Aggregation
csvsql --query "SELECT department, AVG(salary) as avg_salary FROM employees GROUP BY department" employees.csv

# Join mit SQL
csvsql --query "SELECT e.name, d.department_name
                FROM employees e
                JOIN departments d ON e.department_id = d.id" \
       employees.csv departments.csv
```

### 3.4    Alternativen: `mlr` (Miller), `xsv`

**Miller (mlr) – Mächtiges Datenverarbeitungs-Tool:**

```zsh
# Installation
brew install miller

# CSV lesen
mlr --csv cat employees.csv

# Pretty Print
mlr --icsv --opprint cat employees.csv

# Filtern
mlr --csv filter '$salary > 70000' employees.csv

# Spalten auswählen
mlr --csv cut -f name,salary employees.csv

# Transformieren
mlr --csv put '$bonus = $salary * 0.1' employees.csv

# Gruppieren
mlr --csv stats1 -a mean -f salary -g department employees.csv

# Sortieren
mlr --csv sort -nr salary employees.csv

# JSON-Ausgabe
mlr --icsv --ojson cat employees.csv
```

**Miller Vorteile:**

- Sehr schnell (C-basiert)
- Unterstützt CSV, JSON, DKVP, Markdown
- Mächtige DSL für Transformationen
- Streaming-fähig

**xsv – Extrem schnelles CSV-Tool:**

```zsh
# Installation
brew install xsv

# Statistiken (sehr schnell)
xsv stats employees.csv | xsv table

# Spalten
xsv select name,salary employees.csv

# Suchen (schneller als csvgrep)
xsv search -s department "Engineering" employees.csv

# Zählen
xsv count employees.csv

# Häufigkeiten
xsv frequency -s department employees.csv

# Sampling
xsv sample 100 large-file.csv

# Sortieren
xsv sort -s salary -R employees.csv

# Index erstellen (für sehr große Dateien)
xsv index employees.csv
xsv slice -i 1000 employees.csv
```

**xsv Vorteile:**

- Extrem schnell (Rust-basiert)
- Ideal für große Dateien
- Index-Unterstützung

**Vergleich:**

| Feature | csvkit | Miller | xsv |
| ------- | ------ | ------ | --- |
| Geschwindigkeit | Mittel | Schnell | Sehr schnell |
| SQL-Support | ✅ | ❌ | ❌ |
| JSON-Support | ⚠️ | ✅ | ❌ |
| Transformation | Basis | Sehr gut | Basis |
| Große Dateien | ⚠️ | ✅ | ✅ |
| Verfügbarkeit | pip/brew | brew | brew |

## 4    Komprimierung & Archivierung

### 4.1    `tar` – Archive erstellen und entpacken

`tar` (Tape Archive) kombiniert mehrere Dateien in ein Archiv. Ursprünglich ohne Kompression, heute meist mit gzip oder xz kombiniert.

**Archiv erstellen:**

```zsh
# Ohne Kompression
tar -cvf archiv.tar ordner/
# c = create
# v = verbose
# f = file

# Mit gzip-Kompression
tar -czvf archiv.tar.gz ordner/
# z = gzip

# Mit bzip2-Kompression
tar -cjvf archiv.tar.bz2 ordner/
# j = bzip2

# Mit xz-Kompression (beste Kompression)
tar -cJvf archiv.tar.xz ordner/
# J = xz

# Mehrere Dateien/Ordner
tar -czvf archiv.tar.gz datei1.txt datei2.txt ordner/

# Mit Ausschlüssen
tar -czvf archiv.tar.gz --exclude='*.log' --exclude='node_modules' ordner/

# Aus Liste
tar -czvf archiv.tar.gz -T files.txt
```

**Archiv entpacken:**

```zsh
# tar
tar -xvf archiv.tar

# tar.gz / tgz
tar -xzvf archiv.tar.gz

# tar.bz2
tar -xjvf archiv.tar.bz2

# tar.xz
tar -xJvf archiv.tar.xz

# In bestimmtes Verzeichnis
tar -xzvf archiv.tar.gz -C /ziel/verzeichnis/

# Nur bestimmte Dateien
tar -xzvf archiv.tar.gz pfad/zur/datei.txt
```

**Archiv-Inhalt anzeigen:**

```zsh
# Inhalt auflisten
tar -tvf archiv.tar.gz

# Nur Dateinamen
tar -tf archiv.tar.gz

# Mit grep filtern
tar -tf archiv.tar.gz | grep "\.py$"
```

**Wichtige Optionen:**

| Option | Beschreibung |
| ------ | ------------ |
| `-c` | Create (erstellen) |
| `-x` | Extract (entpacken) |
| `-t` | List (auflisten) |
| `-v` | Verbose |
| `-f FILE` | Archivdatei |
| `-z` | gzip |
| `-j` | bzip2 |
| `-J` | xz |
| `-C DIR` | Zielverzeichnis |
| `--exclude` | Ausschließen |
| `-p` | Berechtigungen erhalten |
| `--strip-components=N` | N Pfad-Ebenen entfernen |

**Praktische Beispiele:**

```zsh
# Backup mit Datum
tar -czvf "backup-$(date +%Y%m%d).tar.gz" ~/Documents/

# Nur neue/geänderte Dateien (inkrementell)
tar -czvf backup.tar.gz --newer-mtime="2024-01-01" ~/Documents/

# Über SSH auf Remote-Server
tar -czvf - ordner/ | ssh user@server "cat > backup.tar.gz"

# Von Remote-Server entpacken
ssh user@server "cat backup.tar.gz" | tar -xzvf -

# Pfad-Präfix entfernen beim Entpacken
tar -xzvf archiv.tar.gz --strip-components=1
```

### 4.2    `gzip` / `gunzip` – Einzeldateien komprimieren

`gzip` komprimiert einzelne Dateien (ersetzt Original durch .gz-Datei).

**Komprimieren:**

```zsh
# Datei komprimieren (Original wird ersetzt)
gzip datei.txt
# Ergebnis: datei.txt.gz

# Original behalten
gzip -k datei.txt

# Kompressionsgrad (1-9, 9=beste)
gzip -9 datei.txt

# Schnellste Kompression
gzip -1 datei.txt

# Mehrere Dateien
gzip file1.txt file2.txt file3.txt

# Rekursiv (alle Dateien in Ordner)
gzip -r ordner/
```

**Dekomprimieren:**

```zsh
# Entpacken
gunzip datei.txt.gz
# oder
gzip -d datei.txt.gz

# Original behalten
gunzip -k datei.txt.gz

# Nach stdout (Original behalten)
gunzip -c datei.txt.gz > datei.txt
zcat datei.txt.gz > datei.txt
```

**Informationen:**

```zsh
# Kompressionsinfos
gzip -l datei.txt.gz
# compressed  uncompressed  ratio  uncompressed_name
#      1234         5678    78.3%  datei.txt

# Integrität prüfen
gzip -t datei.txt.gz
```

**Mit Pipes:**

```zsh
# Komprimiert speichern
cat large-file.log | gzip > large-file.log.gz

# Komprimierte Datei durchsuchen
zgrep "error" logfile.gz
zcat logfile.gz | grep "error"

# Komprimierte Datei anzeigen
zless logfile.gz
zcat logfile.gz | less
```

### 4.3    `zip` / `unzip` – ZIP-Archive

ZIP ist das universelle Archivformat, kompatibel mit Windows und macOS Finder.

**Archiv erstellen:**

```zsh
# Dateien zippen
zip archiv.zip file1.txt file2.txt

# Ordner (rekursiv)
zip -r archiv.zip ordner/

# Mit Passwort
zip -e -r archiv.zip ordner/

# Kompressionsgrad (0-9)
zip -9 -r archiv.zip ordner/

# Keine Kompression (nur archivieren)
zip -0 -r archiv.zip ordner/

# Ausschließen
zip -r archiv.zip ordner/ -x "*.log" -x "*.tmp"

# Versteckte Dateien ausschließen
zip -r archiv.zip ordner/ -x ".*" -x "*/.*"
```

**Archiv entpacken:**

```zsh
# Entpacken
unzip archiv.zip

# In bestimmtes Verzeichnis
unzip archiv.zip -d /ziel/

# Nur bestimmte Dateien
unzip archiv.zip "*.txt"

# Überschreiben ohne Nachfrage
unzip -o archiv.zip

# Nie überschreiben
unzip -n archiv.zip

# Leise
unzip -q archiv.zip
```

**Archiv verwalten:**

```zsh
# Inhalt anzeigen
unzip -l archiv.zip

# Detailliert
unzip -v archiv.zip

# Integrität prüfen
unzip -t archiv.zip

# Datei zum Archiv hinzufügen
zip archiv.zip neue-datei.txt

# Datei aktualisieren (nur wenn neuer)
zip -u archiv.zip datei.txt

# Datei aus Archiv löschen
zip -d archiv.zip datei.txt
```

**Passwortgeschützte Archive:**

```zsh
# Mit Passwort erstellen
zip -e -r geheim.zip ordner/

# Entpacken (Passwort-Prompt)
unzip geheim.zip

# Passwort auf Kommandozeile (unsicher!)
unzip -P "geheim123" geheim.zip
```

### 4.4    `xz` – Hohe Kompression

`xz` bietet die beste Kompressionsrate, ist aber langsamer als gzip.

**Komprimieren:**

```zsh
# Standard-Kompression
xz datei.txt
# Ergebnis: datei.txt.xz

# Original behalten
xz -k datei.txt

# Maximale Kompression (langsam)
xz -9 datei.txt

# Schnelle Kompression
xz -1 datei.txt

# Extreme Kompression
xz -e -9 datei.txt

# Mit Threads (schneller)
xz -T 0 datei.txt  # alle CPUs
xz -T 4 datei.txt  # 4 Threads
```

**Dekomprimieren:**

```zsh
# Entpacken
unxz datei.txt.xz
# oder
xz -d datei.txt.xz

# Original behalten
unxz -k datei.txt.xz

# Nach stdout
xzcat datei.txt.xz
```

**Informationen:**

```zsh
# Details anzeigen
xz -l datei.txt.xz

# Integrität prüfen
xz -t datei.txt.xz
```

### 4.5    `bzip2` – Alternative Kompression

`bzip2` bietet bessere Kompression als gzip, ist aber langsamer.

**Komprimieren:**

```zsh
# Komprimieren
bzip2 datei.txt
# Ergebnis: datei.txt.bz2

# Original behalten
bzip2 -k datei.txt

# Kompressionsgrad
bzip2 -9 datei.txt
```

**Dekomprimieren:**

```zsh
# Entpacken
bunzip2 datei.txt.bz2
# oder
bzip2 -d datei.txt.bz2

# Nach stdout
bzcat datei.txt.bz2
```

### 4.6    Kombinationen (`tar.gz`, `tar.xz`)

**Vergleich der Formate:**

| Format | Kompression | Geschwindigkeit | Verbreitung |
| ------ | ----------- | --------------- | ----------- |
| `.tar` | Keine | Sehr schnell | Unix |
| `.tar.gz` / `.tgz` | Gut | Schnell | Universell |
| `.tar.bz2` | Besser | Mittel | Unix |
| `.tar.xz` | Beste | Langsam | Modern |
| `.zip` | Gut | Schnell | Universell |

**Empfehlungen:**

```zsh
# Für schnelle Backups
tar -czvf backup.tar.gz ordner/

# Für maximale Kompression (Archive, Downloads)
tar -cJvf archiv.tar.xz ordner/

# Für Windows-Kompatibilität
zip -r archiv.zip ordner/

# Für sehr große Dateien (mit Fortschritt)
tar -cvf - ordner/ | pv | gzip > archiv.tar.gz
```

**Cheatsheet:**

```zsh
# tar erstellen
tar -czvf archiv.tar.gz ordner/     # gzip
tar -cjvf archiv.tar.bz2 ordner/    # bzip2
tar -cJvf archiv.tar.xz ordner/     # xz

# tar entpacken
tar -xzvf archiv.tar.gz             # gzip
tar -xjvf archiv.tar.bz2            # bzip2
tar -xJvf archiv.tar.xz             # xz

# tar auflisten
tar -tzvf archiv.tar.gz

# Einzeldateien
gzip/gunzip datei.txt               # .gz
bzip2/bunzip2 datei.txt             # .bz2
xz/unxz datei.txt                   # .xz

# ZIP
zip -r archiv.zip ordner/
unzip archiv.zip

# Komprimierte Dateien lesen
zcat/zless/zgrep                    # .gz
bzcat/bzless/bzgrep                 # .bz2
xzcat/xzless/xzgrep                 # .xz
```

