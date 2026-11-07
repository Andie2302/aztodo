# Aufgaben und Abnahmekriterien

Status: **Erledigt** = implementiert und mit verlinkten Nachweisen geprüft;
**Offen** = noch umzusetzen; **Entscheidung** = vor Umsetzung fachlich klären.
**In Arbeit** und **Blockiert** werden mit PR bzw. fehlender Voraussetzung belegt.

Stand: 2026-11-07. Für neue Pakete gelten die [gemeinsamen Abnahmeregeln](docs/ABNAHME.md)
und die [detaillierten Spezifikationen](docs/ARBEITSPAKETE.md). Die Tabellen sind
der Index; maßgeblich sind zusätzlich Umfang, Grenzen und Kriterien im Detaildokument.
„Vorhanden“ in STATUS.md bedeutet keine pauschale Freigabe für jede Eingabe.

## Kurzfristig

| ID | Repo | Status | Voraussetzungen | Abnahme |
|---|---|---|---|---|
| AZM-001 | azmath | Erledigt | Bestehende special-/dist-Module | Direkte SF/ISF, hochpräzise f32/f64-Referenzen, Randfälle und kleine p-Werte in Hypothesentests; siehe Qualitätsbericht |
| PLAN-001 | aztodo | Erledigt | Repositories angelegt | Zentrale Horizonte, feste IDs, Zuständigkeiten, Abhängigkeiten, Entscheidungsregeln und archivierte alte Roadmap |
| PLAN-002 | aztodo | Erledigt | PLAN-001, aktueller Code-/CI-Abgleich | Offene Pakete spezifiziert, gemeinsame Abnahmeregeln, fehlende Qualitätsthemen als IDs, Abhängigkeiten und nächste Reihenfolge präzisiert; siehe docs/ABNAHME.md und docs/ARBEITSPAKETE.md |
| AZP-001 | azphysics | Erledigt | PLAN-001 | Kompilierbares Gerüst 0.0.1, no_std/alloc-Build, CI-Konfiguration und Architektur; kein Physikverfahren als implementiert ausgegeben |
| AZP-002 | azphysics | Erledigt | AZP-001, öffentliches azmath | Git-Abhängigkeit mit festem rev und Lockfile, Real-Export, Backend-/alloc-Weiterleitung, zwei Integrationstests (mit alloc drei), Embedded-/MSRV-Builds |
| AZM-010 | azmath | Erledigt | AZM-001, vorhandene Krylov-/Sparse-Absicherung | Backend-/Embedded-Prüfungen und Fehlerpfade dokumentiert; Paket-Metadaten, Changelog, `cargo publish --dry-run` und Tests des entpackten Crates erfolgreich; noch keine Veröffentlichung (AZM-011) |
| [AZM-011](docs/ARBEITSPAKETE.md#azm-011) | azmath | Entscheidung | AZM-010, konkrete Version vereinbart | Veröffentlichung separat beauftragen; Versionsstand/Tag/CI prüfen, anschließend tatsächliche crates.io-Veröffentlichung |
| AZM-020 | azmath | Erledigt | AZM-010 | MINRES: Residuen für symmetrisch-indefinite Systeme, f32/f64, Breakdown/Nullstart/NaN, zulässige Vorkonditionierer, Referenzvergleich; Nachweise im Release-/MINRES-Bericht |
| [AZM-021](docs/ARBEITSPAKETE.md#azm-021) | azmath | Entscheidung | Brent vorhanden | Chandrupatla nur nach begründetem Vergleich; geklammerte Nullstellen, Randpunkte, unzulässige Intervalle und Stagnation testen |

## Laufende Qualität: eigenständig abnehmbare Erweiterungen

Die bestehende CI und vorhandene Tests sind in STATUS.md belegt. Die folgenden
Pakete ergänzen diese Grundlage und sind keine Behauptung, bisher habe es keine Tests gegeben.

| ID | Repo | Status | Voraussetzungen | Abnahme |
|---|---|---|---|---|
| [QUAL-001](docs/ARBEITSPAKETE.md#qual-001) | beide | Offen | Aktueller Testbestand, AZM-010 | Testmatrix pro Löser/Präzision/Backend; gezielte f32-, Vorkonditionierer- und micromath-Lücken schließen, Residuen und begrenzte Iterationsarbeit belegen |
| [QUAL-002](docs/ARBEITSPAKETE.md#qual-002) | beide | Offen | Bestehende Property-/Fuzz-Harnesses | ODE-Fuzz-Abdeckung, bestehende FFT/Sparse/special-Targets prüfen, begrenzte Generatoren, feste Seeds, minimierte Funde als Regressionen und reproduzierbare Laufnachweise |
| [QUAL-003](docs/ARBEITSPAKETE.md#qual-003) | azmath | Offen | Vorhandenes Coverage-Artefakt | Aktuelle Baseline, priorisierte Fehlerpfade und Vorher/Nachher-Bericht; Schwellenwerte erst nach stabiler Messung entscheiden |
| [QUAL-004](docs/ARBEITSPAKETE.md#qual-004) | beide | Offen | Vorhandene Benchmark-Beispiele und analytische Referenzen | Automatisierbare Messung, Korrektheitsprüfung, Hardware/Toolchain/Streuung, getrennte Aufbau-/Solve-Kosten; Regressionsgrenzen aus Baseline ableiten |

## Mittelfristig

| ID | Repo | Status | Voraussetzungen | Abnahme |
|---|---|---|---|---|
| AZM-030 | azmath | Erledigt | AZM-010 | Separable symplektische Verlet-Schritte (`ode::verlet`, `ode::verlet_separable`); Ordnung 2, Zeitumkehr, beschränkter Energiefehler am Oszillator und Pendel, konstante/allokationsfreie Puffer. Nachweis: 16 Tests in `tests/ode_verlet.rs`, PR #5, Commit `b3418ef`, alle 18 CI-Jobs grün |
| AZM-031 | azmath | Erledigt | AZM-010, Newton/lineare Löser vorhanden | Zweistufiges Gauss–Legendre-RK4, analytische/FD-Jacobi-Matrix, allokationsfrei, 25 neue Tests zu Ordnung, Zeitumkehr, Invarianten und Fehlerpfaden; PR #7, Commit `75c5dc04`, 18 CI-Jobs grün. azphysics-Vergleich: PR #5, vier f32/f64-Tests und ausführbares Beispiel; Nachweise in STATUS.md |
| [AZM-032](docs/ARBEITSPAKETE.md#azm-032) | azmath | Offen | Radau IIA, vorhandene adaptive Schrittverdopplung | Publizierter eingebetteter Fehlerschätzer, lokale Fehlerordnung, Annahme/Ablehnung und Vergleich bei gleicher Genauigkeit |
| [AZM-033](docs/ARBEITSPAKETE.md#azm-033) | azmath | Offen | ROS2/SEULEX, Jacobi-/lineare Löser | Belegtes Rosenbrock-Paar auswählen und Schätzer/Stabilität, nichtautonome Terme und adaptive Referenzen nachweisen |
| AZP-010 | azphysics | Erledigt | AZP-001, PLAN-001 | SI-/Einheitenkonventionen, kartesische Position/Geschwindigkeit, geprüfte Parameter/Uhren und PhysicsError; docs/CONVENTIONS.md, PR #4 |
| AZP-020 | azphysics | Erledigt | AZM-010, AZP-010, Integrator AZM-030; azphysics verwendet `75c5dc04` mit geprüften Verlet-/Gauss–Legendre-Verfahren (D-006) | Freier Flug, konstante Beschleunigung und Oszillator; 20 f32/f64-Tests, Energie-/Impulsbilanz, Ordnung 2, Zeitumkehr, Beispiel und alle 14 CI-Jobs grün; PR #4, docs/VALIDATION.md |
| [AZP-021](docs/ARBEITSPAKETE.md#azp-021) | azphysics | Offen | AZP-010, AZP-020; geeignete allgemeine ODE-Löser vorhanden | Kombinierbare Kräfte, Feder und lineare Dämpfung; analytische Dämpfungsregime und Arbeits-/Energiebilanz, kein Missbrauch der Verlet-Schnittstelle |
| [AZP-022](docs/ARBEITSPAKETE.md#azp-022) | azphysics | Entscheidung | AZP-010, Anforderungen aus AZP-021/AZP-040 | Einheitentypen und Partikellayout anhand API-Skizzen bewerten; SI bleibt gültig, kein Blocker für kleinen XPBD-Prototyp |
| [GEO-001](docs/ARBEITSPAKETE.md#geo-001) | beide | Erledigt | AZP-020, vorhandene azmath-Geometrie | D-012: dynamischer Partikelindex in azphysics::spatial, mathematische Primitive in azmath; 2D/3D, Datenbesitz, stabile IDs, Updates und alloc-Vertrag mit API-Alternativen in docs/SPATIAL.md; PR #6 |
| [AZP-030](docs/ARBEITSPAKETE.md#azp-030) | azphysics; gemeinsame Primitive gemäß GEO-001 | Erledigt | [GEO-001](docs/ARBEITSPAKETE.md#geo-001) | Spatial Hash mit alloc: Radius-/Paarkandidaten, stabile IDs, atomare Updates/Neuaufbau, Bereichsgrenzen und Scan-Fallback; 38 std/libm-Tests bzw. 20 micromath-f32-Tests, Brute-Force und drei Szenengrößen; PR #6, CI-Nachweise in STATUS.md |
| [AZP-031](docs/ARBEITSPAKETE.md#azp-031) | azphysics; gemeinsame Primitive gemäß GEO-001 | Offen | [GEO-001](docs/ARBEITSPAKETE.md#geo-001) | BVH für AABB-Abfragen: Build/Refit gegen Brute-Force, degenerierte Boxen, Bewegung, IDs, Speicher und Kosten; unabhängig von AZP-030 |
| [AZM-040](docs/ARBEITSPAKETE.md#azm-040) | azmath | Offen | AZM-010, Sparse/FDM/Multigrid vorhanden | Zunächst strukturierter 2D-Poisson-Operator mit konstantem Koeffizienten, Neumann/periodisch, Nullraum, RHS-Kompatibilität und drei Gitter |
| [AZM-041](docs/ARBEITSPAKETE.md#azm-041) | azmath | Offen | AZM-040, FEM P1 vorhanden | P1-Neumann-/Robin-Ränder in 1D/2D, Randintegrale, Nullraum, drei Netze und Flussbilanz; höhere Ordnung separat AZM-043 |
| [AZP-040](docs/ARBEITSPAKETE.md#azp-040) | azphysics | Offen | AZP-010, AZP-020 | XPBD-Distanzbedingungen: inverse Massen, Compliance/dt², Lambda-Vertrag, fixierte/koinzidente Teilchen, Kette und Schritt-/Iterationsvariation; ohne Kontaktpflicht |
| [AZP-041](docs/ARBEITSPAKETE.md#azp-041) | azphysics | Offen | AZP-040; AZP-030 oder AZP-031 | Reibungsfreie einseitige Kontakte, Kandidaten gegen Brute-Force, Wand-/Ruhekontakt, Nichtanziehung und Impulsbilanz; keine CCD-/Reibungszusage |
| [AZP-051](docs/ARBEITSPAKETE.md#azp-051) | azphysics | Offen | AZM-040, AZP-010, PCG vorhanden | Zunächst 2D-MAC-Projektion bei konstanter Dichte: Divergenz/Idempotenz, Wände/periodische Ränder, Nullraum und Einheiten; offene Ränder später |
| [AZM-050](docs/ARBEITSPAKETE.md#azm-050) | azmath | Offen | QP/NLP, LDLT, Autodiff vorhanden | Wiederherstellungsphase und ggf. Maratos-Korrektur; schwierige NLPs, Zulässigkeit und Optimalitätsresiduen gegen Referenzen |

## Langfristig

| ID | Repo | Status | Voraussetzungen | Abnahme |
|---|---|---|---|---|
| [AZP-050](docs/ARBEITSPAKETE.md#azp-050) | azphysics | Offen | AZP-020, AZP-051, Transfer-/Gitterkonzept | PIC/FLIP/APIC getrennt verifizieren; konstante/affine Felder, Masse/Impuls, Energieverhalten und Randbehandlung; danach Kopplung |
| [AZM-043](docs/ARBEITSPAKETE.md#azm-043) | azmath | Offen | [AZM-041](docs/ARBEITSPAKETE.md#azm-041) | Zunächst P2-FEM in 1D/2D, Polynomreproduktion, L2/H1-Konvergenz und Randfreiheitsgrade; kein beliebiges p oder 3D |
| [AZM-042](docs/ARBEITSPAKETE.md#azm-042) | azmath | Offen | AZM-040, Skalierungsbedarf durch Benchmarks | AMG-Hierarchie, Vorkonditionierer-Eigenschaften, Iterationsgewinn und Speicherbedarf auf mehreren Problemklassen |
| [AZM-060](docs/ARBEITSPAKETE.md#azm-060) | azmath | Offen | Reeller nichtsymmetrischer Eigenlöser vorhanden | Reelle Schur-Vektoren/Eigenvektoren und Arnoldi-Ritz-Paare; Rekonstruktion und Residuen. Vorhandene komplexe Scalar-Verfahren separat berücksichtigen |
| [AZM-070](docs/ARBEITSPAKETE.md#azm-070) | azmath | Offen | Scalar/Complex und komplexe Sparse-LU/GMRES vorhanden | Weitere komplexe Sparse-Verfahren mit passenden hermiteschen Voraussetzungen und f32/f64-Komplex-Referenzen |
| [AZM-080](docs/ARBEITSPAKETE.md#azm-080) | azmath | Offen | AZM-001, vorhandene special-Referenzen | Große Formparameter, breite Raster, Nullstellennähe und Subnormalgrenzen; Genauigkeitsbudgets und Konvergenzgrenzen explizit ausweisen |
| [AZP-060](docs/ARBEITSPAKETE.md#azp-060) | azphysics | Entscheidung | AZP-010, konkrete Materialanforderungen | IAPWS-Testimplementierung inventarisieren; Anbindung oder Übernahme entscheiden, Referenzzustände und Gültigkeitsbereiche erhalten |
| [AZP-070](docs/ARBEITSPAKETE.md#azp-070) | azphysics | Entscheidung | Konkretes Modellziel; AZP-050/AZP-060 nur nach Bedarf | Sammlung SPH/Strömung/Thermodynamik vor Implementierung in eigene IDs mit Gleichungen, Referenzen und Grenzen aufteilen; kein pauschales Gesamtpaket |

## Weitere Ideen bleiben erhalten

Die [archivierte Roadmap](docs/archive/azmath-roadmap-2026-11-06.md) enthält
u. a. Taylor-ODE, allgemeine PDE-Erweiterungen und weitere Algebra-/Optimierungsarbeiten.
Eingebettete Radau-/Rosenbrock-Fehlerschätzer sind jetzt als AZM-032/033 explizit erfasst. Vor Aufnahme in
einen aktiven Horizont bekommt jede Idee hier eine ID, eine Zuständigkeit,
prüfbare Voraussetzungen und eine Abnahme. Ein alter Status wird am Quelltext
und an Tests überprüft; „bewusst ausgelassen“ bedeutet weiterhin prüfbar offen.
