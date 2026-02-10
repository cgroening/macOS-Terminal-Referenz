# Regex, `sed` & `awk`

Reguläre Ausdrücke (Regex) sind ein mächtiges Werkzeug zur Mustererkennung in Texten. In Kombination mit `sed` und `awk` ermöglichen sie komplexe Textverarbeitung direkt im Terminal.

## 1    Regex-Grundlagen

Reguläre Ausdrücke beschreiben Suchmuster für Text. Sie werden von vielen Tools unterstützt: `grep`, `sed`, `awk`, `find`, `vim` und Programmiersprachen wie Python, JavaScript und Rust.

### 1.1    Literale Zeichen

Die einfachste Form: Zeichen matchen sich selbst.

```zsh
echo "Hallo Welt" | grep "Welt"   # Findet "Welt"
echo "Hallo Welt" | grep "xyz"    # Keine Ausgabe
```

### 1.2    Metazeichen

Metazeichen haben eine besondere Bedeutung:

| Zeichen | Bedeutung | Beispiel | Matcht |
| ------- | --------- | -------- | ------ |
| `.` | Beliebiges einzelnes Zeichen | `H.llo` | Hallo, Hello, H2llo |
| `^` | Zeilenanfang | `^Hallo` | "Hallo" am Anfang |
| `$` | Zeilenende | `Welt$` | "Welt" am Ende |
| `*` | 0 oder mehr des vorherigen | `ab*c` | ac, abc, abbc, abbbc |
| `+` | 1 oder mehr des vorherigen | `ab+c` | abc, abbc (nicht ac) |
| `?` | 0 oder 1 des vorherigen | `colou?r` | color, colour |
| `\` | Escapen von Metazeichen | `\.` | Literaler Punkt |

**Beispiele:**

```zsh
# Zeilen die mit "Error" beginnen
grep "^Error" logfile.txt

# Zeilen die mit ".txt" enden
grep "\.txt$" dateiliste.txt

# Beliebiges Zeichen zwischen H und llo
echo -e "Hallo\nHello\nHullo" | grep "H.llo"
```

### 1.3    Zeichenklassen

Zeichenklassen matchen eines von mehreren Zeichen:

| Syntax   | Bedeutung           | Beispiel                               |
| -------- | ------------------- | -------------------------------------- |
| `[abc]`  | a, b oder c         | `[Hh]allo` matcht "Hallo" und  "hallo" |
| `[^abc]` | Nicht a, b oder c   | `[^0-9]` matcht Nicht-Ziffern          |
| `[a-z]`  | Kleinbuchstaben a–z | `[a-zA-Z]` alle Buchstaben             |
| `[0-9]`  | Ziffern 0–9         | `[0-9]+` eine oder mehr Ziffern        |

**Vordefinierte Zeichenklassen (POSIX):**

| Klasse | Bedeutung | Äquivalent |
| ------ | --------- | ---------- |
| `[[:alpha:]]` | Buchstaben | `[a-zA-Z]` |
| `[[:digit:]]` | Ziffern | `[0-9]` |
| `[[:alnum:]]` | Buchstaben und Ziffern | `[a-zA-Z0-9]` |
| `[[:space:]]` | Whitespace | `[ \t\n\r]` |
| `[[:upper:]]` | Großbuchstaben | `[A-Z]` |
| `[[:lower:]]` | Kleinbuchstaben | `[a-z]` |
| `[[:punct:]]` | Satzzeichen | `[.,!?;:]` etc. |

**Beispiele:**

```zsh
# Nur Zeilen mit Ziffern
grep "[0-9]" datei.txt

# Zeilen die mit Großbuchstaben beginnen
grep "^[A-Z]" datei.txt

# Wörter mit Bindestrich
grep "[a-z]-[a-z]" datei.txt
```

### 1.4    Quantifizierer

Quantifizierer bestimmen, wie oft ein Element vorkommen darf:

| Syntax | Bedeutung | Beispiel |
| ------ | --------- | -------- |
| `*` | 0 oder mehr | `a*` matcht "", a, aa, aaa |
| `+` | 1 oder mehr | `a+` matcht a, aa, aaa (nicht "") |
| `?` | 0 oder 1 | `a?` matcht "", a |
| `{n}` | Genau n-mal | `a{3}` matcht aaa |
| `{n,}` | Mindestens n-mal | `a{2,}` matcht aa, aaa, aaaa |
| `{n,m}` | n bis m-mal | `a{2,4}` matcht aa, aaa, aaaa |

**Beispiele:**

```zsh
# Telefonnummern (vereinfacht)
grep -E "[0-9]{3,5}" kontakte.txt

