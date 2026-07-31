---
name: get-system-time
description: Verwende diesen Skill, um das aktuelle Datum und die exakte Uhrzeit abzufragen – für einen Ort oder mehrere gleichzeitig.
allowed-tools:
  - Bash
---
# Systemzeit abfragen (für eine oder mehrere Städte/Zeitzonen)

## Grundregel
NIEMALS Wochentag oder Uhrzeit für eine Stadt manuell umrechnen. Für JEDE angefragte
Stadt/Zeitzone eine EIGENE Kommandozeile ausführen und das Ergebnis unverändert
übernehmen (inkl. Wochentag, Datum, Uhrzeit).

## Ablauf

1. Ermittle für jede angefragte Stadt die passende IANA-Zeitzone
   (z.B. Berlin → Europe/Berlin, Bangkok → Asia/Bangkok, Tokio → Asia/Tokyo).

2. Führe für JEDE Stadt einen eigenen Befehl aus (in EINEM Bash-Call, mehrere
   Zeilen, damit alle Werte aus derselben Sekunde stammen):

```bash
   echo "Berlin:  $(TZ='Europe/Berlin' date)"
   echo "Bangkok: $(TZ='Asia/Bangkok' date)"
   echo "Tokio:   $(TZ='Asia/Tokyo' date)"
```

   (Beliebig viele Zeilen für beliebig viele Städte – einfach pro Stadt eine
   weitere `echo "Name: $(TZ='Region/Stadt' date)"`-Zeile ergänzen.)

3. Falls für eine Stadt keine TZ-Zone bekannt ist oder das Ergebnis unplausibel
   wirkt: mit `timedatectl list-timezones | grep -i <stadt>` die korrekte
   Zone suchen. Nur wenn das nichts liefert, als letzten Fallback kurz im Web
   nach "IANA Zeitzone <Stadt>" suchen.

4. Gib dem Nutzer für jede Stadt Datum, Wochentag und Uhrzeit GENAU so aus,
   wie sie in der Kommandozeilen-Ausgabe stehen – nicht neu berechnen oder
   "im Kopf" auf einen anderen Wochentag umlegen, auch wenn es sich falsch
   anfühlt.

## Beispiel-Ausgabe an den Nutzer
- Berlin: Freitag, 31.07.2026, 13:50 Uhr
- Bangkok: Freitag, 31.07.2026, 18:50 Uhr
