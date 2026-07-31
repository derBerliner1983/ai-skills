---
name: schreibstil-vault
description: Wendet den persönlichen Schreibstil aus 00 Kontext/Schreibstil.md (Obsidian-Vault) an, wenn E-Mails, Texte, Anleitungen oder ähnliche Inhalte formuliert werden sollen. Setzt voraus, dass der obsidian-vault-brain-Skill bereits ein Vault gefunden hat. Trigger bei Anfragen wie "schreib eine E-Mail", "formuliere einen Text", "schreib eine Anleitung", "verfasse..." o.ä.
allowed-tools:
  - Bash
  - Read
  - Grep
  - Glob
---

# Persönlichen Schreibstil aus dem Vault anwenden

## Voraussetzung

Dieser Skill baut auf `obsidian-vault-brain` auf. Er greift NUR, wenn zuvor
bereits ein Nextcloud-Vault (Knowledge-Vault) gefunden wurde. Wenn noch nicht
geprüft wurde, ob ein Vault existiert: zuerst `obsidian-vault-brain` befolgen
(Nextcloud suchen, Vault suchen, CLAUDE.md/INDEX.md laden).

Wenn KEIN Vault gefunden wurde: normal ohne Stil-Referenz weiterschreiben,
nichts dazu erwähnen.

## Wann dieser Skill greift

Bei jeder Anfrage, einen Text zu VERFASSEN oder zu FORMULIEREN — nicht beim
Lesen, Zusammenfassen oder Analysieren fremder Texte. Typische Trigger:
- "Schreib eine E-Mail an..."
- "Formuliere einen Text über..."
- "Verfasse eine Anleitung für..."
- "Schreib eine Nachricht/Antwort an..."
- "Erstelle einen Beitrag/Post über..."

NICHT relevant bei: Code, technischen Fehleranalysen, reinen Fakten-Antworten,
Übersetzungen fremder Texte, Zusammenfassungen.

## Schritt 1: Schreibstil.md suchen und laden

Prüfen, ob im gefundenen Vault die Datei existiert:
