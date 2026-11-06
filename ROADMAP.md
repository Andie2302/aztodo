# Gemeinsame Roadmap

Stand: 2026-11-07. Status und Abnahmekriterien sind in [BACKLOG.md](BACKLOG.md)
verbindlich benannt; die hier gezeigten Horizonte legen die Reihenfolge fest.

## Kurzfristig: Grundlage verlässlich und übersichtlich machen

| Reihenfolge | Aufgabe | Voraussetzung | Ergebnis |
|---|---|---|---|
| 1 | AZM-001 Verteilungen und Quantile | Bestehende Spezialfunktionen | Direkte obere Tails, Referenzen und Fehlerpfade; abgeschlossen in diesem Arbeitspaket |
| 2 | PLAN-001 Gemeinsame Planung | Repository-Grenzen geklärt | Dieses Repository und verlinkte lokale Roadmaps |
| 3 | AZP-001 Physik-Gerüst | PLAN-001 | Kleine kompilierbare Struktur mit no_std/alloc-Grenzen und CI; Modelle bleiben offen |
| 4 | AZM-010 Basisprüfung und Release-Vorbereitung | AZM-001, vorhandene Krylov-/Sparse-Tests | Abgeschlossen: Paket-Dry-Run, entpackte Tests, MSRV und dokumentierte Prüfmatrix |
| 5 | AZM-020 MINRES | AZM-010, LinearOperator, Vorkonditionierer | Abgeschlossen: MINRES/PMINRES, 15 f32/f64-/CSR-Tests und originale Residuenprüfung |

AZP-002 ist ebenfalls abgeschlossen: azphysics verwendet azmath direkt per
Git mit festem `rev`, Feature-Weiterleitung und eigenem Integrationstest.

Chandrupatla (AZM-021) ist eine nachrangige Ergänzung: Brent ist bereits da.
Die Entscheidung hängt von messbarem Nutzen an problematischen Nullstellen ab.

**Als Nächstes aktiv:** AZP-010 und AZP-020 in azphysics (mit azmath-`rev` ab
`b3418ef`, das Verlet enthält); azmath-seitig AZM-031. Eine tatsächliche Veröffentlichung ist als
AZM-011 separat zu entscheiden; der erfolgreiche Dry-Run veröffentlicht nichts.

## Mittelfristig: Werkzeuge für erste physikalische Modelle

1. AZM-030 Verlet für separable Hamilton-Systeme (**erledigt**, `b3418ef`) und
   AZM-031 implizites Gauss–Legendre-Runge–Kutta. Gauss–Legendre-**Quadratur** ist bereits vorhanden;
   der Integrator ist eine eigene Aufgabe.
2. AZP-010 Einheiten-/Zustandskonventionen und AZP-020 erste Referenzmodelle
   (freier Flug, harmonischer Oszillator). Integration und lineare Algebra
   werden aus azmath wiederverwendet.
3. AZM-040 Randbedingungen und Nullräume bei generischen PDE-Operatoren;
   AZP-051 physikalische Druckprojektion darauf aufbauen.
4. GEO-001 Zuständigkeit räumlicher Beschleunigungsstrukturen klären, danach
   AZP-030 BVH/Spatial Hash und AZP-040 XPBD.
5. AZM-041 FEM-Randbedingungen und AZM-050 NLP-Robustheit nach Bedarf konkreter
   Modelle. Zusätzliche Fehlerpfade und f32-Tests begleiten jeden Schritt.

## Langfristig: Partikel-Gitter-Simulation und größerer Umfang

- AZP-050 APIC/FLIP: Partikel-Gitter-Transfers, affine Geschwindigkeiten,
  Interpolation und Randbehandlung; gekoppelt mit AZP-051 Druckprojektion.
- AZM-042 algebraisches Multigrid, wenn Benchmarks den Bedarf zeigen.
- AZM-060 reelle Schur-Vektoren/Eigenvektoren, AZM-070 weitere komplexe
  Sparse-Verfahren sowie AZM-080 größere Parameterbereiche der Spezialfunktionen.
- AZP-060 Materialmodelle und mögliche IAPWS-Anbindung; spätere SPH-/Strömungs-
  und Thermodynamikmodule nur mit getrennten Referenzfällen und Modellannahmen.
- Die übrigen Ideen der archivierten Roadmap behalten ihren Platz als
  Kandidaten. Sie sind weder verloren noch ohne Prüfung als erledigt markiert.

## Freigabe eines ersten Physikmodells

Vor AZP-020 müssen AZM-010 und AZP-010 abgeschlossen sein und der benötigte
Integrator seine Abnahmekriterien erfüllen. Für einen Oszillator ist das
beispielsweise AZM-030; für XPBD sind zusätzlich Zwangsbedingungen und Kontakte
relevant; APIC/FLIP benötigt Transfers und einen separat validierten Druckschritt.
Ein offenes AMG- oder Schur-Projekt blockiert deshalb keinen einfachen Oszillator.
