# Rust

## `rustup`

| Befehl                               | Beschreibung                      |
| ------------------------------------ | --------------------------------- |
| `rustup --version`                   | rustup-Version prüfen             |
| `rustup show`                        | Installierte Toolchains anzeigen  |
| `rustup update`                      | Rust aktualisieren                |
| `rustup default stable`              | Standard-Toolchain setzen         |
| `rustup default nightly`             | Nightly als Standard setzen       |
| `rustup toolchain list`              | Installierte Toolchains auflisten |
| `rustup toolchain install nightly`   | Nightly installieren              |
| `rustup toolchain uninstall nightly` | Toolchain entfernen               |
| `rustup component add clippy`        | Komponente hinzufügen             |
| `rustup component add rustfmt`       | Formatter hinzufügen              |
| `rustup component list`              | Verfügbare Komponenten anzeigen   |
| `rustup doc`                         | Rust-Dokumentation öffnen         |
| `rustup doc --std`                   | Standardbibliothek-Doku öffnen    |
| `rustup override set nightly`        | Toolchain für Projekt setzen      |
| `rustup self update`                 | rustup selbst aktualisieren       |

## `cargo`

### Projekt erstellen & verwalten

| Befehl | Beschreibung |
| ------ | ------------ |
| `cargo new project_name` | Neues Projekt erstellen (mit Git) |
| `cargo new --lib library_name` | Neue Bibliothek erstellen |
| `cargo init` | Projekt im aktuellen Ordner initialisieren |
| `cargo init --lib` | Bibliothek im aktuellen Ordner initialisieren |

### Bauen & Ausführen

| Befehl | Beschreibung |
| ------ | ------------ |
| `cargo build` | Projekt kompilieren (Debug) |
| `cargo build --release` | Release-Build erstellen |
| `cargo run` | Kompilieren und ausführen |
| `cargo run --release` | Release-Build ausführen |
| `cargo run -- arg1 arg2` | Mit Argumenten ausführen |
| `cargo run --bin binary_name` | Bestimmte Binary ausführen |
| `cargo run --example example_name` | Beispiel ausführen |

### Testen & Prüfen

| Befehl | Beschreibung |
| ------ | ------------ |
| `cargo test` | Alle Tests ausführen |
| `cargo test test_name` | Bestimmten Test ausführen |
| `cargo test --release` | Tests im Release-Modus |
| `cargo test -- --nocapture` | Ausgabe nicht unterdrücken |
| `cargo bench` | Benchmarks ausführen |
| `cargo check` | Kompilierbarkeit prüfen (schnell) |

### Dependencies

| Befehl | Beschreibung |
| ------ | ------------ |
| `cargo add package` | Dependency hinzufügen |
| `cargo add package@1.2.3` | Bestimmte Version hinzufügen |
| `cargo add package --dev` | Dev-Dependency hinzufügen |
| `cargo remove package` | Dependency entfernen |
| `cargo update` | Dependencies aktualisieren |
| `cargo tree` | Dependency-Baum anzeigen |
| `cargo search package` | Nach Crates suchen |

### Code-Qualität

| Befehl | Beschreibung |
| ------ | ------------ |
| `cargo fmt` | Code formatieren |
| `cargo fmt --check` | Formatierung prüfen |
| `cargo clippy` | Linter ausführen |
| `cargo clippy --fix` | Lint-Fehler automatisch beheben |
| `cargo fix` | Compiler-Warnungen beheben |

### Dokumentation & Infos

| Befehl | Beschreibung |
| ------ | ------------ |
| `cargo doc` | Dokumentation generieren |
| `cargo doc --open` | Dokumentation generieren und öffnen |
| `cargo doc --no-deps` | Ohne Dependencies dokumentieren |

### Aufräumen & Sonstiges

| Befehl | Beschreibung |
| ------ | ------------ |
| `cargo clean` | Build-Artefakte löschen |
| `cargo publish` | Crate auf crates.io veröffentlichen |
| `cargo install package` | Binary global installieren |
| `cargo uninstall package` | Binary deinstallieren |
