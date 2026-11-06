# Stand und Nachweise

Stand: 2026-11-07. Grundlage ist der tatsächliche Quelltext, ergänzt um lokale
Prüfungen. Eine konfigurierte CI ist kein Nachweis für einen bereits erfolgreich
durchgelaufenen GitHub-Job.

## azmath

Version **0.0.1** laut Cargo.toml. Stand dieses Arbeitspakets:
`b094d8ca132447f8ac26d7c2b4a7d7441c55316b`.

| Bereich | Vorhanden | Nachweis / verbleibende Grenze |
|---|---|---|
| Direkte lineare Algebra | LU, pivotierte QR, Cholesky, SVD, LDLT, Refinement | Tests im Code-Repository; performance- und parameterbezogene Grenzen bleiben relevant |
| Iterative Löser | CG/PCG, GMRES, BiCGSTAB und Vorkonditionierer | Arnoldi-/Lanczos-Invarianten für f32/f64; MINRES/PMINRES mit konstantem Speicher vorhanden |
| Sparse | Formate, Umordnungen, direkte Löser, Musterwiederverwendung | Property- und Robustheitstests; komplexer Ausbau/AMG weiter offen |
| Nichtlineares | Brent, Newton, LM, BFGS/L-BFGS und weitere Optimierung | Chandrupatla fehlt; schwierige NLPs benötigen weitere Robustheit |
| ODE/PDE | Explizite/implizite ODE, Radau/ROS2/BDF, Grunddiskretisierungen | Verlet und implizites Gauss–Legendre-RK fehlen; Quadratur ist bereits vorhanden |
| Statistik | CDF, direkte SF, Quantil und ISF für acht Verteilungsfamilien | 606 Referenzfälle, neun Robustheits-/Invariantentests; große Formparameter nicht umfassend abgedeckt |

Lokal bestanden: je **1.211 Tests inklusive 93 Doctests** mit allen Features
bzw. libm+alloc; Clippy, Formatierung, Rustdoc, Embedded-libm+alloc-Build und
drei micromath-Smoke-Tests. Die Referenzgenauigkeit gilt für std/libm.

[Verteilungsbericht](https://github.com/Andie2302/azmath/blob/master/docs/quality/distributions.md),
[Lanczos/Fuzzing](https://github.com/Andie2302/azmath/blob/master/docs/quality/lanczos-fuzz.md),
[frühere Baseline](https://github.com/Andie2302/azmath/blob/master/docs/quality/coverage-special.md).

Ein einstündiger Fuzz-Lauf des Targets `qp` (moderate Werte, `-max_total_time=3600`)
endete ohne Fund nach 3.411.026 Durchläufen. Er gehört zum damaligen Target und erweitert sich nicht automatisch
auf später ergänzte Assertions oder sämtliche Module.

Am früheren Stand `8e01f6a` fehlten MINRES, Chandrupatla, Verlet und implizites
Gauss–Legendre-RK. MINRES/PMINRES ist jetzt implementiert. Chandrupatla, Verlet-
Integratoren und implizites Gauss–Legendre-RK bleiben offen; bloße Worttreffer
wie „Verletzung“ sind kein Nachweis eines Integrators.

AZM-010: Publish-Dry-Run und 1.211 Tests aus dem entpackten Crate erfolgreich;
Rust-1.85.0-Builds für alle Features und libm+alloc sowie 15 MINRES-Tests bestanden.
AZM-020: MINRES vergleicht 32 symmetrisch-indefinite Systeme pro Typ mit LU,
prüft Fehlerpfade und Skalierungen und bestätigt das ursprüngliche Residuum.
Beim gezielten Diagonaltest reduziert Vorkonditionierung 14 auf 2 Iterationen
(f32) bzw. 10 auf 2 (f64).
[Release-/MINRES-Bericht](https://github.com/Andie2302/azmath/blob/master/docs/quality/release-minres.md).
Das ist technische Release-Vorbereitung, keine crates.io-Veröffentlichung;
diese bleibt AZM-011.

## azphysics

Version **0.0.1**, Gerüst mit direkter Git-Abhängigkeit, noch ohne Physikmodelle.
AZP-002 bindet azmath an `c839bc7f2d83c6f4a2a9baef20abda2ea03fc2a9`.
Dieser Commit ergänzt gegenüber dem oben geprüften Rechenstand nur den
experimentellen README-Hinweis; alle 18 azmath-CI-Prüfungen waren erfolgreich.

Backend-Features std/libm/micromath und alloc werden ohne implizite azmath-
Default-Features weitergeleitet. `azphysics::Real` ist das Trait dieser
Abhängigkeit. Cargo.toml und Cargo.lock halten die Revision fest; neue Commits
auf master ändern sie nicht automatisch. Ein bewusstes rev-Update folgt D-006.

Lokal geprüft: zwei Integrationstests je Backend, mit alloc drei; std,
libm und micromath jeweils mit/ohne alloc sowie alle Features. Ohne Backend
werden die Tests nur kompiliert. Embedded-Builds mit libm und libm+alloc,
Rust 1.85.0 mit std und libm+alloc, Clippy, Formatierung und Rustdoc bestanden.
Micromath nutzt beim Näherungstest ein lockeres Smoke-Budget; daraus folgt
keine Freigabe für genaue Physikberechnungen.

Die CI führt diese Backend-Matrix und cargo test aus (D-007). Die Tests
verifizieren Trait-/Löser-Nutzung und alloc-Weiterleitung, keine Physikmodelle.
Ein Git-Abhängigkeitsupdate ist keine crates.io-Veröffentlichung (AZM-011).

## Planung

PLAN-001 ist als erste gemeinsame Planung umgesetzt. Der alte ausführliche
Ideenbestand ist vollständig im [Archiv](docs/archive/azmath-roadmap-2026-11-06.md)
erhalten. Neue Prioritäten und Statusänderungen werden im aktiven Backlog
gepflegt. Ein nächster Statuseintrag nennt wieder den geprüften Commit und
überschreibt historische Prüfergebnisse nicht mit einer globalen Garantie.