# Postleitzahlen (5 Ziffern)
grep -E "^[0-9]{5}$" plz.txt

# Optionaler Bindestrich
grep -E "[0-9]{4}-?[0-9]{4}" nummern.txt
```

### 1.5    Gruppen und Alternativen

| Syntax | Bedeutung | Beispiel |
| ------ | --------- | -------- |
| `(...)` | Gruppierung | `(ab)+` matcht ab, abab, ababab |
| `\|` | Alternative (ODER) | `cat\|dog` matcht cat oder dog |
| `\1`, `\2` | Rückreferenz | `(.)\1` matcht aa, bb, cc (Dopplungen) |

**Beispiele:**

```zsh
# "error" oder "warning" (case-insensitive)
grep -iE "(error|warning)" logfile.txt

# Wiederholte Wörter finden
grep -E "\b(\w+)\s+\1\b" text.txt

# Entweder http oder https
grep -E "https?://" urls.txt
```

### 1.6    Anker und Wortgrenzen

| Syntax | Bedeutung |
| ------ | --------- |
| `^` | Zeilenanfang |
| `$` | Zeilenende |
| `\b` | Wortgrenze |
| `\B` | Keine Wortgrenze |
| `\<` | Wortanfang |
| `\>` | Wortende |

**Beispiele:**

```zsh
# Nur das Wort "the" (nicht "there", "other")
grep -E "\bthe\b" text.txt

# Zeilen die nur aus Ziffern bestehen
grep -E "^[0-9]+$" datei.txt

# Leere Zeilen
grep -E "^$" datei.txt
```

### 1.7    BRE vs. ERE

Es gibt zwei Regex-Varianten:

**BRE (Basic Regular Expressions):**
- Standard bei `grep` und `sed`
- `+`, `?`, `|`, `()` müssen escaped werden: `\+`, `\?`, `\|`, `\(\)`

**ERE (Extended Regular Expressions):**
- Bei `grep -E` (oder `egrep`) und `sed -E`
- `+`, `?`, `|`, `()` funktionieren direkt

```zsh
# BRE: Escaping nötig
grep "https\?" urls.txt
sed 's/\(error\|warning\)/PROBLEM/g' log.txt

# ERE: Kein Escaping
grep -E "https?" urls.txt
sed -E 's/(error|warning)/PROBLEM/g' log.txt
```

**Empfehlung:** ERE verwenden (`grep -E`, `sed -E`) für bessere Lesbarkeit.

### 1.8    Regex-Schnellreferenz

```
Zeichen:
  .         Beliebiges Zeichen
  \d        Ziffer (manche Tools)
  \w        Wortzeichen [a-zA-Z0-9_]
  \s        Whitespace

Anker:
  ^         Zeilenanfang
  $         Zeilenende
  \b        Wortgrenze

Quantifizierer:
  *         0 oder mehr
  +         1 oder mehr
  ?         0 oder 1
  {n}       Genau n
  {n,m}     n bis m

Klassen:
  [abc]     a, b oder c
  [^abc]    Nicht a, b, c
  [a-z]     Bereich a bis z

Gruppen:
  (...)     Gruppe
  |         Alternative
  \1        Rückreferenz
```

## 2    `sed` – Stream Editor

`sed` (Stream Editor) verarbeitet Text zeilenweise und führt Transformationen durch. Es ist ideal für automatisierte Ersetzungen und Textmanipulationen.

### 2.1    Grundsyntax

```zsh
sed [OPTIONEN] 'BEFEHL' [DATEI]
```

**Häufige Optionen:**

| Option | Bedeutung |
| ------ | --------- |
| `-i` | In-place: Datei direkt ändern |
| `-i ''` | In-place ohne Backup (macOS) |
| `-i.bak` | In-place mit Backup |
| `-E` | Extended Regex (ERE) |
| `-n` | Keine automatische Ausgabe |

### 2.2    Suchen und Ersetzen (`s`)

Das häufigste sed-Kommando:

```zsh
sed 's/SUCHE/ERSETZUNG/FLAGS'
```

**Flags:**

| Flag | Bedeutung |
| ---- | --------- |
| `g` | Global: Alle Vorkommen ersetzen |
| `i` | Case-insensitive |
| `p` | Zeile ausgeben (mit `-n`) |
| `1`, `2`, ... | Nur n-tes Vorkommen ersetzen |

**Beispiele:**

```zsh
# Erstes Vorkommen pro Zeile
echo "hallo hallo" | sed 's/hallo/hi/'
# Ausgabe: hi hallo

