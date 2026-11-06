# Aufgaben und Abnahmekriterien

Status: **Erledigt** = implementiert und mit verlinkten Nachweisen geprüft;
**Offen** = noch umzusetzen; **Entscheidung** = vor Umsetzung fachlich klären.
„Vorhanden“ in STATUS.md bedeutet keine pauschale Freigabe für jede Eingabe.

## Kurzfristig

| ID | Repo | Status | Voraussetzungen | Abnahme |
|---|---|---|---|---|
| AZM-001 | azmath | Erledigt | Bestehende special-/dist-Module | Direkte SF/ISF, hochpräzise f32/f64-Referenzen, Randfälle und kleine p-Werte in Hypothesentests; siehe Qualitätsbericht |
| PLAN-001 | aztodo | Erledigt | Repositories angelegt | Zentrale Horizonte, feste IDs, Zuständigkeiten, Abhängigkeiten, Entscheidungsregeln und archivierte alte Roadmap |
| AZP-001 | azphysics | Erledigt | PLAN-001 | Kompilierbares Gerüst 0.0.1, no_std/alloc-Build, CI-Konfiguration und Architektur; kein Physikverfahren als implementiert ausgegeben |
| AZM-010 | azmath | Offen | AZM-001, vorhandene Krylov-/Sparse-Absicherung | Backend-/Embedded-Prüfungen und Fehlerpfade dokumentiert; Paket-Metadaten, Changelog, `cargo publish --dry-run`; noch keine Veröffentlichung |
| AZM-020 | azmath | Offen | AZM-010 | MINRES: Residuen für symmetrisch-indefinite Systeme, f32/f64, Breakdown/Nullstart/NaN, zulässige Vorkonditionierer, Referenzvergleich |
| AZM-021 | azmath | Entscheidung | Brent vorhanden | Chandrupatla nur nach begründetem Vergleich; geklammerte Nullstellen, Randpunkte, unzulässige Intervalle und Stagnation testen |

## Mittelfristig

| ID | Repo | Status | Voraussetzungen | Abnahme |
|---|---|---|---|---|
| AZM-030 | azmath | Offen | AZM-010 | Separable symplektische Verlet-Schritte; Ordnung 2, Zeitumkehr, beschränkter Energiefehler am Oszillator, konstante/allokationsfreie Puffer |
| AZM-031 | azmath | Offen | AZM-010, Newton/lineare Löser vorhanden | Implizites Gauss–Legendre-RK, zunächst zwei Stufen/Ordnung 4; Stufenkonvergenz, Rückwärtsintegration und strukturtreue Referenzfälle |
| AZP-010 | azphysics | Offen | AZP-001, PLAN-001 | SI-/Einheitenkonventionen, Koordinaten, Zustandslayout und Fehlerdiagnosen festgelegt; ungültige Massen/Zeitschritte abgewiesen |
| AZP-020 | azphysics | Offen | AZM-010, AZP-010, benötigter Integrator wie AZM-030 | Freier Flug und Oszillator gegen analytische Lösungen, Energie-/Impulsdiagnostik und reproduzierbare Beispiele; azmath als Git-Abhängigkeit mit festem `rev` (D-006); `cargo test` in der CI (D-007) |
| GEO-001 | beide | Entscheidung | Konkrete Anforderungen aus AZP-020 | Allgemeine Geometrie versus simulationsspezifische Beschleunigung abgrenzen; vorerst kein viertes Repository voraussetzen |
| AZP-030 | azphysics | Offen | GEO-001, geometrische Primitiven | BVH/Spatial Hash: Kandidaten gegen Brute-Force, leere Mengen, Zellgrenzen, Updates und deterministische Ergebnisse |
| AZM-040 | azmath | Offen | AZM-010, Sparse/FDM/Multigrid vorhanden | Generische Neumann-/periodische Operatoren, Nullraumbehandlung, RHS-Kompatibilität und Gitter-Konvergenztests |
| AZM-041 | azmath | Offen | AZM-040, FEM P1 vorhanden | Neumann-/Robin-FEM, anschließend höhere Ordnung; analytische Lösungen, Konvergenzordnung und Bilanztests |
| AZP-040 | azphysics | Offen | AZP-010, AZP-020, für Kontakte AZP-030 | XPBD: Compliance/Zeitschritt-Skalierung, fixierte Teilchen, Distanzbedingungen, Grenzfälle und Kontakt-Referenzfälle |
| AZP-051 | azphysics | Offen | AZM-040, AZP-010, PCG vorhanden | Druckprojektion: Divergenz nach Projektion, Wände/offene Ränder, Neumann-Nullraum und physikalische Einheiten; keine duplizierte PCG-Implementierung |
| AZM-050 | azmath | Offen | QP/NLP, LDLT, Autodiff vorhanden | Wiederherstellungsphase und ggf. Maratos-Korrektur; schwierige NLPs, Zulässigkeit und Optimalitätsresiduen gegen Referenzen |

## Langfristig

| ID | Repo | Status | Voraussetzungen | Abnahme |
|---|---|---|---|---|
| AZP-050 | azphysics | Offen | AZP-020, AZP-051, Transfer-/Gitterkonzept | PIC/FLIP/APIC getrennt verifizieren; konstante/affine Felder, Masse/Impuls, Energieverhalten und Randbehandlung; danach Kopplung |
| AZM-042 | azmath | Offen | AZM-040, Skalierungsbedarf durch Benchmarks | AMG-Hierarchie, Vorkonditionierer-Eigenschaften, Iterationsgewinn und Speicherbedarf auf mehreren Problemklassen |
| AZM-060 | azmath | Offen | Reeller nichtsymmetrischer Eigenlöser vorhanden | Reelle Schur-Vektoren/Eigenvektoren und Arnoldi-Ritz-Paare; Rekonstruktion und Residuen. Vorhandene komplexe Scalar-Verfahren separat berücksichtigen |
| AZM-070 | azmath | Offen | Scalar/Complex und komplexe Sparse-LU/GMRES vorhanden | Weitere komplexe Sparse-Verfahren mit passenden hermiteschen Voraussetzungen und f32/f64-Komplex-Referenzen |
| AZM-080 | azmath | Offen | AZM-001, vorhandene special-Referenzen | Große Formparameter, breite Raster, Nullstellennähe und Subnormalgrenzen; Genauigkeitsbudgets und Konvergenzgrenzen explizit ausweisen |
| AZP-060 | azphysics | Entscheidung | AZP-010, konkrete Materialanforderungen | IAPWS-Testimplementierung inventarisieren; Anbindung oder Übernahme entscheiden, Referenzzustände und Gültigkeitsbereiche erhalten |
| AZP-070 | azphysics | Offen | Validierte Modelle, AZP-050/AZP-060 nach Bedarf | SPH, weitere Strömung/Thermodynamik als eigene Modellpakete mit getrennten Annahmen und Validierung |

## Weitere Ideen bleiben erhalten

Die [archivierte Roadmap](docs/archive/azmath-roadmap-2026-11-06.md) enthält
u. a. eingebettete Radau-/Rosenbrock-Fehlerschätzer, Taylor-ODE, allgemeine
PDE-Erweiterungen und weitere Algebra-/Optimierungsarbeiten. Vor Aufnahme in
einen aktiven Horizont bekommt jede Idee hier eine ID, eine Zuständigkeit,
prüfbare Voraussetzungen und eine Abnahme. Ein alter Status wird am Quelltext
und an Tests überprüft; „bewusst ausgelassen“ bedeutet weiterhin prüfbar offen.
