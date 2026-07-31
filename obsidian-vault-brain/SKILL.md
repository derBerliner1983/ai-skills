---
name: obsidian-vault-brain
description: Sucht auf der Maschine nach einem Nextcloud-Ordner mit einem Obsidian-Vault (Knowledge-Vault) und nutzt es als "zweites Gehirn" für Kontext-Fragen. Trigger bei Sitzungsstart, bei Fragen wie "was ist gerade aktuell", "wo war ich stehen geblieben", oder generell bei Wissensfragen, die aus persönlichem Kontext beantwortet werden könnten.
allowed-tools:
  - Bash
  - Read
  - Grep
  - Glob
---

# Obsidian-Vault als Gehirn nutzen

## Ziel
Prüfen, ob auf der Maschine eine Nextcloud-Installation mit einem Obsidian-
Vault namens "Knowledge-Vault" existiert. Wenn ja: dieses Vault als primäre
Wissensquelle ("Gehirn") behandeln und dem in ihm hinterlegten Protokoll
(INDEX.md, CLAUDE.md) folgen.

## Schritt 1: Nextcloud-Ordner finden

Prüfe die üblichen Speicherorte, je nach Betriebssystem. Nutze EINEN Bash-
Aufruf mit mehreren Kandidaten-Pfaden, nicht mehrere einzelne Aufrufe:

**Windows (typisch):**
```powershell
Test-Path "$env:USERPROFILE\Nextcloud"
Test-Path "$env:USERPROFILE\ownCloud"
Get-ChildItem "$env:USERPROFILE" -Directory -Filter "*Nextcloud*" -ErrorAction SilentlyContinue
Get-ChildItem "C:\Users" -Directory -Filter "*Nextcloud*" -Recurse -Depth 1 -ErrorAction SilentlyContinue
```

**Linux/Mac (typisch):**
```bash
find ~ -maxdepth 3 -iname "*nextcloud*" -type d 2>/dev/null
find ~ -maxdepth 3 -iname "*owncloud*" -type d 2>/dev/null
```

Wenn kein Nextcloud-Ordner gefunden wird: normal weiterarbeiten, kein Vault
verwenden, nichts weiter erwähnen.

## Schritt 2: Nach dem Vault suchen

Wenn ein Nextcloud-Ordner gefunden wurde, darin nach dem Pfad-Muster suchen:
`.../Nextcloud/.../Obsidian-Vault/Knowledge-Vault` (Groß-/Kleinschreibung und
genaue Zwischenordner können variieren — daher mit Wildcard suchen statt
exaktem Pfad).

**Windows:**
```powershell
Get-ChildItem "$env:USERPROFILE\Nextcloud" -Directory -Recurse -Depth 3 -Filter "Knowledge-Vault" -ErrorAction SilentlyContinue
```

**Linux/Mac:**
```bash
find ~/Nextcloud -maxdepth 4 -iname "Knowledge-Vault" -type d 2>/dev/null
```

Wenn nichts gefunden wird: normal weiterarbeiten, kein Vault verwenden.

## Schritt 3: Vault als Gehirn bestätigen (nur beim ERSTEN Fund pro Sitzung)

Wenn ein Vault gefunden wurde UND dies der erste Fund in dieser Sitzung ist:
- Dem Nutzer kurz mitteilen, dass ein Vault gefunden wurde und dass es als
  Gehirn/Kontextquelle für diese Sitzung genutzt wird, z.B.:
  "Ich habe dein Obsidian-Vault (Knowledge-Vault) gefunden und nutze es als
  Gehirn für diese Sitzung."
- Danach NICHT bei jeder weiteren Nachricht wiederholen — nur einmal pro
  Sitzung ansagen.

## Schritt 4: Vault-Kontext laden

Sobald das Vault bestätigt ist, folgendes lesen (in dieser Reihenfolge):

1. **`CLAUDE.md`** im Vault-Wurzelverzeichnis — enthält die Vault-Struktur,
   Regeln, Session-Routinen und das Brain-First-Protokoll. Diese Datei ist
   die Betriebsanleitung für das Vault und hat Vorrang vor generischen
   Annahmen.
2. **`INDEX.md`** — der Katalog aller Bereiche im Vault mit Pfaden und
   Beschreibungen. Diese Datei wird IMMER komplett gelesen (sie ist bewusst
   kurz gehalten), nicht nur teilweise.

## Schritt 5: Brain-First-Protokoll befolgen (aus CLAUDE.md)

Bei jeder Wissensfrage, die mit dem Vault beantwortet werden könnte, gilt
diese Reihenfolge (nicht selbst erfinden, sondern exakt das befolgen, was in
der geladenen `CLAUDE.md` steht — die Regeln können sich dort ändern):

1. INDEX.md konsultieren — steht die Quelle dort, genau diese Datei öffnen.
2. Falls vorhanden: das Wiki (`09 Wiki/`, von der KI verdichtetes Wissen)
   prüfen.
3. Erst danach die Vault-interne Suche nutzen (z.B. `qmd search` / `qmd
   query`, falls im Vault vorhanden) — Kandidaten sichten, ohne Dateien
   direkt zu öffnen.
4. Genau EINE Datei öffnen — die passendste — und nur die relevante Sektion
   lesen.
5. Erst dann antworten. Kein blindes Durchsuchen ganzer Ordner.

## Schritt 6: Session-Routinen (aus CLAUDE.md, falls dort vorhanden)

- **Bei Sitzungsstart:** `01 Inbox/` auf neue Notizen prüfen, kurz zeigen was
  drin liegt, anbieten die Einträge einzusortieren.
- **Bei Fragen wie "Was ist aktuell?" / "Wo war ich stehen geblieben?":** die
  letzten 2-3 Daily Notes in `05 Daily Notes/` und aktive Projekt-Dateien in
  `02 Projekte/` lesen und ein kurzes Briefing geben.
- **Bei Sitzungsende:** anbieten, einen Daily-Note-Eintrag zu erstellen, neue
  Erkenntnisse als Notizen zu speichern, und die Inbox aufzuräumen falls
  nötig.

## Wichtige Einschränkungen

- Niemals Dateien im Vault löschen oder überschreiben, ohne vorher nachzufragen.
- Neue Notizen ohne klaren Platz kommen in `01 Inbox/`.
- Abgeschlossene Projekte nur auf explizite Anweisung nach `06 Archiv/`
  verschieben.
- Wenn Dateien erstellt oder verschoben werden: kurz erklären warum.
- Rohnotizen im Wiki-Bereich (`09 Wiki/`) sind unantastbar — Widersprüche
  werden markiert, nie stillschweigend aufgelöst (siehe `09 Wiki/_SCHEMA.md`
  falls vorhanden).
- Wenn CLAUDE.md und INDEX.md sich widersprechen oder eine der beiden Dateien
  fehlt: das dem Nutzer kurz mitteilen statt zu raten.

## Wenn kein Vault gefunden wird

Kein Hinweis, keine Fehlermeldung — einfach normal ohne Vault-Kontext
weiterarbeiten. Erst bei einer expliziten Nutzerfrage dazu (z.B. "hast du
mein Vault gefunden?") den Suchstatus mitteilen.