# Alle Vorkommen
echo "hallo hallo" | sed 's/hallo/hi/g'
# Ausgabe: hi hi

# Case-insensitive
echo "Hallo HALLO" | sed 's/hallo/hi/gi'
# Ausgabe: hi hi

# Nur zweites Vorkommen
echo "a a a a" | sed 's/a/X/2'
# Ausgabe: a X a a
```

### 2.3    Trennzeichen

Das Trennzeichen muss nicht `/` sein – nützlich bei Pfaden:

```zsh
# Mit /
sed 's/\/usr\/local/\/opt/g'

# Besser: Anderes Trennzeichen
sed 's|/usr/local|/opt|g'
sed 's#/usr/local#/opt#g'
sed 's@/usr/local@/opt@g'
```

### 2.4    Gruppen und Rückreferenzen

Mit `\(...\)` (BRE) oder `(...)` (ERE) können Teile erfasst und mit `\1`, `\2` wiederverwendet werden:

```zsh
# Reihenfolge tauschen
echo "Max Mustermann" | sed -E 's/([^ ]+) ([^ ]+)/\2, \1/'
# Ausgabe: Mustermann, Max

# Datum umformatieren
echo "2024-01-15" | sed -E 's/([0-9]{4})-([0-9]{2})-([0-9]{2})/\3.\2.\1/'
# Ausgabe: 15.01.2024

# Wörter verdoppeln
echo "test" | sed -E 's/(.+)/\1 \1/'
# Ausgabe: test test

# HTML-Tags entfernen
echo "<b>Text</b>" | sed -E 's/<[^>]+>//g'
# Ausgabe: Text
```

### 2.5    Adressierung – Zeilen auswählen

Befehle können auf bestimmte Zeilen beschränkt werden:

| Adresse | Bedeutung |
| ------- | --------- |
| `5` | Nur Zeile 5 |
| `5,10` | Zeilen 5 bis 10 |
| `$` | Letzte Zeile |
| `/muster/` | Zeilen die Muster enthalten |
| `1,/muster/` | Von Zeile 1 bis erste Zeile mit Muster |

**Beispiele:**

```zsh
# Nur Zeile 3 bearbeiten
sed '3s/alt/neu/' datei.txt

# Zeilen 2-5 bearbeiten
sed '2,5s/alt/neu/' datei.txt

# Letzte Zeile bearbeiten
sed '$s/alt/neu/' datei.txt

# Nur Zeilen mit "error"
sed '/error/s/status/STATUS/' log.txt

# Ab "START" bis "END"
sed '/START/,/END/s/foo/bar/g' datei.txt
```

### 2.6    Weitere sed-Befehle

| Befehl | Bedeutung |
| ------ | --------- |
| `d` | Zeile löschen |
| `p` | Zeile ausgeben |
| `a\TEXT` | Text nach Zeile einfügen |
| `i\TEXT` | Text vor Zeile einfügen |
| `c\TEXT` | Zeile ersetzen |
| `y/abc/xyz/` | Zeichen transliterieren |

**Beispiele:**

```zsh
# Zeilen löschen
sed '3d' datei.txt              # Zeile 3 löschen
sed '/^#/d' config.txt          # Kommentare löschen
sed '/^$/d' datei.txt           # Leere Zeilen löschen

# Zeilen ausgeben (mit -n)
sed -n '5p' datei.txt           # Nur Zeile 5 ausgeben
sed -n '10,20p' datei.txt       # Zeilen 10-20
sed -n '/error/p' log.txt       # Nur Zeilen mit "error"

# Text einfügen
sed '3a\Neue Zeile' datei.txt   # Nach Zeile 3
sed '1i\Kopfzeile' datei.txt    # Vor Zeile 1

