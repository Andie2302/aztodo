# Präzisierte offene Arbeitspakete

Stand: 2026-11-07. Status, Priorität und Repository stehen im
[Backlog](../BACKLOG.md). Für jedes Paket gelten außerdem die
[gemeinsamen Abnahmeregeln](ABNAHME.md). Die folgenden Kriterien sind geplante
Anforderungen und keine Behauptung bereits bestandener Tests.

<a id="qual-001"></a>

## QUAL-001 — Präzisions- und Backend-Abdeckung

Umfang: bestehende Tests inventarisieren und gezielt Lücken schließen.
Arnoldi-Invarianten, Vorkonditionierertests und micromath-Smoke-Tests sind bereits
vorhanden; alte „ungetestet“-Hinweise gelten nicht als aktuelle Bestandsaufnahme.

- Eine Matrix ordnet Löserfamilien/Robustheitsfälle f32, f64, std, libm und
  micromath zu, jeweils mit Testpfad oder begründeter Lücke. Priorität:
  Krylov, ILU(0)/IC(0)/SSOR, Newton und ODE.
- Für jeden Vorkonditionierer mindestens ein deterministisches geeignetes
  System mit ursprünglichem Residuum und Vergleich ohne Vorkonditionierung.
  Einen Iterationsgewinn nur für diesen Referenzfall verlangen; keine allgemeine
  Beschleunigung behaupten. IC/PCG nur mit passenden SPD-Voraussetzungen;
  ILU mit GMRES/BiCGSTAB. Unzulässige Parameter, fehlende Pivots und nicht
  unterstützte Matrixklassen gemäß dokumentiertem Vertrag abweisen.
- Zusätzliche micromath-Kernfälle umfassen Krylov und ODE mit festem
  Iterations-/Schrittlimit, endlichem Ergebnis oder dokumentiertem Fehler.
  Für bekannte analytische Fälle eigene begründete Fehlerbudgets setzen;
  alle Matrixeinträge durch CI-Links belegen.

<a id="qual-002"></a>

## QUAL-002 — Property-Tests und Fuzzing erweitern

Vorhanden sind Targets für FFT, Sparse, special und mehrere QP-Varianten sowie
erzeugte Invariantentests. Der einstündige QP-Lauf ist ein historischer Nachweis.

- Fehlende ODE-Abdeckung ergänzen: beschränkte Dimensionen/Schrittzahlen,
  Vorwärts-/Rückwärtslauf, ungültige Daten und vertraglich definierter Zustand
  nach Fehlern. Referenzen auf analytisch lösbare Systeme beschränken.
- Bestehende Targets prüfen/ergänzen: FFT-Roundtrip auch bei Primlängen/Bluestein,
  Sparse-Musterwiederverwendung/Umordnung, special-Identitäten im gültigen Bereich.
  NaN/Inf, leere und 1×1-Fälle getrennt als gültig oder erwarteter Fehler behandeln.
- Generatoren, Größenlimits und Seeds dokumentieren. Neue Targets mindestens
  als deterministische Harness-Fälle in CI ausführen; libFuzzer-Läufe mit
  festem Zeitbudget separat nachweisen. Jeden Fund minimieren und als dauerhaften
  Regressionstest sichern. Ein Lauf ohne Fund ist keine Vollständigkeitsgarantie.

<a id="qual-003"></a>

## QUAL-003 — Coverage und Fehlerpfade

- Bestehendes llvm-cov-Artefakt an einem aktuellen Commit auswerten; je priorisiertem
  Modul ungetestete relevante Pfade mit Quelle/Zeile und Testidee auflisten.
  Zuerst ungültige Eingaben, Breakdown, Newton-Stillstand und Buffergrenzen.
- Für ausgewählte Lücken gezielte Tests ergänzen und Vorher/Nachher bei gleicher
  Toolchain/Feature-Konfiguration dokumentieren. Vorhandene Stable-Messung als
  Zeilen-/Regionenabdeckung ausweisen; keine Branch-Abdeckung behaupten.
- Erst nach stabiler Baseline Schwellenwerte pro Bereich entscheiden, generierten
  oder unerreichbaren Code begründet ausnehmen. Ein Miri-Pilot kann ergänzen,
  benötigt aber eigenen Umfang/Toolchain/Laufzeit; er ersetzt keine Numeriktests
  und wird nicht wegen fehlendem `unsafe` als kostenlos angenommen.

<a id="qual-004"></a>

## QUAL-004 — Reproduzierbare Performance-Baseline

- Vorhandene Sparse-Refaktor-/QP-Benchmarks inventarisieren und ein automatisierbares
  Messverfahren festlegen (criterion oder begründete Alternative). Aufwärmen,
  Stichproben, Release-Profil, Hardware, Toolchain und Problemgrößen festhalten.
- Aufbau/Analyse, Refaktorisierung und Solve getrennt messen; RHS-/Jacobi-Aufrufe,
  Iterationen und Speicherbedarf ergänzen. Jede Messung mit Residuum/Referenz
  gegen eine schnellere, aber falsche Lösung absichern.
- Auf festgelegter Hardware wiederholte Baseline samt Streuung speichern. Erst
  daraus Regressionsgrenze und Runner wählen. Allgemeine GitHub-Runner bekommen
  zunächst Berichte, keine willkürliche harte Prozentgrenze. GL4/Verlet bleibt
  ein Genauigkeitsvergleich, bis ein solcher Zeitbenchmark vorliegt.

<a id="azm-032"></a>

## AZM-032 — Eingebetteter Radau-Fehlerschätzer

- Für das vorhandene dreistufige Radau IIA ein veröffentlichtes, nachvollziehbares
  Schätzerverfahren mit Koeffizienten und Ordnung belegen. Eine weitere
  Schrittverdopplung erfüllt dieses Paket nicht.
- Geschätzten und tatsächlichen lokalen Fehler auf glatten und steifen
  Referenzen über mindestens drei Schrittweiten vergleichen; Controller mit
  Annahme, Ablehnung, Mindestschritt und Maximalzahl prüfen, auch rückwärts.
- Gegen die bisherige Schrittverdopplung bei gleicher erzielter Genauigkeit
  Fehler, RHS-/Jacobi-Aufrufe und Newton-Arbeit ausweisen; kein Gewinn erzwingen,
  wenn er nicht messbar ist. Neue Methode/Option erst danach dokumentiert freigeben.

<a id="azm-033"></a>

## AZM-033 — Rosenbrock-Verfahren mit eingebettetem Schätzer

- Konkretes Rosenbrock-Paar mit publizierten Koeffizienten, Haupt-/Schätzerordnung
  und Stabilitätseigenschaften auswählen. Die Auswahl ist vor Implementierung
  im Bericht zu begründen; RODAS oder eine andere Familie ist noch nicht festgelegt.
- Nichtautonome Terme, Jacobi-Matrix und eventuelle zeitliche Ableitung gemäß
  dem gewählten Verfahren behandeln. Lineare steife Systeme, nichtlineare
  Referenz und nichtautonomer Fall prüfen; adaptive Annahme-/Ablehnung wie AZM-032.
- Gegen ROS2-Schrittverdopplung und vorhandenes SEULEX bei vergleichbarer
  Genauigkeit messen. SEULEX nicht als bereits vorhandenes Rosenbrock-Paar ausgeben.

<a id="geo-001"></a>

## GEO-001 — Zuständigkeit räumlicher Suche entscheiden

- Vorhandene Geometrie-Primitiven in azmath inventarisieren; konkrete Abfragen
  für Partikelkontakte und Nachbarschaften definieren: Punkt/Radius/AABB,
  2D/3D, statische/dynamische Daten, stabile IDs und deterministische Reihenfolge.
- Allgemeine Geometrie in azmath versus simulationsbezogener Index in azphysics
  anhand zweier kleiner API-Skizzen vergleichen. Besitz-/Update-Modell, alloc-
  Bedarf und Fehlerverhalten festlegen. Abhängigkeit bleibt azphysics → azmath.
