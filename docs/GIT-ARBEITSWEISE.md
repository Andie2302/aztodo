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

**Grundregel: direkt auf `master` arbeiten**, außer ein eigener Branch ist sinnvoller.

| Art der Änderung | Vorgehen |
|---|---|
| Dokumentation, Links, Notizen, Planungsdateien (besonders in aztodo), kleine risikoarme Korrekturen | Direkt auf `master` committen und pushen |
| Änderungen an `src/`, neue Verfahren, CI-Dateien, größere oder riskante Umbauten | Eigener Branch und Pull Request, damit die CI vor dem Merge prüft und `master` grün bleibt |
| Arbeit, die mehrere Schritte braucht oder bewusst erst später fertig wird | Eigener Branch |

Grund: Eine KI-Sitzung führt Tests nicht lokal aus (Wunsch des Projektinhabers; Tests laufen bei ihm).
Die CI auf GitHub ist dann die erste Prüfung. Deshalb gehört Code vor dem Merge in einen Pull Request.
azphysics bindet azmath außerdem mit festem `rev` ein (D-006), ein roter `master` in azmath stört also
nur, wenn das `rev` darauf zeigt.

Ablauf mit Branch:

1. Neuen Branch von `master` anlegen (zum Beispiel `claude/<thema>` oder `feature/<thema>`).
2. Committen und pushen.
3. Pull Request nach `master` anlegen; die CI läuft automatisch.
4. Wenn die CI grün ist: mergen (Merge-Commit).
5. **Branch danach löschen.** Der Merge-Commit bleibt im Hauptbranch erhalten, und GitHub bietet kurz „Restore“ an.

Ablauf direkt auf `master`: `git pull`, ändern, committen, `git push`. Danach in GitHub unter *Actions*
prüfen, dass die CI grün ist (aztodo hat keine CI).

## Was „ahead“ und „behind“ bedeutet

| Anzeige | Bedeutung | Branch löschen? |
|---|---|---|
| 0 ahead, N behind | Nichts Eigenes, alles steckt schon in `master` (typisch nach einem Merge) | Ja |
| N ahead, 0 behind | Eigene, noch nicht gemergte Arbeit | Nein, erst mergen oder bewusst verwerfen |
| N ahead, M behind | Eigene Arbeit, und `master` ist weitergelaufen | Nein |

**Vorsicht:** Ein Branch, der Grundlage eines offenen Pull Requests ist, darf nicht gelöscht
werden. Der PR wird sonst automatisch geschlossen, ohne gemergt zu sein (so geschehen bei
azphysics #2).

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