# Zeichen ersetzen (wie tr)
echo "hello" | sed 'y/aeiou/AEIOU/'
# Ausgabe: hEllO
```

### 2.7    Mehrere Befehle

```zsh
# Mit -e
sed -e 's/foo/bar/' -e 's/baz/qux/' datei.txt

# Mit Semikolon
sed 's/foo/bar/; s/baz/qux/' datei.txt

# Mehrzeilig
sed '
  s/foo/bar/g
  s/baz/qux/g
  /^#/d
' datei.txt
```

### 2.8    In-place-Bearbeitung

```zsh
# macOS: Leeres Backup-Suffix erforderlich
sed -i '' 's/alt/neu/g' datei.txt

# Mit Backup
sed -i '.bak' 's/alt/neu/g' datei.txt

# Linux: Kein Suffix nötig
sed -i 's/alt/neu/g' datei.txt
```

### 2.9    Praktische sed-Beispiele

```zsh
# Leerzeichen am Zeilenende entfernen
sed 's/[[:space:]]*$//' datei.txt

# Mehrere Leerzeichen zu einem
sed -E 's/ +/ /g' datei.txt

# Erste Zeile jeder Datei (wie head -1)
sed -n '1p' *.txt

# Zeilen 5-10 löschen
sed '5,10d' datei.txt

# Jede Zeile nummerieren
sed = datei.txt | sed 'N; s/\n/\t/'

# Zwischen zwei Markern ersetzen
sed '/BEGIN/,/END/s/old/new/g' datei.txt

# Konfigurationswert ändern
sed -E 's/^(port=).*/\18080/' config.txt

# CSV: Spaltenreihenfolge ändern
sed -E 's/([^,]+),([^,]+),([^,]+)/\3,\1,\2/' data.csv
```

## 3    `awk` – Textanalyse und Berichte

`awk` ist eine vollständige Programmiersprache für Textverarbeitung. Es eignet sich besonders für spaltenbasierte Daten wie CSV, Logs und tabellarische Ausgaben.

### 3.1    Grundsyntax

```zsh
awk [OPTIONEN] 'PROGRAMM' [DATEI]
```

Ein awk-Programm besteht aus Regeln:

```
MUSTER { AKTION }
```

- Zeilen die dem MUSTER entsprechen, führen die AKTION aus
- Ohne MUSTER: Aktion auf alle Zeilen
- Ohne AKTION: Zeile ausgeben

### 3.2    Felder und Variablen

`awk` teilt jede Zeile automatisch in Felder:

| Variable | Bedeutung |
| -------- | --------- |
| `$0` | Gesamte Zeile |
| `$1` | Erstes Feld |
| `$2` | Zweites Feld |
| `$NF` | Letztes Feld |
| `$(NF-1)` | Vorletztes Feld |
| `NF` | Anzahl der Felder |
| `NR` | Aktuelle Zeilennummer |
| `FNR` | Zeilennummer in aktueller Datei |
| `FS` | Feldtrenner (Standard: Whitespace) |
| `OFS` | Ausgabe-Feldtrenner |
| `RS` | Zeilentrenner |
| `ORS` | Ausgabe-Zeilentrenner |

**Beispiele:**

```zsh
# Erste Spalte ausgeben
echo "Max 30 Berlin" | awk '{print $1}'
# Ausgabe: Max

# Mehrere Spalten
echo "Max 30 Berlin" | awk '{print $1, $3}'
# Ausgabe: Max Berlin

# Spalten in anderer Reihenfolge
echo "Max 30 Berlin" | awk '{print $3, $1, $2}'
# Ausgabe: Berlin Max 30

# Letzte Spalte
echo "a b c d e" | awk '{print $NF}'
# Ausgabe: e

# Anzahl Felder
echo "a b c d e" | awk '{print NF}'
# Ausgabe: 5
```

### 3.3    Feldtrenner ändern

```zsh
# Mit -F
awk -F',' '{print $1}' datei.csv
awk -F':' '{print $1, $3}' /etc/passwd

# Im Programm
awk 'BEGIN{FS=","} {print $1}' datei.csv

# Mehrere Trennzeichen
awk -F'[,;:]' '{print $1}' datei.txt

# Tab als Trenner
awk -F'\t' '{print $2}' datei.tsv
```

### 3.4    Muster (Pattern)

```zsh
# Zeilen mit "error"
awk '/error/' log.txt

# Zeilen die NICHT "comment" enthalten
awk '!/comment/' datei.txt

