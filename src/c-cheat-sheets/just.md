# just


| Befehl                    | Beschreibung                          |
| ------------------------- | ------------------------------------- |
| `just`                    | Standard-Rezept ausführen             |
| `just recipe`             | Bestimmtes Rezept ausführen           |
| `just -l` / `just --list` | Alle Rezepte auflisten                |
| `just --show recipe`      | Rezept-Definition anzeigen            |
| `just -n recipe`          | Dry-run – zeigt was ausgeführt würde  |
| `just --choose`           | Rezept interaktiv auswählen (mit fzf) |
| `just -f other.just`      | Anderes Justfile verwenden            |
| `just --summary`          | Kurze Übersicht aller Rezepte         |
| `just --evaluate`         | Alle Variablen ausgeben               |
| `just recipe arg1 arg2`   | Rezept mit Argumenten aufrufen        |
| `just --init`             | Neues Justfile erstellen              |

**Beispiel `justfile`:**

```just
# Standard-Rezept
default:
    @just --list

# Projekt bauen
build:
    cargo build --release

# Tests ausführen
test:
    cargo test

# Formatieren und linten
check:
    cargo fmt --check
    cargo clippy

# Aufräumen
clean:
    cargo clean
```
