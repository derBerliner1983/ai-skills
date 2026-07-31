# claude-skills

Zentrales Repo für meine Claude Code Skills. Wird auf jedem Rechner nach
`~/claude-skills` geklont und `skills/` per Symlink nach `~/.claude/skills`
verlinkt (siehe Installer-Scripts).

## Struktur

```
claude-skills/
├── README.md
└── skills/
    └── <skill-name>/
        ├── SKILL.md        # Pflicht - Einstiegspunkt des Skills
        └── ...             # optionale weitere Dateien (Scripts, Templates, Docs)
```

## Neuen Skill anlegen

1. Ordner unter `skills/<name>/` anlegen
2. `SKILL.md` mit Frontmatter erstellen:
   ```yaml
   ---
   name: <name>
   description: Präzise Trigger-Bedingung - wann soll dieser Skill greifen?
   ---
   ```
3. Committen und pushen (oder über das Installer-Script, Menüpunkt 3)

## Sync auf neuem Rechner

Installer-Script ausführen (`install-claude-skills.ps1` unter Windows,
`install-claude-skills.sh` unter macOS/Linux), Menüpunkt 1 (Setup).

## Update ziehen

Installer-Script, Menüpunkt 2 - oder manuell:
```bash
cd ~/claude-skills && git pull
```
