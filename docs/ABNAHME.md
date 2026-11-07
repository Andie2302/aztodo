# Gemeinsame Abnahmeregeln

Stand: 2026-11-07. Diese Regeln gelten für neue Arbeitspakete. Historische
Abschlüsse werden nicht nachträglich als umfassender geprüft ausgegeben.
Die konkreten Anforderungen stehen in [ARBEITSPAKETE.md](ARBEITSPAKETE.md).

## Status und Abschluss

- **Offen:** geplant, noch nicht vollständig implementiert oder geprüft.
- **Entscheidung:** Alternativen, Zuständigkeit oder Gültigkeitsbereich müssen
  zuerst schriftlich entschieden werden. Das ist keine Implementierungsfreigabe.
- **In Arbeit:** konkreter Branch/PR und bearbeiteter Teilumfang sind verlinkt.
- **Blockiert:** fehlende Voraussetzung und betroffene ID sind genannt.
- **Erledigt:** alle vereinbarten Kriterien erfüllt; Code-Commit, Tests,
  CI-Lauf und verbleibende Grenzen sind in STATUS.md oder einem dort verlinkten
  Bericht nachgewiesen. Ein grüner Build allein genügt für Numerik nicht.

Ein Entscheidungspaket ist abgeschlossen, wenn Alternativen, gewählte Lösung,
Begründung und Folgen in DECISIONS.md stehen. Benötigte Folgeimplementierungen
bleiben eigene offene Aufgaben. Ein offenes Langfristthema blockiert andere
Pakete nur bei einer ausdrücklich angegebenen Abhängigkeit.

## Mindestumfang eines Implementierungspakets

1. **Vertrag:** Eingaben, Dimensionen, Einheiten in azphysics, mathematische
   Voraussetzungen, Fehlerfälle, Speicherbedarf und Zustandsverhalten bei Fehlern
   dokumentieren. `no_std`, Backend- und alloc-Grenzen explizit nennen.
2. **Korrektheit:** unabhängige analytische oder hochpräzise Referenz und mindestens
   eine passende Invariante. Ein Vergleich zweier Varianten desselben Algorithmus
   allein ist keine unabhängige Referenz.
3. **Robustheit:** leere/kleinste zulässige Eingabe, ungültige Dimensionen/Parameter,
   NaN/Inf und fachlich relevante singuläre oder schlecht konditionierte Fälle
   prüfen. Kein Panic und kein unbeschränktes Iterieren bei gültigem API-Aufruf;
   ausgeschriebene Panic-Verträge bleiben ausdrücklich gekennzeichnet.
4. **Präzision:** numerische Kernfälle für f32 und f64 mit std und libm ausführen.
   Abweichende Unterstützung begründen. Micromath erhält eigene begrenzte
   Smoke-Tests, keine pauschale Freigabe durch gelockerte globale Toleranzen.
5. **Nachweis:** vor Code-Merge die betroffenen CI-Jobs erfolgreich; README/API-Doku,
   Changelog im Code-Repository und zentralen Status aktualisieren. azphysics-
   Abhängigkeiten nur gemeinsam in Cargo.toml/Cargo.lock bewusst aktualisieren.

Gemäß Projektwunsch führen KI-Sitzungen keine lokalen Tests aus. Ausführung
erfolgt in GitHub CI oder durch den Projektinhaber; Quelle, Commit und Ergebnis
werden angegeben. Reine Planungsänderungen benötigen keinen Rust-Testlauf.

## Messgrößen und Toleranzen

- Vor der Implementierung pro Referenzfall ein Fehlerbudget festlegen:
  `Fehler <= atol + rtol * Referenzskala`. Dimension, Datentyp, Backend und
  Kondition berücksichtigen. Nahe null absolute Fehler prüfen; keine Division
  durch null. Toleranzen nicht allein an beobachtete Fehler einer neuen Lösung anpassen.
- Lineare Systeme: ursprüngliches Residuum und Rückwärtsfehler prüfen,
  z. B. `||Ax-b|| / (||A||*||x||+||b||)`. Den Nullnenner separat behandeln.
  Ein intern vorkonditioniertes Residuum reicht nicht aus.
- Nichtlineare Systeme: Zulässigkeit und ursprüngliches Residuum kontrollieren;
  bei Optimierung zusätzlich Stationarität/KKT und Komplementarität passend
  zum Problem. Ein kleiner Schritt ist kein Konvergenznachweis.
- Konvergenzordnung: mindestens drei Auflösungen `h, h/2, h/4`, Fehler und
  beobachtete Ordnung `log2(E(h)/E(h/2))` angeben. Das verwendete Band um die
  theoretische Ordnung vorab begründen; Rundungs-/Newtonfehler dürfen den
  betrachteten Bereich nicht dominieren.
- Energie/Impuls: Gültigkeitsannahmen, Normierung, Zeitschritt und Laufzeit
  angeben. Bei konservativen Verfahren maximale Abweichung über den Verlauf
  und eine zehnfach längere Laufzeit prüfen; Endpunkt allein übersieht Oszillationen.
  Bei Dissipation oder äußeren Kräften die passende Bilanz statt Konstanz prüfen.
- Spezialfunktionen: Referenzgenerator, Präzision und Zieltyp-Rundung nennen;
  ULP-Budget pro Funktion/Bereich, absolute Fehler nahe Nullstellen und
  gesondertes Verhalten an Polen, Tails und Über-/Unterlaufgrenzen.

## Abschlussdatensatz

Jedes erledigte Paket nennt: ID, Repository, Code-Commit/PR/Merge, geprüfte
Backends und Datentypen, Referenzfälle und Budgets, CI-URL sowie bekannte
Grenzen. Benchmarks zusätzlich Hardware/Toolchain/Profil; Fuzzing zusätzlich
Target, Commit, Seed/Corpus, Zeitbudget und Ergebnis. Nachweise gelten für
diesen Stand und werden bei Änderungen nicht automatisch übertragen.
