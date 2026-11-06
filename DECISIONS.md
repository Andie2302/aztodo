# Entscheidungen und Pflege

## Festgehalten

- D-001: Mathematik in azmath, physikalische Formulierung und Simulation in
  azphysics; gemeinsame Zukunftsplanung in aztodo. Abhängigkeit nur von Physik
  zu Mathematik, nicht umgekehrt.
- D-002: Backend-Prinzip, `no_std` und optionales `alloc` aus azmath übernehmen.
  Physik-Backends werden mit der tatsächlichen azmath-Abhängigkeit eingeführt;
  ein leeres Feature darf keine geprüfte numerische Backend-Unterstützung vortäuschen.
- D-003: Während 0.0.1 dürfen begründete Breaking Changes vorgenommen werden.
  Sie werden im jeweiligen Changelog und in der zentralen Planung beschrieben.
- D-004: Lokale ROADMAP-Dateien sind kurze Wegweiser. Zukünftige Prioritäten
  werden hier gepflegt; Implementierungsdetails und Qualitätsberichte bleiben
  beim Code. Die alte Liste wird als Ideenspeicher archiviert.
- D-005: Physik beginnt nach gezielter Basisfreigabe und Freigabe der benötigten
  mathematischen Verfahren. Der Begriff „azmath passt“ wird damit an konkrete
  Tests und Gültigkeitsbereiche gebunden, nicht an eine unendliche Featureliste.

## Noch offen

- GEO-001: Paketgrenze für BVH/Spatial Hash und allgemeine räumliche Suche.
- AZP-010: Einheiten als dokumentierte Konvention, eigene Typen oder externe
  Bibliothek; Zustandslayout und SIMD-/Parallelisierungsbedarf erst danach.
- AZP-060: Bestehendes IAPWS als separates Referenz-/Anwendungscrate erhalten
  oder künftig als optionales Materialmodul anbinden.
- AZM-010: Erste Veröffentlichung, SemVer-Strategie und Prüfmatrix für eine
  freigegebene Version. Kein automatisches Veröffentlichen auf crates.io.

## So bleibt der Plan übersichtlich

1. Vor einem Arbeitspaket Repository-Stand und passende ID prüfen.
2. Änderungen mit Referenzen/Invarianten und Fehlerfällen absichern.
3. Im Code-Repository Changelog und Qualitätsbericht pflegen.
4. Hier Status und Nachweis ergänzen und abhängige Aufgaben neu bewerten.
5. Neue Wünsche zuerst zuordnen, dann priorisieren. Unbekannte Architekturfragen
   als Entscheidung festhalten, statt einen fiktiven Fertigstellungsstatus zu setzen.

Zeitaufwand und Termine werden erst für konkret eingegrenzte Arbeitspakete
vereinbart. Benchmarks, Fuzzingdauer und Testzahlen sind Nachweise eines
bestimmten Stands, keine allgemeinen Korrektheitsgarantien.
