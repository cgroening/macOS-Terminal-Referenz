# Python

## `python`

| Befehl | Beschreibung |
| ------ | ------------ |
| `python --version` | Python-Version prüfen |
| `python -c "print('Hello')"` | Python-Code direkt ausführen |
| `python script.py` | Skript ausführen |
| `python -m module_name` | Modul als Skript ausführen |
| `python -m venv .venv` | Virtuelle Umgebung erstellen |
| `python -i script.py` | Skript ausführen, danach interaktiv bleiben |
| `python -m http.server 8000` | Einfachen Webserver starten |
| `python -m json.tool file.json` | JSON formatiert ausgeben |

## `conda`

| Befehl                                    | Beschreibung                        |
| ----------------------------------------- | ----------------------------------- |
| `conda --version`                         | Conda-Version prüfen                |
| `conda env list`                          | Umgebungen auflisten                |
| `conda activate py310`                    | Umgebung aktivieren                 |
| `conda deactivate`                        | Umgebung deaktivieren               |
| `conda create -n py310 python=3.10`       | Umgebung erstellen                  |
| `conda create --name py312 --clone py310` | Umgebung klonen                     |
| `conda remove -n py310 --all`             | Umgebung löschen                    |
| `conda install numpy`                     | Paket installieren                  |
| `conda install python=3.12`               | Python-Version aktualisieren        |
| `conda update --all`                      | Alle Pakete aktualisieren           |
| `conda update conda`                      | Conda selbst aktualisieren          |
| `conda list`                              | Installierte Pakete anzeigen        |
| `conda search numpy`                      | Nach Paketen suchen                 |
| `conda info`                              | Conda-Informationen anzeigen        |
| `conda clean --all`                       | Cache und ungenutzte Pakete löschen |
| `conda env export > environment.yml`      | Umgebung exportieren                |
| `conda env create -f environment.yml`     | Umgebung aus Datei erstellen        |

## `pip`

| Befehl | Beschreibung |
| ------ | ------------ |
| `pip --version` | pip-Version prüfen |
| `pip install package` | Paket installieren |
| `pip install package==1.2.3` | Bestimmte Version installieren |
| `pip install -U package` | Paket aktualisieren |
| `pip install -r requirements.txt` | Alle Pakete aus Datei installieren |
| `pip uninstall package` | Paket deinstallieren |
| `pip list` | Installierte Pakete anzeigen |
| `pip list --outdated` | Veraltete Pakete anzeigen |
| `pip show package` | Paket-Informationen anzeigen |
| `pip search package` | Nach Paketen suchen (oft deaktiviert) |
| `pip freeze > requirements.txt` | Installierte Pakete exportieren |
| `pip cache purge` | pip-Cache leeren |
| `pip install -e .` | Paket im Entwicklungsmodus installieren |
| `pip install --user package` | Nur für aktuellen Benutzer installieren |
