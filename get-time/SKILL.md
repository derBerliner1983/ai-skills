---
name: get-system-time
description: Verwende diesen Skill, um Datum, Wochentag und/oder Uhrzeit abzufragen – für einen Ort oder mehrere gleichzeitig, wahlweise einzeln oder kombiniert.
allowed-tools:
  - Bash
---
# Systemzeit abfragen (flexibel: nur Zeit, nur Datum, nur Tag, oder Kombination)

## Grundregel
NIEMALS Wochentag, Datum oder Uhrzeit manuell umrechnen. Für JEDE angefragte
Stadt/Zeitzone einen EIGENEN `date`-Aufruf mit passendem Format ausführen und
das Ergebnis unverändert übernehmen.

## Schritt 1: Format je nach Anfrage wählen

| Nutzer will...          | Format-String              |
|--------------------------|-----------------------------|
| nur Uhrzeit              | `+%H:%M:%S` (oder `+%H:%M`) |
| nur Datum                | `+%d.%m.%Y`                 |
| nur Wochentag            | `+%A`                       |
| Datum + Wochentag        | `+%A, %d.%m.%Y`              |
| alles (Datum+Tag+Zeit)   | `+%A, %d.%m.%Y %H:%M:%S`     |

(Bei Unklarheit lieber alles ausgeben als zu wenig.)

## Schritt 2: Pro Stadt ein eigener Aufruf, gleiches Format für alle

```bash
echo "Berlin:  $(TZ='Europe/Berlin' date '+%A, %d.%m.%Y %H:%M')"
echo "Bangkok: $(TZ='Asia/Bangkok' date '+%A, %d.%m.%Y %H:%M')"
```

Beliebig viele Städte = beliebig viele Zeilen, alle im selben Bash-Call
(damit alle Werte aus derselben Sekunde stammen).

## Schritt 3: Zeitzone unbekannt?
`timedatectl list-timezones | grep -i <stadt>` nutzen. Nur wenn das nichts
liefert: kurz im Web nach "IANA Zeitzone <Stadt>" suchen.

## Schritt 4: Ausgabe
Genau das ausgeben, wonach gefragt wurde – nicht mehr, nicht weniger. Werte
1:1 aus der Kommandozeilen-Ausgabe übernehmen, nichts im Kopf nachrechnen.

## Beispiele
- "Wie spät ist es in Bangkok?" → nur `%H:%M` für Bangkok
- "Welcher Wochentag ist heute in Berlin und Tokio?" → nur `%A` für beide
- "Datum und Uhrzeit für Berlin, Bangkok, New York" → `%A, %d.%m.%Y %H:%M` für alle drei
