# Stand und Nachweise

Stand: 2026-11-06. Grundlage ist der tatsächliche Quelltext, ergänzt um lokale
Prüfungen. Eine konfigurierte CI ist kein Nachweis für einen bereits erfolgreich
durchgelaufenen GitHub-Job.

## azmath

Version **0.0.1** laut Cargo.toml. Stand dieses Arbeitspakets:
`8e01f6a48afa9de344e0b009a71d75effd7bcbac`.

| Bereich | Vorhanden | Nachweis / verbleibende Grenze |
|---|---|---|
| Direkte lineare Algebra | LU, pivotierte QR, Cholesky, SVD, LDLT, Refinement | Tests im Code-Repository; performance- und parameterbezogene Grenzen bleiben relevant |
| Iterative Löser | CG/PCG, GMRES, BiCGSTAB und Vorkonditionierer | Arnoldi-/Lanczos-Invarianten für f32/f64; MINRES fehlt noch |
| Sparse | Formate, Umordnungen, direkte Löser, Musterwiederverwendung | Property- und Robustheitstests; komplexer Ausbau/AMG weiter offen |
| Nichtlineares | Brent, Newton, LM, BFGS/L-BFGS und weitere Optimierung | Chandrupatla fehlt; schwierige NLPs benötigen weitere Robustheit |
| ODE/PDE | Explizite/implizite ODE, Radau/ROS2/BDF, Grunddiskretisierungen | Verlet und implizites Gauss–Legendre-RK fehlen; Quadratur ist bereits vorhanden |
| Statistik | CDF, direkte SF, Quantil und ISF für acht Verteilungsfamilien | 606 Referenzfälle, neun Robustheits-/Invariantentests; große Formparameter nicht umfassend abgedeckt |

Lokal bestanden: je **1.195 Tests inklusive 92 Doctests** mit allen Features
bzw. libm+alloc; Clippy, Formatierung, Rustdoc, Embedded-libm+alloc-Build und
drei micromath-Smoke-Tests. Die Referenzgenauigkeit gilt für std/libm.

[Verteilungsbericht](https://github.com/Andie2302/azmath/blob/master/docs/quality/distributions.md),
[Lanczos/Fuzzing](https://github.com/Andie2302/azmath/blob/master/docs/quality/lanczos-fuzz.md),
[frühere Baseline](https://github.com/Andie2302/azmath/blob/master/docs/quality/coverage-special.md).

Ein einstündiger Fuzz-Lauf des Targets `qp` (moderate Werte, `-max_total_time=3600`)
endete ohne Fund nach 3.411.026 Durchläufen. Er gehört zum damaligen Target und erweitert sich nicht automatisch
auf später ergänzte Assertions oder sämtliche Module.

Gegen den Quelltext (Commit `8e01f6a`) geprüft: MINRES, Chandrupatla,
Verlet-Integratoren und implizites Gauss–Legendre-RK sind nicht vorhanden. Die
Textsuche „verlet“ trifft nur das deutsche Wort „Verletzung“ (Nebenbedingungen).

## azphysics

Version **0.0.1**, Gerüst ohne Physikverfahren. Lokal geprüft: Clippy mit allen
Features, Build ohne Default-Features, Embedded-Build ohne std mit alloc,
Formatierung und Rustdoc mit Warnungen als Fehlern. Die CI führt `cargo test` aus
(derzeit ohne Tests). MSRV 1.85 ist in CI
konfiguriert, wurde in dieser Arbeitsumgebung nicht separat ausgeführt.

Die azmath-Cargo-Abhängigkeit ist für das erste Modell geplant (siehe D-006). Solange sie
nicht eingebunden ist, gibt es auch keine behauptete Integration von
azmath-Backends in azphysics.

## Planung

PLAN-001 ist als erste gemeinsame Planung umgesetzt. Der alte ausführliche
Ideenbestand ist vollständig im [Archiv](docs/archive/azmath-roadmap-2026-11-06.md)
erhalten. Neue Prioritäten und Statusänderungen werden im aktiven Backlog
gepflegt. Ein nächster Statuseintrag nennt wieder den geprüften Commit und
überschreibt historische Prüfergebnisse nicht mit einer globalen Garantie.