- Ergebnis als Entscheidung mit zugeordneten Modulen, Grenzen und Reihenfolge
  von AZP-030/AZP-031 festhalten. Kein viertes Repository voraussetzen und keine
  Implementierung oder Leistungsfreigabe durch diese Entscheidung behaupten.

<a id="azp-021"></a>

## AZP-021 — Kombinierbare Kräfte, Federn und Dämpfung

- Kraft-/Beschleunigungs-Schnittstelle und Summation mit Masse, SI-Einheiten und
  validierten Parametern dokumentieren. Federkräfte ohne Dämpfung und lineare
  viskose Dämpfung getrennt formulieren; negative Dämpfung nicht still zulassen.
- Ungedämpfte, unter-, kritisch und überdämpfte Referenzfälle gegen analytische
  Lösungen prüfen; bei Dämpfung Arbeit/Energiebilanz und monotone Gesamtenergie
  im nachgewiesenen Bereich statt Energieerhaltung verlangen.
- Geschwindigkeitsabhängige Dämpfung mit geeignetem allgemeinem ODE-Löser
  integrieren oder eine mathematisch begründete eigene Aufspaltung dokumentieren.
  Sie darf nicht als ortsabhängige Verlet-Beschleunigung eingeschleust werden.

<a id="azp-022"></a>

## AZP-022 — Einheiten, Zustandslayout und Ausführung entscheiden

- SI-Konventionen bleiben für bestehende Modelle gültig. Typisierte Einheiten
  versus dokumentierte Skalare anhand derselben kleinen Kraft-/Integrations-API
  vergleichen; no_std, f32/f64, Abhängigkeiten und Bedienbarkeit bewerten.
- Für viele Partikel AoS/SoA und Datenbesitz anhand eines repräsentativen Ablaufs
  skizzieren; SIMD/Parallelisierung nur bei gemessenem Bedarf priorisieren.
- Entscheidung und bewusst zurückgestellte Punkte dokumentieren. Dieses Paket
  ist kein notwendiger Blocker für den kleinen XPBD-Distanzprototyp.

<a id="azp-030"></a>

## AZP-030 — Spatial Hash

- Nach GEO-001 zunächst einen klar begrenzten Partikel-/Radiusindex in 2D/3D
  bauen. Zellgröße > 0, negative Koordinaten, Zellgrenzen, Integer-Überlauf,
  doppelte IDs und nichtendliche Daten mit definiertem Verhalten behandeln.
- Kandidatenmengen gegen Brute-Force auf festen und erzeugten Szenen prüfen:
  keine fehlenden echten Nachbarn; zulässige Übermengen explizit machen.
  Leere, einzelne, identische und dicht geclusterte Punkte einschließen.
- Bewegung/Neuaufbau/Entfernung gemäß API testen; keine veralteten Kandidaten,
  eindeutige Paare und deterministische Ausgabe. Speichermodell/alloc-Grenze
  sowie Aufwand für mindestens drei Szenengrößen dokumentieren.

<a id="azp-031"></a>

## AZP-031 — BVH

- Nach GEO-001 Objekttyp, AABB-Konventionen und Build-/Refit-Vertrag festlegen.
  Zunächst AABB-Abfragen; Ray-/Mesh-Abfragen sind keine stillen Zusatzanforderungen.
- Gegen Brute-Force auf statischen und bewegten 2D/3D-Szenen vergleichen,
  einschließlich leerer/degenerierter Boxen, Grenzberührung und ungültiger Grenzen.
- Refit versus Neubau prüfen; keine verlorenen Treffer, stabile IDs und
  deterministische Ausgabe. Speicher und Aufbau-/Abfragekosten messen;
  BVH ist keine Voraussetzung für Spatial Hash oder erste Distanzbedingungen.

<a id="azp-040"></a>

## AZP-040 — XPBD-Distanzbedingungen

