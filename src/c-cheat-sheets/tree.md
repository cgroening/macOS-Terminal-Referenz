# tree


| Befehl                                      | Beschreibung                           |
| ------------------------------------------- | -------------------------------------- |
| `tree`                                      | Verzeichnisbaum anzeigen               |
| `tree -L 2`                                 | Nur 2 Ebenen tief                      |
| `tree -a`                                   | Auch versteckte Dateien anzeigen       |
| `tree -d`                                   | Nur Verzeichnisse anzeigen             |
| `tree -I "node_modules"`                    | Ordner ausschließen                    |
| `tree -I "*.pyc"`                           | Dateien nach Muster ausschließen       |
| `tree -I "node_modules\|.git\|__pycache__"` | Mehrere Muster ausschließen            |
| `tree -P "*.py"`                            | Nur bestimmte Dateien anzeigen         |
| `tree -h`                                   | Dateigrößen anzeigen (human-readable)  |
| `tree -s`                                   | Dateigrößen in Bytes anzeigen          |
| `tree -D`                                   | Änderungsdatum anzeigen                |
| `tree -p`                                   | Berechtigungen anzeigen                |
| `tree -u`                                   | Besitzer anzeigen                      |
| `tree -f`                                   | Volle Pfade anzeigen                   |
| `tree -o output.txt`                        | In Datei speichern                     |
| `tree --gitignore`                          | .gitignore-Regeln beachten             |
| `tree -C`                                   | Farbige Ausgabe (Standard im Terminal) |
| `tree -n`                                   | Keine Farben                           |
