# Orquestra Skills

Skills de [Claude Code](https://claude.com/claude-code) hechas para trabajar **con [Orquestra](https://orquestra.me)** y su servidor MCP. Cada una usa las tools del MCP para dejar todo el registro dentro de Orquestra, sin archivos extra en tus repos.

Para skills de uso general (que no dependen de Orquestra) mira [claude-skills](https://github.com/ajuarez93/claude-skills).

## Skills

| Skill | Qué hace |
|---|---|
| [orquestra-spec](skills/orquestra-spec/) | Spec con criterios de aceptación **antes** de construir una tarea grande: veredicto previo de la idea (`go`, `needs-clarification` o `kill`), la spec como doc en Orquestra, revisión en frío por un subagente que no ve tu conversación, tu aprobación, y la tarea creada con checklist y una verificación por criterio. Incluye el modo `drift`, que contrasta un doc (CLAUDE.md, README, ficha de módulo) con el código real. |

## Requisitos

El **MCP de Orquestra** conectado al proyecto:

```bash
npx orquestra-mcp setup
```

Sin el MCP, las skills se detienen y te lo dicen; no inventan un sustituto.

## Instalación

```bash
git clone https://github.com/ajuarez93/orquestra-skills.git

# Global (todos tus proyectos)
cp -r orquestra-skills/skills/orquestra-spec ~/.claude/skills/

# Solo un proyecto
cp -r orquestra-skills/skills/orquestra-spec <proyecto>/.claude/skills/
```

También se puede instalar desde Orquestra con el MCP (`preview_skill` / `install_skill`, con `sourceRepo: ajuarez93/orquestra-skills` y `sourcePath: skills/orquestra-spec`).

## Uso

```
/orquestra-spec <idea o folio>        # spec antes de código
/orquestra-spec drift <ruta o módulo> # contrastar un doc con el código
```

Se usa para tareas de más de un día, multiplataforma o que tocan API, reglas de acceso o esquema de datos. En bugs pequeños y ajustes visuales no aplica.

## Contribuir

Issues y PRs son bienvenidos.

## Licencia

[MIT](LICENSE).
