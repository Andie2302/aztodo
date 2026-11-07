# Git-Arbeitsweise für azmath, azphysics und aztodo

Kurzes Nachschlagewerk, damit man zwischen den drei Repositories nicht den Überblick verliert.
Stand: 2026-11-07 (Grundregel „direkt auf master“ ergänzt).

## Festlegungen

- **Standardbranch heißt in allen drei Repositories `master`** (azmath, azphysics, aztodo).
  Links auf Dateien verwenden deshalb `.../blob/master/...`.
- **Sichtbarkeit:** azmath, azphysics und aztodo sind öffentlich (laut GitHub, Stand 2026-11-06).
  azmath wurde dafür am 2026-11-06 umgestellt, damit azphysics es per Git einbinden kann.
  Alles, was committet wird, ist damit für alle lesbar, auch die Historie.
- **azphysics bindet azmath mit festem `rev`** ein (Entscheidung D-006), nie mit einem
  Branch-Namen. Das `rev` ist ein Commit-Hash aus `master` von azmath und wird bewusst
  aktualisiert; der geprüfte Commit steht in [STATUS.md](../STATUS.md).

## Ablauf einer Änderung

**Es wird ausschließlich direkt auf `master` gearbeitet** (azmath, azphysics, aztodo),
auch bei Code-Änderungen. Es werden **keine** Branches und **keine** Pull Requests angelegt,
ohne Ausnahme.

Ablauf: `git pull`, ändern, committen, `git push` auf `master`.

- **Tests und Prüfwerkzeuge** (`cargo test`, `cargo check`, `cargo clippy`, `cargo fmt`, Fuzzer)
  werden in KI-Arbeitssitzungen **nicht ausgeführt**. Der Projektinhaber lässt Tests bei sich
  laufen; die CI auf GitHub prüft nach dem Push (in aztodo gibt es keine CI).
- Ein roter CI-Lauf auf `master` wird danach durch einen weiteren Commit auf `master` behoben.
- azphysics bindet azmath mit festem `rev` ein (D-006); ein kurzzeitig roter `master` in azmath
  betrifft azphysics daher nicht.

## Was „ahead“ und „behind“ bedeutet

| Anzeige | Bedeutung | Branch löschen? |
|---|---|---|
| 0 ahead, N behind | Nichts Eigenes, alles steckt schon in `master` (typisch nach einem Merge) | Ja |
| N ahead, 0 behind | Eigene, noch nicht gemergte Arbeit | Nein, erst mergen oder bewusst verwerfen |
| N ahead, M behind | Eigene Arbeit, und `master` ist weitergelaufen | Nein |

Reste alter Arbeitsbranches (aus der Zeit vor dieser Regel) können gelöscht werden, wenn sie
0 Commits „ahead“ sind.

## Branch-Schutz (empfohlen, nicht erzwungen)

Der GitHub-Hinweis „Your master branch isn't protected“ ist ein Vorschlag, keine Warnung.
Fremde können ohne Schreibrecht nichts in `master` schreiben; sie können nur Forks und
Pull Requests anlegen. Sinnvoll für ein öffentliches Repository:

- **Restrict deletions** und **Block force pushes** aktivieren.
- Optional **Require status checks to pass** (azmath: Jobs `lint`, `Test (...)`, `MSRV 1.85`).
- **Nicht** „Require a pull request“ oder „Require approvals“ aktivieren, solange man allein
  arbeitet; sonst lassen sich eigene PRs nicht mehr selbst mergen.

## Hinweise zu Klonen

- Ein leer geklontes Repository hat anfangs keinen lokalen `origin/master`. Dann zuerst
  `git fetch origin master` und danach mit `FETCH_HEAD` weiterarbeiten.
- Nach einem Umbenennen des Standardbranches bei bestehenden Klonen:
  `git branch -m main master && git fetch origin && git branch -u origin/master master`.
- Vor dem Pushen immer `git pull` auf den aktuellen `master`, besonders wenn parallel in
  einem anderen Repository gearbeitet wird.
