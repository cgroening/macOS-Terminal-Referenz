# make

| Befehl             | Beschreibung                            |
| ------------------ | --------------------------------------- |
| `make`             | Standard-Target ausführen (meist `all`) |
| `make target`      | Bestimmtes Target ausführen             |
| `make -n target`   | Dry-run – zeigt was ausgeführt würde    |
| `make -j4`         | Parallel mit 4 Jobs bauen               |
| `make -j$(nproc)`  | Parallel mit allen CPUs bauen           |
| `make -f other.mk` | Anderes Makefile verwenden              |
| `make -B`          | Alles neu bauen (ignore timestamps)     |
| `make clean`       | Aufräumen (falls Target definiert)      |
| `make -p`          | Alle Variablen und Regeln anzeigen      |
| `make VAR=value`   | Variable beim Aufruf setzen             |