# Zeilen wo Feld 3 größer als 100
awk '$3 > 100' daten.txt

# Zeilen wo Feld 1 einem Muster entspricht
awk '$1 ~ /^A/' namen.txt

# Kombination
awk '/error/ && $3 > 5' log.txt

# Bereichsmuster
awk '/START/,/END/' datei.txt
```

### 3.5    BEGIN und END

`BEGIN` wird vor der ersten Zeile ausgeführt, `END` nach der letzten:

```zsh
# Kopfzeile ausgeben
awk 'BEGIN{print "Name\tAlter"} {print $1, $2}' daten.txt

# Summe berechnen
awk '{sum += $1} END{print "Summe:", sum}' zahlen.txt

# Zeilen zählen
awk 'END{print NR, "Zeilen"}' datei.txt

# Durchschnitt berechnen
awk '{sum += $1; count++} END{print "Durchschnitt:", sum/count}' zahlen.txt
```

### 3.6    Formatierte Ausgabe mit `printf`

```zsh
# Grundsyntax
awk '{printf "%-10s %5d\n", $1, $2}' daten.txt
```

**Format-Spezifizierer:**

| Format | Bedeutung |
| ------ | --------- |
| `%s` | String |
| `%d` | Integer |
| `%f` | Fließkommazahl |
| `%.2f` | 2 Dezimalstellen |
| `%10s` | 10 Zeichen breit, rechtsbündig |
| `%-10s` | 10 Zeichen breit, linksbündig |

**Beispiele:**

```zsh
# Tabelle formatieren
awk '{printf "%-15s %8.2f\n", $1, $2}' preise.txt

# Mit Kopfzeile
awk 'BEGIN{printf "%-15s %8s\n", "Produkt", "Preis"; print "------------------------"}
     {printf "%-15s %8.2f\n", $1, $2}' preise.txt
```

### 3.7    Variablen und Berechnungen

```zsh
# Eigene Variablen
awk '{total = $2 * $3; print $1, total}' bestellung.txt

# Externe Variablen übergeben
threshold=100
awk -v limit="$threshold" '$2 > limit {print}' daten.txt

# Mehrere Variablen
awk -v min=10 -v max=50 '$1 >= min && $1 <= max' zahlen.txt
```

### 3.8    Kontrollstrukturen

**if-else:**

```zsh
awk '{
    if ($3 > 100)
        print $1, "hoch"
    else if ($3 > 50)
        print $1, "mittel"
    else
        print $1, "niedrig"
}' daten.txt
```

**for-Schleife:**

```zsh
# Alle Felder ausgeben
awk '{for(i=1; i<=NF; i++) print i, $i}' datei.txt

# Felder rückwärts
awk '{for(i=NF; i>=1; i--) printf "%s ", $i; print ""}' datei.txt
```

**while-Schleife:**

```zsh
awk '{
    i = 1
    while(i <= NF) {
        print $i
        i++
    }
}' datei.txt
```

### 3.9    Arrays

```zsh
# Werte zählen
awk '{count[$1]++} END{for(key in count) print key, count[key]}' log.txt

# Summen nach Kategorie
awk '{sum[$1] += $2} END{for(cat in sum) print cat, sum[cat]}' verkauf.txt

# Duplikate finden
awk 'seen[$0]++' datei.txt

# Einzigartige Zeilen (wie uniq)
awk '!seen[$0]++' datei.txt
```

### 3.10    Eingebaute Funktionen

**String-Funktionen:**

| Funktion | Bedeutung |
| -------- | --------- |
| `length(s)` | Länge des Strings |
| `substr(s,i,n)` | Substring ab Position i, n Zeichen |
| `index(s,t)` | Position von t in s |
| `split(s,a,fs)` | String in Array aufteilen |
| `sub(r,s)` | Erstes Vorkommen ersetzen |
| `gsub(r,s)` | Alle Vorkommen ersetzen |
| `tolower(s)` | In Kleinbuchstaben |
| `toupper(s)` | In Großbuchstaben |

**Beispiele:**

```zsh
# Stringlänge
echo "Hallo Welt" | awk '{print length($0)}'
# Ausgabe: 10

# Substring
echo "Hallo Welt" | awk '{print substr($0, 1, 5)}'
# Ausgabe: Hallo

# Ersetzen
echo "foo bar foo" | awk '{gsub(/foo/, "baz"); print}'
# Ausgabe: baz bar baz

