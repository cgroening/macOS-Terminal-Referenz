# Beispiele für alltägliche Aufgaben im Terminal

## 1    Dateien suchen

Im aktuellen Ordner sollen alle Dateien vom Typ `PDF` gefunden werden.
### 1.1    GUI

- Finder öffnen :luc_arrow_right: im Eingabefeld für die Suche rechts oben ".pdf" eingeben
- Weitere Verfeinerungen über Filter möglich (`+`-Button)

### 1.2    Terminal

```zsh
find . -name "*.pdf"
```

:luc_arrow_right: Listet alle PDF-Dateien (auch in Unterordnern)

Bis hier ist der Aufwand gleich. Das Terminal ist jedoch deutlich schneller, wenn zusätzlich alle Dateien gefunden werden sollen, die einen bestimmten Dateiinhalt haben.

**Beispiel:**

```zsh
find . -name "*.pdf" -exec grep -Hn "Rechnung" {} \
```

## 2    Zuletzt bearbeitete Dateien anzeigen

Es sollen alle `JPEG`-Dateien eines Ordners angezeigt werden, die in den letzten 10 Minuten bearbeitet wurden. Anschließend sollen diese in der Vorschau-App geöffnet werden.

### 2.1    GUI

1. Im Finder in die Listenansicht wechseln und die Spalte "Änderungsdatum" anzeigen lassen, falls noch nicht vorhanden
2. Auf die Spaltenüberschrift klicken, um nach Änderungsdatum zu sortieren
3. Die Datei mit dem ältesten Änderungsdatum, das in Frage kommt, suchen
4. Auswählen von dieser Datei und allen, die sich davor/dahinter befinden
5. Rechtsklick :luc_arrow_right: Öffnen mit :luc_arrow_right: Vorschau.app
6. Zurück zum Finder wechseln und alle ursprünglichen Darstellungseinstellungen wiederherstellen

### 2.2    Terminal

Alle `JPEG`-Dateien anzeigen, die nicht älter als 10 Minuten sind:

```zsh
find . -type f -iname "*.jpeg" -mmin -10
```

Sollen Dateien mit den Endungen `.jpg` und `.jpeg` angezeigt werden, muss der Befehl wie folgt erweitert werden:

```zsh
find . -type f \( -iname "*.jpg" -o -iname "*.jpeg" \) -mmin -10
```

Genau diese Dateien in der Vorschau-App öffnen:

```zsh
find . -type f -iname "*.jpeg" -mmin -10 -print0 | xargs -0 open -a Preview
```

#### 2.2.1    Alias

Besonders schnell und einfach erfolgt es, wenn man sich einen Alias erstellt:

```zsh
alias showrecent='find . -type f -iname "*.jpg" -mmin $1'
```

Komfortabler ist es in diesem Fall, wenn man stattdessen Funktionen erstellt:

```zsh
showrecent () {
	echo "The following files were modified in the past $1 minutes:"
	find . -type f -iname "*.*" -mmin -$1
}

openrecentjpg () {
	echo "Opening the following files in preview:"
	find . -type f \( -iname "*.jpg" -o -iname "*.jpeg" \) -mmin -$1
	find . -type f \( -iname "*.jpg" -o -iname "*.jpeg" \) -mmin -$1 -print0 | xargs -0 open -a Preview
}
```

**Verwendung:**

```zsh
showrecent 10
```

```zsh
openrecentjpg 10
```

**Beispielausgabe:**

```
ls

IMG_7094.jpeg		IMG_7096 Kopie.jpeg	IMG_7097.jpeg		IMG_7099.jpeg
IMG_7095.jpeg		IMG_7096.jpeg		IMG_7099 Kopie.jpeg
```

```
showrecent 10

The following files were modified in the past 10 minutes:
./IMG_7099 Kopie.jpeg
./IMG_7096 Kopie.jpeg
```

```
openrecentjpg 10

Opening the following files in preview:
./IMG_7099 Kopie.jpeg
./IMG_7096 Kopie.jpeg
```

> [!TIP]
> Sollte ein Funktionsname verwendet werden sollen, der zuvor ein Alias war (z. B. `alias showrecent=...` :luc_arrow_right: `showrecent() ...`) muss zuvor der folgende Befehl ausgeführt werden:
>
> ```zsh
> unalias <aliasname>
> ```
