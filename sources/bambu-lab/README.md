# Offizielle Bambu-Lab-PLA-Farben und Farbmischungen

Abruf: **13.11.2026**. Quelle: Bambu Labs eigenes BambuStudio-Repository, festgehalten auf Commit [930fe93851afbc76b1c64dd37d8728b6a9afc89c](https://github.com/bambulab/BambuStudio/blob/930fe93851afbc76b1c64dd37d8728b6a9afc89c/). Keine Community-Farbliste.

## Dateien

- [PLA-Farben als JSON](../../data/bambu-pla-colors.json): 183 veröffentlichte PLA-Farbeinträge, mit deutschen/englischen Namen, Produktcodes und allen Farbfeldern.
- [PLA-Farben als CSV](../../data/bambu-pla-colors.csv): dieselben Einträge für Tabellen und weitere Verarbeitung.
- [Unveränderte Original-Farbtabelle](upstream/filaments_color_codes.json): alle 342 Material-/Farbeinträge des Originaldatensatzes, auch Nicht-PLA.
- [Offizielle Mischrezepte](upstream/standard_color_recipes.json): 806 Einträge mit Komponenten, Prozentanteilen, Ergebnis-RGB und Lab.
- [Technische Mischparameter](../../data/bambu-color-mixing-parameters.json): Konfigurationsschlüssel und versionsbezogene Druckhinweise.
- [Original-Prognosemodell](upstream/FilamentMixerModel.hpp): Koeffizienten und C++-Implementierung für angenäherte Farbmischung; noch nicht in unsere Vorschau integriert.
- [Herkunft und feste Quelllinks](provenance.json).

## Umfang der PLA-Liste

| Familie | Einträge |
|---|---:|
| PLA Basic | 38 |
| PLA Matte | 25 |
| PLA Silk | 18 |
| PLA Silk+ | 13 |
| PLA Marble | 2 |
| PLA Sparkle | 6 |
| PLA Tough | 10 |
| PLA Aero | 3 |
| PLA Glow | 5 |
| PLA Dynamic | 1 |
| PLA Galaxy | 4 |
| PLA Wood | 6 |
| PLA-CF | 7 |
| PLA Lite | 13 |
| PLA Translucent | 10 |
| PLA Tough+ | 7 |
| PLA Metal | 5 |
| PLA Pure | 5 |
| PLA Palette | 5 |

Die Liste beschreibt veröffentlichte Einträge in Bambu Studio, **keine Aussage über aktuelle Lieferbarkeit oder Vollständigkeit jedes regionalen Shops**. Mehrfarbige Filamente behalten mehrere Swatches. Das Original verwendet #RRGGBBAA; zusätzlich stellen wir #RRGGBB bereit. RGB-/HEX-Werte sind Bildschirmfarben, keine spektralen Messkurven und keine garantierte Druckfarbe. Metall-, Glanz-, Transparenz- und Beleuchtungseffekte lassen sich so nicht vollständig abbilden.

## Mischdaten: Messung und Näherung getrennt

| Material / Modus | Gemessen | Interpoliert |
|---|---:|---:|
| PLA Basic / CMYW | 239 | 19 |
| PLA Basic / RYBW | 247 | 11 |
| PLA Palette / CMYKW | 290 | 0 |
| Gesamt | 776 | 30 |

CMYW bezeichnet Cyan/Magenta/Yellow/White, RYBW Red/Yellow/Blue/White, CMYKW ergänzt Black (Key). Ein Rezept nutzt nur die in `components` aufgeführten Komponenten; ein Fünffarbsatz bedeutet nicht, dass jedes Rezept fünf Filamente gleichzeitig mischt.

Beispiel aus dem Original: **PLA Basic Cyan 20 % + Magenta 80 % → #965E9E**, Lab [48.14, 33.87, -25.42], `source: measured`. Gemessen und interpoliert dürfen bei einer künftigen Farbsuche nicht gleichwertig als Messwerte ausgegeben werden. Alle 806 Rezepte haben zueinander passende Komponenten-/Anteilslisten und eine Anteilssumme von 100 %.

Das zusätzlich gespeicherte FilamentMixer-Modell approximiert Pigmentmischung mit einer Regression vierten Grades. Die upstream genannte mittlere Delta-E-Abweichung von etwa 2,07 betrifft diese Modellapproximation; sie ist **keine zugesicherte Abweichung eines realen Ausdrucks**. Der Dateikommentar der Rezepte erwähnt auch `source=filament_mixer`; im hier gespeicherten Datensatz sind jedoch nur `measured` und `interpolated` vorhanden.

## Technische Parameter

Bambu Studios Color Mixing erzeugt eine optische Mischung durch verschiedenfarbige Druckschichten bzw. Unterschichten. Es ist keine Regelung einer kontinuierlich im Hotend vermischten Schmelze.

| Schlüssel | Bedeutung |
|---|---|
| `filament_is_mixed` | Kennzeichnet virtuelle Mischfilamente. |
| `filament_mixed_components` | Kommagetrennte 1-basierte Filamentindizes, z. B. 1,3. |
| `filament_mixed_sublayer_ratios` | Kommagetrennte normierte Anteile, z. B. 0.7,0.3. Rezeptdatei dagegen in Prozent; nicht unverändert übernehmen. |
| `enable_mixed_color_sublayer` | Globale Standardvorgabe false; H3C-Profile können true setzen. Teilt gemischte Schichten in Unterschichten. |
| `filament_mixed_gradient` | Z-abhängigen Verlauf aktivieren. |
| `filament_mixed_gradient_range` | Start-/Endanteil der ersten Komponente, z. B. 0.10,0.90. |
| `filament_mixed_gradient_curve` | Kurvenpunkte mit | getrennt; x,y oder x,y,m_in,m_out. Leere Tangente oder nan verwendet PCHIP. |
| `filament_mixed_gradient_per_part` | Verlauf je Bauteil statt gemeinsamer Z-Spanne. |

Wichtig: Rezeptanteile sind Prozentzahlen (20,80); die Slicer-Konfiguration verwendet normierte Anteile als String (`0.2,0.8`). Filamentindizes in `filament_mixed_components` sind **1-basiert**. Die Grenzen 0,1–0,9 im Quellcode gelten für **Verlaufsanker**, nicht pauschal für alle manuellen Mischrezepte.

Der globale Quellcode-Default für `enable_mixed_color_sublayer` ist false. Laut [offizieller Version 2.9.1](https://github.com/bambulab/BambuStudio/releases/tag/v02.09.01.70) ist die Funktion in **H3C-Profilen mit 0,4-mm-Düse bei 0,20 und 0,24 mm Schichthöhe standardmäßig aktiv**. Dort werden PLA Palette und „Use Official Color Mixing Kit“ unterstützt. Das ist vom globalen Default und von älteren Empfehlungen zu unterscheiden.

Die [Einführungsnotizen zu Version 2.5.3](https://github.com/bambulab/BambuStudio/releases/tag/v02.05.03.62) nennen zwei oder drei Filamente desselben Materialtyps, eine Empfehlung für nahezu senkrechte Wände sowie bei 0,4-mm-Düse 0,12 mm Basisschichthöhe und 0,20 mm Mischschichthöhe. Diese älteren Werte sind keine allgemeingültige Vorgabe für aktuelle H3C-Profile. Kleine Unterschichten und extreme Mischverhältnisse müssen zur Düse und zum verwendeten Profil passen.

## Bedeutung für unseren Tentakelring

Die Messrezepte sind eine bessere Grundlage als ein einfacher RGB-Mittelwert. Die vielen Rundungen, Schrägen und kleinen Saugnäpfe des Rings entsprechen aber nicht durchgehend den nahezu senkrechten Flächen der ursprünglichen Anwendungsempfehlung. Eine Vorschau kann daher nur angenähert sein. Ein kleiner Farbprobedruck und die Kontrolle des Slicer-Schichtaufbaus bleiben nötig. Transmissionsdistanz-/Spektralparameter wurden in den ausgewerteten Quellen **nicht als vollständiger Datensatz für sämtliche PLA-Farben** gefunden; fehlende Werte werden nicht erfunden.

## Herkunft und Lizenz

Die Original-JSON-Dateien werden unverändert als Quellen archiviert; die kompakte PLA-Liste ist daraus abgeleitet. BambuStudio wird unter GNU AGPL v3 veröffentlicht; eine Kopie liegt unter [LICENSE-AGPL-3.0.txt](upstream/LICENSE-AGPL-3.0.txt). Die Originaldatei FilamentMixerModel.hpp enthält ausdrücklich eine eigene MIT-Lizenz einschließlich Copyright Justin Hayes (2026), die unverändert erhalten bleibt. Die Ablage übernimmt diese Herkunftshinweise und behauptet keine eigene exklusive Lizenz an den Fremddaten.

Die offizielle Wiki-URL ist in provenance.json hinterlegt. Ihr automatischer Abruf scheiterte mit 402; die dokumentierten Parameter wurden deshalb anhand offizieller Release Notes und des festgehaltenen Quellcodes geprüft.
