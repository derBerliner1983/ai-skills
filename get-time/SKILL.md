---
name: get-system-time
description: Verwende diesen Skill, um Datum, Wochentag und/oder Uhrzeit abzufragen – für einen Ort, mehrere Orte gleichzeitig, oder relativ zum Standort des Nutzers.
allowed-tools:
  - Bash
---

# Systemzeit abfragen (flexibel, für eine oder mehrere Städte/Zeitzonen)

## Grundregeln (unbedingt einhalten)

1. **Niemals manuell rechnen.** Kein "Berlin ist UTC+2, also +2h auf die
   Systemzeit", kein Kopfrechnen von Wochentagen, keine Offset-Addition/
   Subtraktion durch Claude selbst. Jede Zeitangabe kommt ausschließlich aus
   einem `date`-Befehl mit passender `TZ`-Variable. Der Offset (inkl.
   Sommerzeit) wird ausschließlich vom System über die IANA-TZ-Datenbank
   ermittelt, nie von Claude selbst.
2. **Niemals eine bereits ausgegebene Zeit als Basis für weitere Berechnung
   nehmen.** Jede Stadt bekommt ihren eigenen, unabhängigen `TZ='Region/Stadt'
   date`-Aufruf – nie "Stadt A ist X Uhr, also ist Stadt B X+3 Uhr".
3. **Ergebnis 1:1 übernehmen**, egal wie es sich anfühlt (auch wenn der
   Wochentag "falsch" erscheint – die Systemausgabe ist die Wahrheit).
4. Websuche ist hier NICHT nötig und KEIN Ersatz für den `date`-Befehl – die
   IANA-TZ-Datenbank ist zuverlässiger als eine Google-Zeitbox. Websuche nur
   als allerletzten Fallback nutzen, wenn Schritt 1 keine Zeitzone findet.

## Schritt 1: Passende Zeitzone(n) ermitteln

Für jede angefragte Stadt die passende IANA-Zeitzone bestimmen, z.B.:
- Berlin → Europe/Berlin
- London → Europe/London
- Bangkok → Asia/Bangkok
- Vancouver → America/Vancouver
- Tokio → Asia/Tokyo
- New York → America/New_York

Falls unklar oder unbekannt: `timedatectl list-timezones | grep -i <stadt>`
ausführen. Nur wenn das nichts Eindeutiges liefert, als letzten Fallback kurz
im Web nach "IANA Zeitzone <Stadt>" suchen.

## Schritt 2: Standort des Nutzers berücksichtigen

Wenn der Nutzer relativ fragt ("hier", "bei mir", "meine Zeit", "wie spät ist
es gerade" ohne Stadt) UND ein Standort bekannt ist (z.B. aus dem Kontext):
- Diesen Standort als eigene Zeile mit abfragen (z.B. bei bekanntem Standort
  Berlin: `TZ='Europe/Berlin' date ...`).
- NICHT die rohe Container-Systemzeit (UTC) ungefragt als "deine Zeit"
  ausgeben, außer der Nutzer-Standort ist unbekannt – dann das explizit dazu
  sagen ("basierend auf Systemzeit, da kein Standort bekannt ist").

Wenn der Nutzer eine andere Stadt explizit nennt (z.B. "London"), wird IMMER
deren eigene TZ-Zone abgefragt – unabhängig vom Nutzerstandort. Bei Vergleichs-
fragen ("wie spät ist es in London im Vergleich zu mir") werden beide Städte
einzeln abgefragt und nebeneinandergestellt – nie eine aus der anderen
berechnet.

## Schritt 3: Format je nach Nutzeranfrage wählen

| Nutzer will...              | Format-String                |
|------------------------------|-------------------------------|
| nur Uhrzeit                  | `+%H:%M` (oder `+%H:%M:%S`)    |
| nur Datum                    | `+%d.%m.%Y`                    |
| nur Wochentag                 | `+%A`                          |
| Datum + Wochentag             | `+%A, %d.%m.%Y`                 |
| alles (Tag+Datum+Zeit)        | `+%A, %d.%m.%Y %H:%M`           |

Bei Unklarheit lieber alles ausgeben als zu wenig.

## Schritt 4: Pro Stadt EIN eigener Aufruf, gleiches Format für alle, in EINEM Bash-Call

Alle Städte in derselben Bash-Ausführung abfragen, damit alle Werte aus
derselben Sekunde stammen:

```bash
echo "Berlin:    $(TZ='Europe/Berlin' date '+%A, %d.%m.%Y %H:%M')"
echo "London:    $(TZ='Europe/London' date '+%A, %d.%m.%Y %H:%M')"
echo "Bangkok:   $(TZ='Asia/Bangkok' date '+%A, %d.%m.%Y %H:%M')"
echo "Vancouver: $(TZ='America/Vancouver' date '+%A, %d.%m.%Y %H:%M')"
```

Beliebig viele Städte = beliebig viele Zeilen nach demselben Muster. Egal ob
2, 3, 4 oder mehr Städte angefragt werden – jede bekommt ihre eigene Zeile.

## Schritt 5: Ausgabe an den Nutzer

Genau das ausgeben, wonach gefragt wurde (nur Zeit / nur Datum / nur Tag /
Kombination) – nicht mehr, nicht weniger. Werte unverändert aus der
Kommandozeilen-Ausgabe übernehmen, nichts nachträglich korrigieren oder
"plausibilisieren".

## Beispiele

- "Wie spät ist es in Bangkok?"
  → nur `%H:%M` für Bangkok, ein Aufruf.

- "Welcher Wochentag ist heute in Berlin und Tokio?"
  → nur `%A` für beide, zwei Aufrufe in einem Bash-Call.

- "Datum und Uhrzeit für Berlin, Bangkok und Vancouver"
  → `%A, %d.%m.%Y %H:%M` für alle drei, drei Aufrufe in einem Bash-Call.

- "Wie spät ist es gerade bei mir?" (kein Ort genannt, Standort Berlin bekannt)
  → `TZ='Europe/Berlin' date` abfragen, nicht die rohe UTC-Systemzeit.

- "Wie spät ist es in London im Vergleich zu mir?" (Standort Berlin bekannt)
  → zwei Zeilen: `TZ='Europe/Berlin'` und `TZ='Europe/London'`, beide einzeln
    abgefragt und nebeneinander ausgegeben.