- Zunächst Teilchen mit inverser Masse und paarweise Distanzbedingungen,
  Compliance >= 0 und `alpha_tilde = compliance / dt²`. Fixierte Teilchen
  haben inverse Masse null. Lambda-Lebensdauer pro Zeitschritt und Warmstart
  ausdrücklich definieren; Vorzeichen und Einheiten dokumentieren.
- Zwei-Teilchen-Referenz, starre/nachgiebige Grenze, ungleiche Massen,
  zwei fixierte Teilchen und koinzidente Punkte ohne Division durch null prüfen.
  Zeitschritt und Iterationszahl getrennt variieren; verbleibende Abhängigkeit messen.
- Kleine Kette mit Schwerkraft und fixiertem Ende testen. Endliche Zustände,
  begrenzte Constraint-Residuen und Reproduzierbarkeit nachweisen. Kontakte und
  räumliche Suche sind für dieses Basispaket keine Voraussetzung; siehe AZP-041.

<a id="azp-041"></a>

## AZP-041 — XPBD-Kontakte

- Baut auf AZP-040 und AZP-030 oder AZP-031 auf. Zuerst reibungsfreie
  nichtadhäsive Teilchen-/Primitive-Kontakte mit einseitiger Normalbedingung;
  Reibung, Restitution und kontinuierliche Kollisionserkennung bleiben explizit offen.
- Kandidaten gegen Brute-Force prüfen; Normalen, erlaubte Penetration und
  Nichtanziehung bei getrennten Körpern testen. Statische Wand, zwei Teilchen,
  Ruhekontakt und symmetrischer Fall mit Impulsbilanz als Referenzen verwenden.
- Verhalten bei großen Zeitschritten/Schnellbewegung dokumentieren; keine
  Tunneling-Garantie ohne CCD. Kontaktpersistenz und Reihenfolge deterministisch prüfen.

<a id="azm-040"></a>

## AZM-040 — PDE-Ränder und Nullräume

- Zunächst skalaren strukturierten 2D-Poisson-Operator mit konstantem Koeffizienten
  für Neumann- und periodische Ränder ergänzen; vorhandenes Dirichlet erhalten.
  Vorzeichen, Gitterkonvention und diskrete Flussbilanz festlegen.
- Bei reinem Neumann/periodischen Rändern `A*1 = 0`, Symmetrie und
  RHS-Kompatibilität nachweisen. Inkompatible RHS abweisen oder ausdrücklich
  projizieren; Nullraumfixierung/Mittelwert null dokumentieren. PCG nur auf
  dem geeigneten Unterraum einsetzen.
- Hergestellte glatte Lösungen auf mindestens drei Gittern mit passender
  Konvergenzordnung, Bilanz und Residuum prüfen; Multigrid-Restriktion/
  Prolongation müssen Konstanten/Nullraum erhalten. Variable Koeffizienten,
  Anisotropie und 3D werden später separat abgegrenzt.

<a id="azm-041"></a>

## AZM-041 — FEM-Neumann-/Robin-Ränder

- Vorhandene P1-Elemente in 1D/2D um Randintegrale für Neumann und Robin
  ergänzen. Normalenrichtung, Robin-Vorzeichen und Mischung mit Dirichlet festlegen.
- Analytische/hergestellte Lösungen, Randflussbilanz und Konvergenz auf drei
  Netzen prüfen; reines Neumann mit Kompatibilität/Nullraum behandeln.
- Unzulässige Randzuordnung, degenerierte Elemente und nichtendliche Daten
  sauber melden. Höhere Ordnung ist das eigene Paket AZM-043; 3D bleibt gesondert.

<a id="azm-043"></a>

## AZM-043 — FEM höherer Ordnung

- Nach AZM-041 zunächst quadratische P2-Elemente in 1D/2D auswählen;
  lokale Knotenordnung, Randfreiheitsgrade und Integrationsordnung dokumentieren.
- Polynome bis zum vorgesehenen Grad exakt reproduzieren; Partition der Eins,
  Ableitungen und Assemblierung prüfen. L2-/H1-Konvergenz auf drei Netzen im
  regulären Referenzfall nachweisen und gegen P1 bei gleicher Problemstellung vergleichen.
- Dirichlet/Neumann/Robin aus AZM-041 übernehmen. Keine pauschale Freigabe
  beliebiger p-Ordnungen oder 3D durch dieses erste P2-Paket.

<a id="azp-051"></a>

## AZP-051 — Physikalische Druckprojektion

- Zunächst inkompressible konstante Dichte auf einem kartesischen 2D-MAC-Gitter.
  Diskrete Divergenz/Gradient und deren Zusammenhang mit dem Poisson-Operator,
  Druckeinheiten und Wand-/periodische Ränder dokumentieren; offene Ränder später abgrenzen.
- Aus AZM-040 Nullraum und RHS-Kompatibilität verwenden, PCG nicht duplizieren.
  Divergenz nach Projektion gegen ein vorab festgelegtes, zum Löserresiduum
  passendes Budget prüfen; Projektion zweimal nahezu idempotent.
- Divergenzfreies Feld, hergestelltes Druckfeld, geschlossene Box und
  inkompatible RHS testen. Wandfluss und Energiebilanz der Projektion prüfen.

<a id="azp-050"></a>

## AZP-050 — PIC/FLIP/APIC

- Zunächst 2D-MAC-Gitter und konstante Dichte aus AZP-051 nutzen. Drei getrennte
  Meilensteine: PIC-Transfers, FLIP-Geschwindigkeitsinkremente, APIC-affine
  Partikelzustände. Gesamtes Paket erst nach allen drei Nachweisen erledigt.
- Partikel→Gitter→Partikel auf konstanten Feldern, APIC zusätzlich auf affinen
  Feldern prüfen; Massen-/Impulsbilanz und geeignete Drehimpulsreferenz,
  leere Zellen, Randpartikel und kleine Massen behandeln. PIC darf dissipieren;
  FLIP/APIC erhalten keine unbegründete Energiegarantie.
- Erst isolierte Transfers, dann Druckprojektion koppeln. Eine geschlossene
  Referenzszene mit Divergenz, Gesamtmasse und Randfluss auswerten. Freie
  Oberflächen, 3D und komplexe Kontaktmodelle brauchen eigene Folgepakete.

<a id="azm-042"></a>

## AZM-042 — Algebraisches Multigrid

- Zunächst reelle skalare SPD-Probleme, z. B. variable Poisson-Koeffizienten;
  Aggregation/Interpolation, Grobgitteroperator und Glätter festlegen.
- Konstanten bzw. bekannten Nahnullraum erhalten. Falls als PCG-Vorkonditionierer
  angeboten, Symmetrie/Positivität des gesamten Zyklus nachweisen; sonst ein
  geeignetes anderes Krylov-Verfahren verwenden.
- Residuen, Iterationen, Aufbauzeit und Speicher gegen vorhandene Alternativen
  auf mindestens drei Größen und zwei Koeffizientenklassen ausweisen. Kein
  universelles lineares Skalierungsversprechen aus einem einzelnen Benchmark.

<a id="azm-050"></a>

## AZM-050 — Schwierige nichtlineare Optimierung

- SQP-Wiederherstellung für unzulässige Startpunkte zuerst; Machbarkeit,
  Zielabstieg und maximale Wiederherstellungsarbeit mit Diagnosen dokumentieren.
- Ein schwieriges zulässiges Problem, ein unzulässiger Start und ein tatsächlich
  unlösbares Problem mit KKT-/Zulässigkeitsresiduen gegen unabhängige Referenzen
  prüfen; Stillstand darf keinen Erfolg melden.
- Danach Maratos-Effekt an einem belegten Referenzfall reproduzieren und Bedarf
  einer Korrektur entscheiden. Umsetzung oder begründete Zurückstellung nennen;
  Wiederherstellung und Second-Order-Korrektur sind unterschiedliche Mechanismen.

<a id="azm-060"></a>

## AZM-060 — Reelle Schur- und Eigenvektoren

