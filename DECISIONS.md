# Entscheidungen und Pflege

## Festgehalten

- D-001: Mathematik in azmath, physikalische Formulierung und Simulation in
  azphysics; gemeinsame Zukunftsplanung in aztodo. Abhängigkeit nur von Physik
  zu Mathematik, nicht umgekehrt.
- D-002: Backend-Prinzip, `no_std` und optionales `alloc` aus azmath übernehmen.
  Physik-Backends werden seit AZP-002 mit der tatsächlichen azmath-Abhängigkeit
  weitergeleitet und geprüft; ein leeres Feature darf keine geprüfte numerische
  Backend-Unterstützung vortäuschen.
- D-003: Während 0.0.1 dürfen begründete Breaking Changes vorgenommen werden.
  Sie werden im jeweiligen Changelog und in der zentralen Planung beschrieben.
- D-004: Lokale ROADMAP-Dateien sind kurze Wegweiser. Zukünftige Prioritäten
  werden hier gepflegt; Implementierungsdetails und Qualitätsberichte bleiben
  beim Code. Die alte Liste wird als Ideenspeicher archiviert.
- D-005: Physik beginnt nach gezielter Basisfreigabe und Freigabe der benötigten
  mathematischen Verfahren. Der Begriff „azmath passt“ wird damit an konkrete
  Tests und Gültigkeitsbereiche gebunden, nicht an eine unendliche Featureliste.
- D-006: Solange azmath nicht veröffentlicht ist, bindet azphysics es als
  Git-Abhängigkeit mit festem `rev` ein (kein Branch-Name), damit Physik-Ergebnisse
  reproduzierbar bleiben. Ein Update des `rev` ist eine bewusste Änderung mit
  Verweis auf den geprüften azmath-Commit in STATUS.md. Nach der ersten
  separat beauftragten Veröffentlichung (AZM-011) wird auf eine Versionsabhängigkeit umgestellt.
- D-007: Jede azphysics-CI führt `cargo test` aus, sobald es Code gibt;
  Modelle werden mit analytischen Referenzen getestet (siehe AZP-020).
- D-008: Der Standardbranch heißt in allen drei Repositories `master`. Es wird ausschließlich
  direkt auf `master` gearbeitet, auch bei Code. Es gibt keine Branches und keine Pull Requests,
  ohne Ausnahme (die frühere Ausnahme „sinnvollerer Branch“ ist widerrufen). In KI-Arbeitssitzungen werden
  keine Tests und keine Prüfwerkzeuge ausgeführt (der Projektinhaber testet; die CI prüft nach
  dem Push), siehe [Git-Arbeitsweise](docs/GIT-ARBEITSWEISE.md). Alle drei Repositories
  sind öffentlich.
- D-009: Erste Mechanikmodelle verwenden dokumentierte SI-Einheiten und
  kartesische Arrays. State hält Position/Geschwindigkeit; Impuls wird als m*v
  berechnet. Zunächst keine Einheitenbibliothek. Negative endliche Schritte
  sind für Rückwärtsintegration erlaubt; null, nichtendliche oder nicht
  darstellbare Schritte werden abgewiesen. Energieaussagen setzen geeignete
  konservative Modelle und stabile Schrittweiten voraus.

- D-010: Neue Arbeitspakete verwenden [gemeinsame Abnahmeregeln](docs/ABNAHME.md)
  und [konkrete Spezifikationen](docs/ARBEITSPAKETE.md). Entscheidungspakete
  schließen mit einer dokumentierten Wahl ab, Implementierungspakete erst mit
  Code und Prüfnachweis. Neue Kriterien gelten nicht rückwirkend als historisch erfüllt.
- D-011: Planung trennt Spatial Hash (AZP-030) und BVH (AZP-031),
  XPBD-Distanzbedingungen (AZP-040) und Kontakte (AZP-041) sowie
  P1-FEM-Ränder (AZM-041) und höhere Ordnung (AZM-043). Eine Teilimplementierung
  darf damit nicht als Abschluss des früheren Sammelumfangs erscheinen.

- D-012 (GEO-001): Dynamische Partikelindizes mit stabilen IDs, Positionskopien
  und Update-Vertrag gehören in `azphysics::spatial` (no_std, optional alloc).
  Mathematische Vektoren/Rotationen/Transformationen bleiben in azmath;
  allgemeine AABB-Primitiven gehören bei Bedarf ebenfalls dorthin. Der
  simulationsbezogene BVH-Index bleibt ein eigenes azphysics-Paket AZP-031.
  Zunächst Radius-/Paarkandidaten in 2D/3D, deterministische IDs und konservative
  Übermengen; exakte Kontaktprüfung ist AZP-041. Der erste Index besitzt Daten
  und verwendet alloc; eine no-alloc-Variante ist nicht zugesagt. Die Alternativen
  und API-Skizzen stehen in [azphysics/docs/SPATIAL.md](https://github.com/Andie2302/azphysics/blob/master/docs/SPATIAL.md).

## Noch offen

- AZP-022: Für die ersten Modelle ist per D-009 entschieden (SI-Konvention,
  kartesische Arrays, keine Einheitenbibliothek). Offen bleibt, ob später eigene
  Einheitentypen oder eine externe Bibliothek folgen; Zustandslayout und
  SIMD-/Parallelisierungsbedarf erst bei konkretem Bedarf.
- AZP-060: Bestehendes IAPWS als separates Referenz-/Anwendungscrate erhalten
  oder künftig als optionales Materialmodul anbinden.
- AZM-033: Konkretes Rosenbrock-Paar anhand zitierter Koeffizienten und
  Referenzfälle auswählen; noch kein bestimmtes RODAS-Verfahren festgelegt.
- QUAL-004: Benchmark-Verfahren, Hardware und Regressionsgrenzen aus einer Baseline ableiten.
- AZP-070: Konkrete spätere SPH-/Strömungs-/Thermodynamikpakete auswählen.
- AZM-011: Erste Veröffentlichung, SemVer-Strategie und Prüfmatrix für eine
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
