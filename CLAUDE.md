# Agentic Product Framework — Agent Instructions

Este proyecto tiene dos fases con protocolos distintos. Identifica en cuál estás antes de actuar.

## Fase 1: Definición

Objetivo: convertir una idea o problema en historias de usuario validadas y priorizadas, antes de que exista una sola tarjeta en el tablero.

Se apoya en el plugin **mercadona-user-story-toolkit** (instalado globalmente en `~/.claude/plugins/`, no vive en este repo). Comandos disponibles, en el orden típico de uso:

1. `/research` — diseña el research (entrevistas, preguntas) sobre el problema.
2. `/analyze-research` — extrae JTBDs del research recogido.
3. `/build-story` o `/stories` — convierte JTBDs/PRD en historias de usuario.
4. `/validate-stories` — aplica el scoring de calidad (6D) a las historias.
5. `/split-stories` — descompone historias sobredimensionadas.
6. `/prioritize` — ordena el trabajo entre lotes.
7. `/prd-quality-guard` — valida el PRD contra el estándar de calidad antes de avanzar.

El toolkit es "copiloto, no autopiloto": si falta un dato, lo marca como `[⚠️ Pending: definir con PM/Data]` en vez de inventarlo. No fuerces esa marca a mano; complétala con el dato real o dilo explícitamente al usuario.

Salidas de esta fase: `PRD.md` en la raíz, `research/` y `stories/` (ambas se crean cuando exista el primer artefacto real, no antes).

**No pases a Fase 2** hasta que las historias de la primera tanda estén validadas y priorizadas.

## Fase 2: Ejecución — protocolo Kanvas

A partir de aquí, el trabajo se rastrea únicamente en `Project.canvas`, con las reglas de esta sección. Las gestiona Kanvas (`canvas-tool.py` / `RULES.md`) y no se editan a mano.

---

# Canvas Workflow — Agent Instructions

## CRITICAL RULE

**NEVER edit `.canvas` files directly.** All canvas modifications MUST go through the CLI tool:

```bash
python canvas-tool.py "<file>.canvas" <command> [args]
```

Direct JSON editing of `.canvas` files is **forbidden**. The CLI tool enforces workflow rules (valid transitions, cycle detection, blocked states) so you don't have to remember them.

## Session Protocol

### 1. Start of session — read the board

```bash
python canvas-tool.py "Project.canvas" status
```

Review the board state. Run `normalize` if needed. Report ready tasks, blocked tasks, and any anomalies to the user.

### 2. Pick a task

```bash
python canvas-tool.py "Project.canvas" ready            # see what's available
python canvas-tool.py "Project.canvas" show <TASK-ID>    # read task details
python canvas-tool.py "Project.canvas" start <TASK-ID>   # begin work (red → orange)
```

If multiple tasks are ready, ask the user which to prioritize.

### 3. Work on the task

Execute the task. If you discover subtasks, propose them:

```bash
python canvas-tool.py "Project.canvas" propose Development "Subtask title" "Description" --depends-on DV-01
```

Update notes on your in-progress task:

```bash
python canvas-tool.py "Project.canvas" edit <TASK-ID> "Updated description with findings."
```

### 4. Finish the task

```bash
python canvas-tool.py "Project.canvas" finish <TASK-ID>   # orange → cyan
```

Tell the user what was done. Do NOT attempt to set the card green — only the human does that.

### 5. Repeat

After the human marks your task green, check for newly unblocked tasks:

```bash
python canvas-tool.py "Project.canvas" normalize
python canvas-tool.py "Project.canvas" ready
```

## What you CAN do

- **Read** the board: `status`, `show`, `list`, `blocked`, `blocking`, `ready`, `dump`
- **Normalize** the board: `normalize`
- **Propose** tasks: `propose` or `batch` (creates purple cards)
- **Propose** groups: `propose-group` or `batch`
- **Start** a task: `start <ID>` (red → orange)
- **Finish** a task: `finish <ID>` (orange → cyan)
- **Pause** a task: `pause <ID>` (orange → red)
- **Edit** task text: `edit <ID> "<text>"` (only orange tasks)
- **Add dependencies**: `add-dep <FROM> <TO>`

## What you CANNOT do

- Edit `.canvas` files directly
- Mark any card green (done) — human only
- Work on purple cards (proposals awaiting approval)
- Work on gray cards (blocked)
- Work on cyan cards (awaiting human review)
- Remove cards or edges
- Change green cards

## Color meanings

| Color | Meaning | Value |
|-------|---------|-------|
| Purple | Proposed by agent | `"6"` |
| Red | To Do (ready) | `"1"` |
| Orange | Doing | `"2"` |
| Cyan | Ready to review | `"5"` |
| Green | Done | `"4"` |
| Gray | Blocked | `"0"` |
