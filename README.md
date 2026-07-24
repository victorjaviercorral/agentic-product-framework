# Agentic Product Framework Development

Framework de definición de producto en fases, ejecutado con agentes: **research → PRD → historias de usuario → tablero → ejecución**. Este repositorio documenta y versiona todo el proceso, pensado para compartirse en abierto.

## Fases

1. **Definir** — research, análisis JTBD, PRD, historias de usuario y su validación/priorización. Se apoya en el plugin [mercadona-user-story-toolkit](https://github.com/josemerca/mercadona-user-story-toolkit) (comandos `/research`, `/analyze-research`, `/stories`, `/validate-stories`, `/split-stories`, `/prioritize`, `/prd-quality-guard`, `/build-story`).
2. **Ejecutar** — una vez las historias están validadas, pasan al tablero (`Project.canvas`) gestionado con [Kanvas](https://github.com/XMihura/Kanvas) (`canvas-tool.py`). El protocolo de trabajo con el tablero está descrito en `CLAUDE.md` / `RULES.md`.

## Estructura

```
.
├── CLAUDE.md          # Instrucciones para el agente: fase Definición + protocolo Ejecución (Kanvas)
├── RULES.md           # Protocolo del tablero (Kanvas, no editar a mano)
├── canvas-tool.py      # CLI de Kanvas para operar el tablero
├── Project.canvas      # Tablero de ejecución (estado versionado)
├── PRD.md              # Nace cuando exista un PRD real
├── research/           # Nace con el primer artefacto de investigación
└── stories/             # Nace con las primeras historias de usuario
```

## Dependencias

| Dependencia | Instalación | Vive en |
|---|---|---|
| mercadona-user-story-toolkit | Plugin global de Claude Code | `~/.claude/plugins/mercadona-user-story-toolkit` (fuera de este repo) |
| Kanvas | CLI clonada aparte, inicializada sobre este proyecto | `~/repos/Kanvas` (fuera de este repo); solo sus archivos copiados (`canvas-tool.py`, `RULES.md`, `Project.canvas`) viven aquí |

## Licencia

MIT. Ver [LICENSE](LICENSE).