# Großbuchstaben
echo "hallo" | awk '{print toupper($0)}'
# Ausgabe: HALLO
```

**Mathematische Funktionen:**

| Funktion | Bedeutung |
| -------- | --------- |
| `int(x)` | Ganzzahliger Anteil |
| `sqrt(x)` | Quadratwurzel |
| `sin(x)`, `cos(x)` | Trigonometrische Funktionen |
| `log(x)` | Natürlicher Logarithmus |
| `exp(x)` | e^x |
| `rand()` | Zufallszahl 0–1 |

### 3.11    Praktische awk-Beispiele

```zsh
# CSV: Bestimmte Spalten extrahieren
awk -F',' '{print $1, $3}' data.csv

# Summe einer Spalte
awk '{sum += $2} END{print sum}' zahlen.txt

# Durchschnitt
awk '{sum += $1; n++} END{print sum/n}' zahlen.txt

# Maximum finden
awk 'BEGIN{max=0} $1>max{max=$1} END{print max}' zahlen.txt

# Zeilen mit mehr als 3 Feldern
awk 'NF > 3' datei.txt

# Jede zweite Zeile
awk 'NR % 2 == 0' datei.txt

# Zeilen zwischen zwei Markern (ohne Marker)
awk '/START/{flag=1; next} /END/{flag=0} flag' datei.txt

# IP-Adressen extrahieren aus Log
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head

# Dateigröße pro Verzeichnis
ls -l | awk '/^-/{sum += $5} END{print "Total:", sum, "Bytes"}'

# Spalten vertauschen
awk '{print $2, $1, $3}' datei.txt

# Bestimmte Zeilen und Spalten
awk 'NR >= 5 && NR <= 10 {print $1, $3}' datei.txt

# Feldtrenner für Ausgabe setzen
awk 'BEGIN{OFS=","} {print $1, $2, $3}' datei.txt

# Top 10 häufigste Werte
awk '{print $1}' datei.txt | sort | uniq -c | sort -rn | head -10
```

## 4    Kombinationsbeispiele (Pipe-Workflows)

Die wahre Stärke von `sed`, `awk` und Regex zeigt sich in Kombination mit anderen Unix-Tools.

### 4.1    Log-Analyse

**Fehler aus Log extrahieren und zählen:**

```zsh
grep -i "error" application.log | \
    awk '{print $4}' | \
    sort | uniq -c | sort -rn | head -10
```

**Zugriffe pro Stunde aus Access-Log:**

```zsh
awk '{print $4}' access.log | \
    cut -d: -f2 | \
    sort | uniq -c | \
    awk '{print $2":00", $1}'
```

**IP-Adressen mit den meisten Fehlern:**

```zsh
grep " 404 \| 500 " access.log | \
    awk '{print $1}' | \
    sort | uniq -c | sort -rn | head -10
```

**Langsame Requests finden (> 5 Sekunden):**

```zsh
awk '$NF > 5000 {print $7, $NF"ms"}' access.log | \
    sort -t' ' -k2 -rn | head -20
```

### 4.2    Dateitransformationen

**CSV zu TSV konvertieren:**

```zsh
sed 's/,/\t/g' data.csv > data.tsv
```

**JSON-Lines zu CSV (einfach):**

```zsh
cat data.jsonl | \
    sed 's/[{}":]//g' | \
    sed 's/,/\t/g'
```

**Konfigurationsdatei bereinigen:**

```zsh
# Kommentare und leere Zeilen entfernen, sortieren
grep -v '^#' config.txt | \
    grep -v '^$' | \
    sed 's/[[:space:]]*$//' | \
    sort
```

**Markdown-Überschriften extrahieren:**

```zsh
grep -E "^#{1,3} " document.md | \
    sed 's/^#* //' | \
    awk '{printf "%d. %s\n", NR, $0}'
```

### 4.3    System-Administration

**Disk-Usage sortiert nach Größe:**

```zsh
du -sh * 2>/dev/null | \
    sort -rh | head -20
```

**Größte Dateien finden:**

```zsh
find . -type f -exec ls -la {} \; 2>/dev/null | \
    awk '{print $5, $9}' | \
    sort -rn | head -20
```

**Prozesse nach Memory-Verbrauch:**

```zsh
ps aux | \
    awk 'NR>1 {print $4, $11}' | \
    sort -rn | head -10