- Reelle Schur-Form mit 1×1-/2×2-Blöcken: `A*Q ≈ Q*T`, `QᵀQ ≈ I` und
  Rekonstruktion relativ zur Matrixnorm. Skalierung und Konvergenzfehler prüfen.
- Eigenvektoren aus reellen/komplex-konjugierten Blöcken mit `A*v ≈ lambda*v`
  prüfen, Normalisierung/Phasenfreiheit dokumentieren. Bei defekten Matrizen
  keine vollständige Eigenbasis versprechen; Schur-Vektoren bleiben getrennt.
- Wiederholte/nahe Eigenwerte und nichtnormale Systeme einschließen;
  Arnoldi-Ritz-Paare durch ursprüngliche Residuen prüfen. Bestehende komplexe
  Scalar-Verfahren nicht fälschlich als fehlend markieren.

<a id="azm-070"></a>

## AZM-070 — Weitere komplexe Sparse-Verfahren

- Bestand (ScalarCsr, komplexe Sparse-LU/GMRES) inventarisieren; erste konkrete
  Erweiterung als hermitesches CG mit dokumentiertem SPD-Vertrag abgrenzen.
- Konjugiertes Skalarprodukt, Hermitezität und Residuen für Complex<f32/f64>
  gegen dichte Referenz prüfen; ungeeignete Matrixklassen und Breakdown behandeln.
- Komplexe Vorkonditionierer erst mit eigenem Vertrag/Testbereich ergänzen;
  keine vollständig generische Sparse-Unterstützung durch einen Löser behaupten.

<a id="azm-080"></a>

## AZM-080 — Breitere Genauigkeitsbereiche

- Bestehende special-/Verteilungsreferenzen beibehalten; zunächst große
  Formparameter, extreme Tails, Nullstellennähe und Subnormale priorisieren.
  Raster und ausgeschlossene Bereiche vorab benennen.
- Referenzen mit höherer Präzision unabhängig auf den Zieltyp runden und
  Stabilität bei verdoppelter Referenzpräzision verlangen. ULP-/absolute/
  relative Fehler mit f32/f64 und std/libm je Bereich ausweisen.
- Monotonie, Komplementarität und CDF/Quantil-/SF/ISF-Roundtrips prüfen;
  unerreichbare Genauigkeit oder Nichtkonvergenz ausdrücklich melden.

<a id="azm-021"></a>
<a id="azm-011"></a>
<a id="azp-060"></a>
<a id="azp-070"></a>

## AZM-021, AZM-011, AZP-060 und AZP-070 — Entscheidung und spätere Aufteilung

- **AZM-021:** Chandrupatla gegen vorhandenes Brent auf glatten, flachen und
  schlecht skalierten geklammerten Nullstellen vergleichen. Funktionsaufrufe,
  Residuum und Fehlermeldungen messen; nur bei begründetem Nutzen aufnehmen.
- **AZM-011:** separat beauftragte Veröffentlichung. Version/Tag, Lizenzen,
  Paketinhalt, MSRV/Feature-Matrix, Changelog und Dokumentation prüfen; Dry-Run
  und entpackte Tests belegen. Erst echte Veröffentlichung samt Link schließt ab.
- **AZP-060:** vorhandenes IAPWS-Testcrate inventarisieren, Lizenz/Abhängigkeiten,
  Einheiten, Zustandsbereiche und Referenzen erfassen. Separates Referenzcrate
  versus optionales Materialmodul entscheiden; Entscheidung schließt noch
  keine künftige Materialimplementierung ab. Dafür anschließend neue IDs anlegen.
- **AZP-070:** Ideensammlung für SPH/weitere Strömung/Thermodynamik. Erst Modell,
  Gleichungen, Randbedingungen, Gültigkeit, Voraussetzungen und Referenzproblem
  auswählen und in neue konkrete Implementierungs-IDs aufteilen. Der Sammelpunkt
  wird nicht durch ein einzelnes Verfahren als vollständig erledigt markiert.
