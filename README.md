# az-Projektplan

> **Zuletzt gegen azmath geprüft:** Commit `c839bc7f` (2026-11-07). Bei neueren Commits Status zuerst abgleichen.

Die gemeinsame Planung für [azmath](https://github.com/Andie2302/azmath) und
[azphysics](https://github.com/Andie2302/azphysics). Hier steht, was als Nächstes
kommt, wohin eine Funktion gehört und welche Voraussetzungen sie benötigt.

| Repository | Aufgabe | Aktueller Stand |
|---|---|---|
| azmath | Allgemeine Mathematik und numerische Verfahren | Implementiert; numerische Absicherung und Ausbau laufen |
| azphysics | Physikalische Modelle und Simulationen auf dieser Grundlage | Gerüst 0.0.1 mit fest gebundener azmath-Revision, Modelle noch geplant |
| aztodo | Gemeinsame Prioritäten, Abhängigkeiten und Entscheidungen | Zentrale Planung; enthält keinen Laufzeitcode |

**Abgeschlossen:** Verteilungsreferenzen, zentrale Roadmap, technische
Release-Vorbereitung, MINRES/PMINRES und direkte azphysics→azmath-Anbindung.
**Als Nächstes:** AZM-030, symplektisches Verlet für separable Hamilton-Systeme.
Eine crates.io-Veröffentlichung (AZM-011) ist gesondert zu entscheiden.

- [Stand und Nachweise](STATUS.md): Was tatsächlich vorhanden und geprüft ist.
- [Kurz-, mittel- und langfristige Schritte](ROADMAP.md): Die Reihenfolge.
- [Aufgaben mit Abnahmekriterien](BACKLOG.md): Feste IDs, Zuständigkeit und Voraussetzungen.
- [Architektur und Abhängigkeiten](ARCHITECTURE.md): Grenzen zwischen Mathematik und Physik.
- [Entscheidungen und Pflege](DECISIONS.md): Was feststeht und was noch offen ist.
- [Git-Arbeitsweise](docs/GIT-ARBEITSWEISE.md): Direkt auf `master` arbeiten, wann ein Branch sinnvoll ist, Branch-Schutz.
- [Alte azmath-Roadmap](docs/archive/azmath-roadmap-2026-11-06.md): Vollständiger Ideenspeicher vor der Neuordnung.

Die Zeiträume sind **Prioritätshorizonte**, keine zugesagten Termine. Eine
implementierte Methode ist erst für eine konkrete Anwendung freigegeben, wenn
ihre Eingabebereiche, Fehlerfälle und passenden Genauigkeitstests dokumentiert
sind. azphysics beginnt mit Modellen nach dieser Basisprüfung; vollständige
Fertigstellung sämtlicher Mathematikideen ist dafür nicht erforderlich.

Bei Änderungen an der Implementierung werden zuerst Tests und lokale
Qualitätsberichte aktualisiert, danach hier Status, Voraussetzungen und nächste
Aufgaben. Historische Hinweise wie „bewusst ausgelassen“ sind Anhaltspunkte für
offene Arbeit und keine dauerhaften Verbote.
