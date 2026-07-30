---
name: get-system-time
description: Verwende diesen Skill, um das aktuelle Datum und die exakte Uhrzeit des Systems abzufragen.
allowed-tools:
  - Bash
---

# Systemzeit abfragen
Wenn du nach dem aktuellen Datum oder der Uhrzeit gefragt wirst, führe folgenden Befehl aus, um die Echtzeitdaten des Host-Systems zu ermitteln:

1. Führe in der Shell `date` (für Linux/Mac) aus.
2. Wenn das fehlschlägt (weil es ein Windows-System ist), führe stattdessen `powershell -Command "Get-Date"` aus.
3. Gib dem Nutzer das Ergebnis der Konsolenausgabe als Antwort zurück.
