# Stand und Nachweise

Stand: 2026-11-07. Grundlage sind Quelltext und GitHub CI; ältere lokale
Prüfungen sind als historische Nachweise markiert. Eine konfigurierte CI ist kein Nachweis für einen bereits erfolgreich
durchgelaufenen GitHub-Job.

## azmath

Version **0.0.1** laut Cargo.toml. Aktueller Referenzstand:
`3c6068a5f218aaba940a85a73606c9f61c8ebdc9` (PR #6, Verlet-Dokumentation präzisiert).
Die historische technische Basisprüfung unten gehört zu `b094d8ca`.

| Bereich | Vorhanden | Nachweis / verbleibende Grenze |
|---|---|---|
| Direkte lineare Algebra | LU, pivotierte QR, Cholesky, SVD, LDLT, Refinement | Tests im Code-Repository; performance- und parameterbezogene Grenzen bleiben relevant |
| Iterative Löser | CG/PCG, GMRES, BiCGSTAB und Vorkonditionierer | Arnoldi-/Lanczos-Invarianten für f32/f64; MINRES/PMINRES mit konstantem Speicher vorhanden |
| Sparse | Formate, Umordnungen, direkte Löser, Musterwiederverwendung | Property- und Robustheitstests; komplexer Ausbau/AMG weiter offen |
| Nichtlineares | Brent, Newton, LM, BFGS/L-BFGS und weitere Optimierung | Chandrupatla fehlt; schwierige NLPs benötigen weitere Robustheit |
| ODE/PDE | Explizite/implizite ODE, Radau/ROS2/BDF, Grunddiskretisierungen | symplektisches Verlet (`ode::verlet`, `ode::verlet_separable`) vorhanden; implizites Gauss–Legendre-RK fehlt; Quadratur ist bereits vorhanden |
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
(AZM-030, siehe unten). Chandrupatla und implizites Gauss–Legendre-RK bleiben
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

Aktuelle CI von azmath PR #6: 18 erfolgreiche Jobs; der All-Features-Lauf hat
**1.228 Tests inklusive Doctests** bestanden. Die Verlet-Dokumentation nennt
jetzt die Hamilton-Voraussetzungen, stabile Schrittweiten und die mögliche
Energiezufuhr durch zeitabhängige Kräfte. Keine numerische Implementierungsänderung.

## azphysics

Version **0.0.1**, erste Punktmassenmodelle mit direkter Git-Abhängigkeit.
AZP-010 und AZP-020 sind implementiert und nach grüner CI gemergt:
[PR #4](https://github.com/Andie2302/azphysics/pull/4), Code-Merge
`1528bea62aba8a856b5cb9b39fae507d0d18e878`.

- State enthält endliche kartesische Position/Geschwindigkeit; SI-Konventionen,
  positive Masse, nichtnegative Federkonstante und eindeutige Fehlerdiagnosen.
- Freie Punktmasse, konstantes Beschleunigungsfeld und harmonischer Oszillator
  mit Masse und Gleichgewichtslage. Energie-Nullpunkte und Impulsbilanz sind definiert.
- Integration ausschließlich mit azmath-Verlet. Negative Schritte sind für
  Zeitumkehr erlaubt; ungültige Uhren werden abgewiesen. Fehler erhalten den
  letzten vollständig berechneten Zustand.
- 20 neue f32/f64-Tests für analytische Lösungen, Ordnung 2, Zeitumkehr,
  Energieverhalten, Parameter-/Zeit-/Bufferfehler und Überlaufbehandlung.
- Zusammen mit den Integrationstests: 22 Tests ohne alloc und 23 mit alloc
  bei std/libm. Bei reinem micromath nur die bisherigen zwei/drei Smoke-Tests;
  keine präzise Physik-Freigabe.

Alle 14 CI-Jobs waren sowohl für Push als auch PR erfolgreich:
[PR-Lauf](https://github.com/Andie2302/azphysics/actions/runs/42314757082).
Darin: Backend-Matrix, Compile-only ohne Backend, Embedded libm/libm+alloc,
Rust 1.85, Clippy, Formatierung und Rustdoc. In dieser Sitzung wurden gemäß
D-008 **keine lokalen Tests** ausgeführt; lokal nur Formatierung und
Cargo-Lockfile-Auflösung.

azmath-rev in Cargo.toml und Cargo.lock:
`3c6068a5f218aaba940a85a73606c9f61c8ebdc9`. Updates bleiben bewusst (D-006).
std/libm/micromath und alloc werden ohne implizite Default-Features weitergereicht.
[Konventionen](https://github.com/Andie2302/azphysics/blob/master/docs/CONVENTIONS.md),
[Validierungsbericht](https://github.com/Andie2302/azphysics/blob/master/docs/VALIDATION.md).
Das ist keine crates.io-Veröffentlichung (AZM-011).

## Planung

PLAN-001 ist als erste gemeinsame Planung umgesetzt. Der alte ausführliche
Ideenbestand ist vollständig im [Archiv](docs/archive/azmath-roadmap-2026-11-06.md)
erhalten. Neue Prioritäten und Statusänderungen werden im aktiven Backlog
gepflegt. Ein nächster Statuseintrag nennt wieder den geprüften Commit und
überschreibt historische Prüfergebnisse nicht mit einer globalen Garantie.
