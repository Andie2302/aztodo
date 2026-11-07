# az-Projektplan

> **Standabgleich:** azmath `d38e9036`, azphysics `99da4a36` (2026-11-07).
> Die feste azmath-Abhängigkeit bleibt `75c5dc04`; Details und CI-Nachweise in STATUS.md.

Die gemeinsame Planung für [azmath](https://github.com/Andie2302/azmath) und
[azphysics](https://github.com/Andie2302/azphysics). Hier steht, was als Nächstes
kommt, wohin eine Funktion gehört und welche Voraussetzungen sie benötigt.

| Repository | Aufgabe | Aktueller Stand |
|---|---|---|
| azmath | Allgemeine Mathematik und numerische Verfahren | Implementiert; numerische Absicherung und Ausbau laufen |
| azphysics | Physikalische Modelle und Simulationen auf dieser Grundlage | Punktmassenmodelle und Spatial Hash 0.0.1, feste azmath-Revision und SI-Konventionen |
| aztodo | Gemeinsame Prioritäten, Abhängigkeiten und Entscheidungen | Zentrale Planung; enthält keinen Laufzeitcode |

**Abgeschlossen:** Verteilungsreferenzen, technische Release-Vorbereitung,
MINRES/PMINRES, Verlet, Gauss–Legendre-RK4, direkte Git-Anbindung sowie SI-Konventionen und erste
Physikmodelle (freier Flug, konstante Beschleunigung, harmonischer Oszillator),
GEO-001-Paketentscheidung und Spatial Hash für 2D/3D (AZP-030).
**Als Nächstes empfohlen:** XPBD-Distanzbedingungen (AZP-040), anschließend
Kontakte (AZP-041) mit dem vorhandenen Spatial Hash. BVH (AZP-031) ist unabhängig
und bleibt offen. QUAL-001–004 ergänzen die Qualitätssicherung.

Eine crates.io-Veröffentlichung (AZM-011) ist gesondert zu entscheiden.

- [Stand und Nachweise](STATUS.md): Was tatsächlich vorhanden und geprüft ist.
- [Kurz-, mittel- und langfristige Schritte](ROADMAP.md): Die Reihenfolge.
- [Aufgabenindex](BACKLOG.md): Feste IDs, Status, Zuständigkeit und Voraussetzungen.
- [Arbeitspakete im Detail](docs/ARBEITSPAKETE.md): Umfang, Grenzen und überprüfbare Kriterien.
- [Gemeinsame Abnahme](docs/ABNAHME.md): Fehlerbudgets, Referenzen und Abschlussnachweise.
- [Architektur und Abhängigkeiten](ARCHITECTURE.md): Grenzen zwischen Mathematik und Physik.
- [Entscheidungen und Pflege](DECISIONS.md): Was feststeht und was noch offen ist.
- [Git-Arbeitsweise](docs/GIT-ARBEITSWEISE.md): Direkt auf `master` arbeiten, wann ein Branch sinnvoll ist, Branch-Schutz.
- [Alte azmath-Roadmap](docs/archive/azmath-roadmap-2026-11-06.md): Vollständiger Ideenspeicher vor der Neuordnung.

Die Zeiträume sind **Prioritätshorizonte**, keine zugesagten Termine. Eine
implementierte Methode ist erst für eine konkrete Anwendung freigegeben, wenn
ihre Eingabebereiche, Fehlerfälle und passenden Genauigkeitstests dokumentiert
sind. azphysics beginnt mit Modellen nach dieser Basisprüfung; vollständige
Fertigstellung sämtlicher Mathematikideen ist dafür nicht erforderlich.

Bei Änderungen an der Implementierung werden zuerst Tests und Qualitätsberichte
im Code-Repository aktualisiert, danach hier Status, Voraussetzungen und nächste
Aufgaben. KI-Sitzungen führen gemäß Projektwunsch keine lokalen Tests aus;
Ausführung und Nachweise kommen aus GitHub CI oder vom Projektinhaber. Historische Hinweise wie „bewusst ausgelassen“ sind Anhaltspunkte für
offene Arbeit und keine dauerhaften Verbote.