```

**Offene Ports auflisten:**

```zsh
netstat -an | \
    grep LISTEN | \
    awk '{print $4}' | \
    sed 's/.*\.//' | \
    sort -n | uniq
```

### 4.4    Textbereinigung

**Mehrfache Leerzeichen und -zeilen bereinigen:**

```zsh
cat messy.txt | \
    sed 's/[[:space:]]\+/ /g' | \
    sed 's/^[[:space:]]//' | \
    sed 's/[[:space:]]$//' | \
    cat -s
```

**HTML-Tags entfernen:**

```zsh
sed -E 's/<[^>]+>//g' page.html | \
    sed '/^$/d' | \
    sed 's/&nbsp;/ /g; s/&amp;/\&/g; s/&lt;/</g; s/&gt;/>/g'
```

**E-Mail-Adressen extrahieren:**

```zsh
grep -oE '[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}' dokument.txt | \
    sort -u
```

**URLs extrahieren:**

```zsh
grep -oE 'https?://[^[:space:]"<>]+' webpage.html | \
    sort -u
```

### 4.5    Datenanalyse

**CSV-Statistiken:**

```zsh
# Spalte 3: Min, Max, Durchschnitt
awk -F',' 'NR>1 {
    sum += $3; count++;
    if(min == "" || $3 < min) min = $3;
    if(max == "" || $3 > max) max = $3
} END {
    print "Min:", min
    print "Max:", max
    print "Avg:", sum/count
}' data.csv
```

**Häufigkeit von Werten in Spalte:**

```zsh
awk -F',' 'NR>1 {count[$2]++} END {
    for(val in count) print count[val], val
}' data.csv | sort -rn
```

**Gruppierte Summen:**

```zsh
awk -F',' 'NR>1 {sum[$1] += $3} END {
    for(group in sum) print group, sum[group]
}' sales.csv | sort -t' ' -k2 -rn
```

### 4.6    Batch-Umbenennung

**Dateinamen bereinigen (Leerzeichen durch Unterstriche):**

```zsh
for f in *\ *; do
    mv "$f" "$(echo $f | sed 's/ /_/g')"
done
```

**Dateien mit Datum versehen:**

```zsh
for f in *.txt; do
    mv "$f" "$(date +%Y%m%d)_$f"
done
```

**Erweiterung ändern:**

```zsh
for f in *.jpeg; do
    mv "$f" "${f%.jpeg}.jpg"
done

# Oder mit sed
ls *.jpeg | sed 's/\(.*\)\.jpeg/mv "\1.jpeg" "\1.jpg"/' | sh
```

### 4.7    Komplexe Pipeline-Beispiele

**Git: Änderungen pro Autor (letzte 30 Tage):**

```zsh
git log --since="30 days ago" --format='%an' | \
    sort | uniq -c | sort -rn
```

**Apache-Log: Requests pro Minute visualisieren:**

```zsh
awk '{print $4}' access.log | \
    cut -d: -f1-3 | \
    uniq -c | \
    awk '{printf "%s %s\n", $2, substr("====================", 1, $1/10)}'
```

**Wort-Frequenz-Analyse:**

```zsh
cat text.txt | \
    tr '[:upper:]' '[:lower:]' | \
    tr -cs '[:alpha:]' '\n' | \
    sort | uniq -c | sort -rn | head -20
```

**JSON-Werte aggregieren (mit jq und awk):**

```zsh
cat data.json | \
    jq -r '.[] | "\(.category) \(.amount)"' | \
    awk '{sum[$1] += $2} END {for(c in sum) print c, sum[c]}' | \
    sort -k2 -rn
```

### 4.8    Debugging von Pipelines

**Schritt-für-Schritt testen:**

```zsh
# Erst einzeln testen
cat log.txt | head
cat log.txt | grep "error" | head
cat log.txt | grep "error" | awk '{print $1}' | head
# ... dann erweitern
```

**Zwischenergebnisse mit tee:**

```zsh
cat data.txt | \
    tee /dev/stderr | \     # Zeigt Zwischenergebnis
    grep "pattern" | \
    tee step1.txt | \       # Speichert Zwischenergebnis
    awk '{print $2}'
```

**Pipeline mit Zeilennummern debuggen:**

```zsh
cat -n datei.txt | grep "muster"
```
