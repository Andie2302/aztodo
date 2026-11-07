# Stand und Nachweise

Stand: 2026-11-07. Grundlage sind Quelltext und GitHub CI; ältere lokale
Prüfungen sind als historische Nachweise markiert. Eine konfigurierte CI ist kein Nachweis für einen bereits erfolgreich
durchgelaufenen GitHub-Job.

## Aktueller Repository-Abgleich

Am 2026-11-07 wurden die öffentlichen master-Stände und ihre GitHub-Checks erneut geprüft:

| Repository | master-Commit | Nachweis |
|---|---|---|
| azmath | `d38e903614d479c56250b19e20f357cc058979ce` | [CI: 18/18 erfolgreich](https://github.com/Andie2302/azmath/actions/runs/42320812517) |
| azphysics | `4e8354f8c2b8cc4c2cce04ea099203134eaaad6f` | [CI: 14/14 erfolgreich](https://github.com/Andie2302/azphysics/actions/runs/42320846762) |

Diese Stände ergänzen die unten genannten Code-Merges um Dokumentation.
azphysics pinnt weiterhin den geprüften azmath-Code-Merge `75c5dc04`; ein
neuerer Dokumentationscommit erfordert kein automatisches Dependency-Update.
Beide Cargo-Versionen bleiben **0.0.1**. aztodo enthält keinen Runtime-Code und
hat keine eigene CI; PLAN-002 wird durch Quellenabgleich sowie Dokumentprüfungen belegt.

### Bestand und noch offene Qualitätserweiterungen

- azmath enthält bereits Arnoldi-f32/f64-Invarianten, ILU(0)/IC(0)/SSOR-Tests,
  Property-Tests und FFT-/Sparse-/special-/QP-Fuzz-Targets. Keine erneute pauschale
  Einstufung als „ungetestet“. Zusätzliche Präzisions-/Fehlerpfadabdeckung: QUAL-001/002.
- Coverage-Artefakt, micromath-Smoke, Embedded, MSRV, Rustdoc und
  Publish-Dry-Run/entpackte Tests sind bereits in CI vorhanden. Aktuelle
  Coverage-Analyse und belastbare Schwellenwerte bleiben QUAL-003.
- Benchmark-Beispiele und Berichte existieren; reproduzierbare Performance-
  Regressionserkennung ist noch offen (QUAL-004). Der GL4-/Verlet-Vergleich ist kein Zeitbenchmark.
- Radau/ROS2 adaptiv nutzen bisher Schrittverdopplung; eingebettete Schätzer
  sind jetzt explizite offene Pakete AZM-032/033. SEULEX existiert bereits.
- Räumliche Suche, XPBD, kombinierbare Kraft-/Dämpfungsmodelle, erweiterte
  PDE-/FEM-Ränder und Druckprojektion werden hier weiterhin nicht als implementiert ausgegeben.

PLAN-002: offene Aufgaben mit Umfang/Grenzen und Abnahmekriterien präzisiert;
Qualitätspakete und notwendige Aufteilungen ergänzt. Neue Anforderungen
sind geplant, nicht nachträglich bestandene Tests. Archiv und historische
Prüfergebnisse bleiben erhalten. Quellen: aktuelle Repository-Bäume,
CI-Konfigurationen, Qualitätsberichte und die oben verlinkten Läufe.

## azmath

Version **0.0.1** laut Cargo.toml. Numerischer Referenzstand:
`75c5dc04c51ddf02568f15f4ab3c46f7ba4c2c7c` (PR #7, Gauss–Legendre-RK4).
Die historische technische Basisprüfung unten gehört zu `b094d8ca`.

| Bereich | Vorhanden | Nachweis / verbleibende Grenze |
|---|---|---|
| Direkte lineare Algebra | LU, pivotierte QR, Cholesky, SVD, LDLT, Refinement | Tests im Code-Repository; performance- und parameterbezogene Grenzen bleiben relevant |
| Iterative Löser | CG/PCG, GMRES, BiCGSTAB und Vorkonditionierer | Arnoldi-/Lanczos-Invarianten für f32/f64; MINRES/PMINRES mit konstantem Speicher vorhanden |
| Sparse | Formate, Umordnungen, direkte Löser, Musterwiederverwendung | Property- und Robustheitstests; komplexer Ausbau/AMG weiter offen |
| Nichtlineares | Brent, Newton, LM, BFGS/L-BFGS und weitere Optimierung | Chandrupatla fehlt; schwierige NLPs benötigen weitere Robustheit |
| ODE/PDE | Explizite/implizite ODE, Radau/ROS2/BDF, Grunddiskretisierungen | symplektisches Verlet (`ode::verlet`, `ode::verlet_separable`) vorhanden; implizites Gauss–Legendre-RK4 mit analytischer/FD-Jacobi-Matrix vorhanden; Quadratur ist ein separates Verfahren |
| Statistik | CDF, direkte SF, Quantil und ISF für acht Verteilungsfamilien | 606 Referenzfälle, neun Robustheits-/Invariantentests; große Formparameter nicht umfassend abgedeckt |

Historische Basisprüfung (`b094d8ca`), lokal bestanden: je **1.211 Tests inklusive 93 Doctests** mit allen Features
bzw. libm+alloc; Clippy, Formatierung, Rustdoc, Embedded-libm+alloc-Build und
drei micromath-Smoke-Tests. Die Referenzgenauigkeit gilt für std/libm.

[Verteilungsbericht](https://github.com/Andie2302/azmath/blob/master/docs/quality/distributions.md),
[Lanczos/Fuzzing](https://github.com/Andie2302/azmath/blob/master/docs/quality/lanczos-fuzz.md),
[frühere Baseline](https://github.com/Andie2302/azmath/blob/master/docs/quality/coverage-special.md).

Ein einstündiger Fuzz-Lauf des Targets `qp` (moderate Werte, `-max_total_time=3600`)
endete ohne Fund nach 3.411.026 Durchläufen. Er gehört zum damaligen Target und erweitert sich nicht automatisch
auf später ergänzte Assertions oder sämtliche Module.

Am früheren Stand `8e01f6a` fehlten MINRES, Chandrupatla, Verlet und implizites
Gauss–Legendre-RK. MINRES/PMINRES ist jetzt implementiert, ebenso Verlet
(AZM-030, siehe unten) und Gauss–Legendre-RK4 (AZM-031). Chandrupatla bleibt
offen; bloße Worttreffer wie „Verletzung“ sind kein Nachweis eines Integrators.

AZM-010: Publish-Dry-Run und 1.211 Tests aus dem entpackten Crate erfolgreich;
Rust-1.85.0-Builds für alle Features und libm+alloc sowie 15 MINRES-Tests bestanden.
AZM-020: MINRES vergleicht 32 symmetrisch-indefinite Systeme pro Typ mit LU,
prüft Fehlerpfade und Skalierungen und bestätigt das ursprüngliche Residuum.
Beim gezielten Diagonaltest reduziert Vorkonditionierung 14 auf 2 Iterationen
(f32) bzw. 10 auf 2 (f64).
[Release-/MINRES-Bericht](https://github.com/Andie2302/azmath/blob/master/docs/quality/release-minres.md).
Das ist technische Release-Vorbereitung, keine crates.io-Veröffentlichung;
diese bleibt AZM-011.

AZM-030: `ode::verlet_separable` (Störmer-Verlet für `H = T(p) + V(q)`) und
`ode::verlet` (`q'' = a(t, q)`), Ordnung 2, symplektisch, zeitumkehrbar,
`verlet_work_len(n) = 4·n`. Stand: Commit `b3418efc34bdbabf983acc3bf7e5c679333ec0f3`
(PR #5). 16 Tests (`tests/ode_verlet.rs`): Konvergenzordnung 2, beschränkter
Energiefehler (Oszillator, Pendel) ohne Drift bei zehnfacher Laufzeit, Zeitumkehr,
exakte Integration bei konstanter Beschleunigung, separables System mit Masse,
Fehlerfälle, f32. Alle 18 azmath-CI-Jobs waren auf diesem Stand grün. Die
Tests wurden in der Arbeitssitzung nicht lokal ausgeführt; die erste Ausführung
war die CI (ein fehlerhafter Test wurde dabei korrigiert, der Code nicht).

Historische CI von azmath PR #6: 18 erfolgreiche Jobs; der All-Features-Lauf hat
**1.228 Tests inklusive Doctests** bestanden. Die Verlet-Dokumentation nennt
jetzt die Hamilton-Voraussetzungen, stabile Schrittweiten und die mögliche
Energiezufuhr durch zeitabhängige Kräfte. Keine numerische Implementierungsänderung.

AZM-031: Allokationsfreies zweistufiges Gauss–Legendre-RK4 mit festem Zeitraster,
gekoppeltem Newton-System, analytischer oder numerischer Jacobi-Matrix und
`gauss_legendre4_work_len(n) = 5*n*n + 21*n` Arbeitselementen.
[PR #7](https://github.com/Andie2302/azmath/pull/7), geprüfter Code-Commit
`d2ca1ad4cb28b92d976410dcb9ac9874976ed1fa`, Merge `75c5dc04`.
Alle 18 CI-Jobs erfolgreich:
[PR-Lauf](https://github.com/Andie2302/azmath/actions/runs/42320120483).
25 neue Tests (zwölf je Präzision und eine Workspace-Prüfung) plus ein Doctest: Ordnung 4,
Zeitumkehr, quadratische Invarianten, nicht separierbare Hamilton-Funktion,
nichtlineare/nichtautonome Systeme und Fehlerpfade. Bei Fehlschlag bleibt der
letzte vollständige Zustand erhalten. Newton-Toleranzen und Rundung begrenzen
Strukturtreue; keine adaptive Fehlerschätzung oder allgemeine exakte Energieerhaltung.
A-stabil, nicht L-stabil. Tests ausschließlich in CI ausgeführt.

## azphysics

Version **0.0.1**, erste Punktmassenmodelle mit direkter Git-Abhängigkeit.
AZP-010 und AZP-020 sind implementiert und nach grüner CI gemergt:
[PR #4](https://github.com/Andie2302/azphysics/pull/4), Code-Merge
`1528bea62aba8a856b5cb9b39fae507d0d18e878`.

- State enthält endliche kartesische Position/Geschwindigkeit; SI-Konventionen,
  positive Masse, nichtnegative Federkonstante und eindeutige Fehlerdiagnosen.
- Freie Punktmasse, konstantes Beschleunigungsfeld und harmonischer Oszillator
  mit Masse und Gleichgewichtslage. Energie-Nullpunkte und Impulsbilanz sind definiert.
- Das Modell-API integriert mit azmath-Verlet; der zusätzliche Vergleich nutzt auch Gauss–Legendre-RK4. Negative Schritte sind für
  Zeitumkehr erlaubt; ungültige Uhren werden abgewiesen. Fehler erhalten den
  letzten vollständig berechneten Zustand.
- 20 neue f32/f64-Tests für analytische Lösungen, Ordnung 2, Zeitumkehr,
  Energieverhalten, Parameter-/Zeit-/Bufferfehler und Überlaufbehandlung.
- Historischer Stand PR #4 zusammen mit den Integrationstests: 22 Tests ohne alloc und 23 mit alloc
  bei std/libm. Bei reinem micromath nur die bisherigen zwei/drei Smoke-Tests;
  keine präzise Physik-Freigabe.

Beim historischen Modellstand PR #4 waren alle 14 CI-Jobs sowohl für Push als auch PR erfolgreich:
[PR-Lauf](https://github.com/Andie2302/azphysics/actions/runs/42314757082).
Darin: Backend-Matrix, Compile-only ohne Backend, Embedded libm/libm+alloc,
Rust 1.85, Clippy, Formatierung und Rustdoc. In dieser Sitzung wurden gemäß
D-008 **keine lokalen Tests** ausgeführt; lokal nur Formatierung und
Cargo-Lockfile-Auflösung.

azmath-rev in Cargo.toml und Cargo.lock:
`75c5dc04c51ddf02568f15f4ab3c46f7ba4c2c7c`. Updates bleiben bewusst (D-006).
std/libm/micromath und alloc werden ohne implizite Default-Features weitergereicht.
[Konventionen](https://github.com/Andie2302/azphysics/blob/master/docs/CONVENTIONS.md),
[Validierungsbericht](https://github.com/Andie2302/azphysics/blob/master/docs/VALIDATION.md).
Das ist keine crates.io-Veröffentlichung (AZM-011).

AZM-031 in azphysics: [PR #5](https://github.com/Andie2302/azphysics/pull/5),
geprüfter Code-Commit `a674f7c54b70537800fe7c570a950a58b6a5222b`,
Merge `5ebcc1d51d5dec9727fa96df1b84a464dd8e184e`. Alle 14 Jobs für Push und
PR erfolgreich (28 Checks am Code-Commit):
[PR-Lauf](https://github.com/Andie2302/azphysics/actions/runs/42320450164),
[Push-Lauf](https://github.com/Andie2302/azphysics/actions/runs/42320436683).
Vier neue f32/f64-Vergleichstests bestätigen Ordnung 2/4 sowie das Energieverhalten
über 800 Schritte. Insgesamt 26 Tests ohne alloc bzw. 27 mit alloc bei std/libm.
Die CI führt auch das Vergleichsbeispiel mit genauen Backends aus. Für 100 Schritte
bis t=10 s liefert der f64-std-Lauf dimensionslose Zustandsfehler von etwa
`3.08e-2` (Verlet) bzw. `4.05e-5` (GL4); dafür 101 Kraftauswertungen gegenüber
600 RHS- und 200 Jacobi-Auswertungen. Gleiche Schrittzahlen sind kein gleicher
Rechenaufwand. Keine lokalen Tests; nur Formatierung und Lockfile-Auflösung.

## Planung

PLAN-001 ist als erste gemeinsame Planung umgesetzt. Der alte ausführliche
Ideenbestand ist vollständig im [Archiv](docs/archive/azmath-roadmap-2026-11-06.md)
erhalten. Neue Prioritäten und Statusänderungen werden im aktiven Backlog
gepflegt. Ein nächster Statuseintrag nennt wieder den geprüften Commit und
überschreibt historische Prüfergebnisse nicht mit einer globalen Garantie.
