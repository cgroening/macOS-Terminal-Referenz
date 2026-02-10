# Berechtigungen (`chmod`) und Besitz (`chown`)

## 1    `chmod`

### 1.1    Rechte und Benutzergruppen

`chmod` steht für *change mode*. Mit diesem Befehl werden Datei- und Verzeichnisrechte geändert.

Es gibt drei Arten von Rechten:

1. **`r`**: read (lesen)
2. **`w`**: write (schreiben)
3. **`x`**: execute (ausführen)

Man unterscheidet drei Benutzergruppen:

1. **`u`**: user (Besitzer)
2. **`g`**: group (Gruppe)
3. **`o`**: others (alle anderen)
- **`a`**: all (alle drei Gruppen: `u+g+o`)

### 1.2    Schreibweisen

#### 1.2.1    Symbolische Schreibweise

Es werden Buchstaben und Operatoren verwendet:

- `+`: Recht hinzufügen
- `-`: Recht entfernen
- `=`: Rechte gleichsetzen

**Beispiele:**

```zsh
chmod u+x datei.sh    # Besitzer darf die Datei ausführen
chmod g-w datei.txt   # Gruppe darf nicht mehr schreiben
chmod o=r datei.txt   # Andere dürfen nur lesen
chmod a+r datei.txt   # Alle dürfen lesen
```

#### 1.2.2    Oktal-/Numerische Schreibweise

Jedes Recht hat einen Zahlenwert:

| **Recht** | **Wert** |
| --------- | -------- |
| `r`       | `4`        |
| `w`       | `2`        |
| `x`       | `1`        |

Man addiert die Werte für jede Gruppe um Rechte zu setzen:

```
---    →    0+0+0 = 0    (keine Rechte)
--x    →    0+0+1 = 1    (nur ausführen)
-w-    →    0+2+0 = 2    (nur schreiben)
-wx    →    0+2+1 = 3    (schreiben + ausführen)
r--    →    4+0+0 = 4    (nur lesen)
r-x    →    4+0+1 = 5    (lesen + ausführen)
rw-    →    4+2+0 = 6    (lesen + schreiben)
rwx    →    4+2+1 = 7    (lesen + schreiben + ausführen)
```

**Beispiel:**

```zsh
chmod 755 datei.sh
```

Bedeutung:

- **`7` (`u`)** → `rwx` → Besitzer darf alles
- **`5` (`g`)** → `r-x` → Gruppe darf lesen & ausführen
- **`5` (`o`)** → `r-x` → andere dürfen lesen & ausführen

### 1.3    Nützliche Operationen

**`-R` → rekursiv (für Verzeichnisse):**

```zsh
chmod -R 755 /pfad/zum/verzeichnis
```

**`--reference=DATEI` → Rechte von einer anderen Datei kopieren:**

```zsh
chmod --reference=vorlage.txt ziel.txt
```

**`-v` → verbose, zeigt was geändert wurde:**

```zsh
chmod -v 644 datei.txt
```

## 2    `chown`

`chown` steht für *change owner*. Dieser Befehl ändern den Besitzer oder die Gruppe einer Datei bzw. eines Verzeichnisses.

Jede Datei hat

- einen `user` (Besitzer) und
- eine `group` (Gruppe).

### 2.1    Syntax

```zsh
chown [OPTIONEN] BESITZER[:GRUPPE] DATEI/VERZEICHNIS
```

- `BESITZER`: neuer Benutzer, der die Datei besitzen soll
- `GRUPPE`: neue Gruppe, der die Datei angehören soll
- `:` → trennt Besitzer und Gruppe

> [!INFO]
> Nur Root oder der Besitzer selbst kann `chown` ausführen. Normale Nutzer können nicht einfach Dateien anderen Nutzern zuweisen. In dem Fall muss der Befehl mit Admin-Rechten ausgeführt werden:
>
> ```zsh
> sudo chown ...
> ```

**Beispiele:**

```zsh
chown anna datei.txt             # Ändert den Besitzer auf anna
chown :developers datei.txt      # Ändert die Gruppe auf developers
chown anna:developers datei.txt  # Ändert Besitzer auf anna und Gruppe auf developers
```

### 2.2    Wichtige Optionen

| **Option**          | **Bedeutung**                                     |
| ------------------- | ------------------------------------------------- |
| `-R`                | rekursiv, für Verzeichnisse und deren Inhalte     |
| `-v`                | verbose, zeigt Änderungen an                      |
| `--reference=DATEI` | setzt Besitzer/Gruppe wie bei einer Referenzdatei |

**Beispiel für Rekursiv:**

```zsh
chown -R anna:developers /home/anna/projekt
```

Alle Dateien und Unterverzeichnisse gehören danach dem Benutzer `anna` und der Gruppe `developers`.

**Beispiel für `--reference`:**

```zsh
chown --reference=vorlage.txt ziel.txt
```

Besitzer & Gruppe wie bei `vorlage.txt` gesetzt.

> [!NOTE]
> Die Standardgruppe für normale Benutzer (nicht für Systemprozesse) lautet `staff`.

## 3    ACL

ACL steht für *Access Control List* (Zugriffssteuerungsliste). Sie erweitert das klassische UNIX-Berechtigungssystem (Lesen, Schreiben, Ausführen für Benutzer / Gruppe / andere) um feinere Zugriffsrechte.

Während das herkömmliche System nur drei Berechtigungsebenen kennt (z. B. `rw-r--r--`), kann eine ACL mehreren einzelnen Benutzern oder Gruppen ganz spezifische Rechte zuweisen.

> [!NOTE]
> ACLs sind eine erweiterte Form von Dateiberechtigungen, mit denen man mehr Kontrolle darüber hat, wer was mit einer Datei tun darf.

**Beispiel:**

Angenommen man hat die folgende Datei:

```zsh
-rw-r--r--  user  staff  dokument.txt
```

Es darf nur `user` schreiben, alle anderen dürfen nur lesen. Mit einer ACL kann man z. B. einem zusätzlichen andern Benutzer wie `john` Schreibrechte geben:

```zsh
chmod +a "alex allow write" dokument.txt
```

Der Befehl `ls -le` liefert dadurch die folgende Ausgabe:

```zsh
-rw-r--r--+  user  staff  dokument.txt
  0: john allow write
```

Das Pluszeichen (`+`) hinter den Berechtigungen zeigt an, dass eine ACL vorhanden ist.

### 3.1    Typische Befehle

| Befehl                             | Bedeutung                                 |
| ---------------------------------- | ----------------------------------------- |
| `ls -le`                           | Zeigt ACL-Einträge für Dateien und Ordner |
| `chmod +a "user allow permission"` | Fügt einen ACL-Eintrag hinzu              |
| `chmod -a "user"`                  | Entfernt einen ACL-Eintrag                |
| `chmod -N datei`                   | Entfernt alle ACLs von einer Datei        |
