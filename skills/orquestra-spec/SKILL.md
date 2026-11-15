---
name: orquestra-spec
description: Spec antes de código con Orquestra (requiere el MCP de Orquestra). Úsala cuando el usuario pida especificar, diseñar o planear una tarea o idea grande (más de un día, multiplataforma, o que toque API, reglas de acceso o esquema de datos), o invoque /orquestra-spec. Redacta la spec como doc en Orquestra, la hace revisar en frío por un subagente sin contexto y, con el visto bueno del usuario, crea la tarea con checklist y verificaciones. Modo `drift`: compara un doc (CLAUDE.md, README, ficha de módulo) contra el código real. No la uses para bugs pequeños, ajustes visuales ni tareas de menos de un día.
version: 0.2.0
---

# orquestra-spec

Una spec con criterios de aceptación **antes** de implementar una tarea grande, con todo el registro dentro de [Orquestra](https://orquestra.me): la spec es un doc, la revisión queda dentro del doc, la tarea nace enlazada a ella. No se crean archivos en el repo (nada de `.specify/` ni `docs/designs/`). Responde en el idioma del usuario.

## Requisitos
- El **MCP de Orquestra** conectado a este proyecto (`npx orquestra-mcp setup`). Comprueba que existen las tools `get_project_context`, `create_doc`, `create_task` y `list_modules`. Si no están, **detente** y dile al usuario que conecte el MCP; no inventes un sustituto.
- Las reglas del usuario (CLAUDE.md, convenciones de su proyecto) mandan sobre las de este skill.

## Cuándo NO usarla
Bugs pequeños, ajustes visuales, preguntas y tareas de menos de un día. Dilo en una línea y sigue el flujo normal.

## Modo spec: `/orquestra-spec <idea o folio>`

1. **Contexto.** `get_project_context` una vez. Si viene un folio, `get_task`. Si viene una idea, `list_ideas` y busca duplicados con `search`.
2. **Veredicto previo** (si es una idea): `go`, `needs-clarification` o `kill`, con una razón. Si no es `go`, **para y haz solo las preguntas que bloquean** (opciones cerradas cuando se pueda). Una idea que mezcla varias cosas se acota aquí a una sola.
3. **Lee el código antes de redactar.** Basa la spec en lo que el código hace hoy; las referencias de una idea o tarea vieja suelen estar desfasadas. No inventes: lo que no verificaste, márcalo como no verificado.
4. **Spec.** `create_doc` enlazado al origen (`relatedTo`) y al módulo, con:
   - **Problema**: qué duele y a quién.
   - **Alcance**: incluye / no incluye.
   - **Criterios de aceptación**: cada uno verificable y con *cómo se comprueba*. Si un criterio depende de provocar un error o un estado raro, incluye un paso previo (spike) que confirme que se puede provocar.
   - **Preguntas abiertas**: lo que se decide en el plan, no en la spec.
   - **Plataformas afectadas**: para scope y paridad.
5. **Revisión en frío (goldfish).** Lanza UN subagente nuevo cuyo prompt contenga **solo el texto de la spec**, sin tu conversación, y pídele, ordenados de más a menos grave y máximo 10: supuestos no escritos, criterios que no se pueden verificar, contradicciones, casos límite y qué falta para implementarla sin preguntar; sin elogios ni resumen. Corrige la spec con lo útil, y deja en el doc una sección "Revisión en frío" con los hallazgos y qué se hizo con cada uno. **Un solo ciclo**, salvo que el usuario pida otro (cuesta tokens). Reserva este paso para tareas multiplataforma o de contrato; en un bug de una sola plataforma bien entendido, basta la spec.
6. **Visto bueno.** Pide al usuario aprobar la spec. Sin aprobación no se crea la tarea ni se escribe código. Al aprobarse, deja el doc marcado como aprobado.
7. **Tarea.** Una tarea = un entregable (varias cosas = varias tareas; lo bloqueado o futuro va aparte). Con `list_modules` elige el módulo; pon `dueDate` con criterio real, tipo, prioridad, tags, stack, `estimatedDays` y scope por distribución si aplica; enlázala al doc con `relatedTo`.
   - `checklist`: solo trabajo.
   - `verifications`: una por criterio de aceptación que **el usuario** prueba, con pasos concretos (qué abrir, qué hacer, qué se espera ver).
8. **Después.** Mueve el estatus de uno en uno y nunca a "Listo" antes de que el usuario verifique. Rama, commits y PR van en los campos de la tarea cuando existan; no inventes valores. Si implementas tú, registra los hallazgos que la spec no previó (suelen venir de leer los llamadores del código que cambias).

## Modo drift: `/orquestra-spec drift <ruta o módulo>`

1. Lee el doc (CLAUDE.md, README o la ficha del módulo con `list_modules`). Si hay doc local **y** ficha en Orquestra, contrástalos también entre sí.
2. Contrasta cada afirmación verificable (rutas, conteos, versiones, estado de plataformas, comandos, archivos listados) con el código real, con comandos acotados. Subagente por repo solo si son muchos archivos.
3. Entrega una tabla: afirmación | realidad | evidencia (archivo:línea o comando). Señala también lo que **no** pudiste verificar.
4. Orquestra es la fuente de verdad: si un doc local quedó viejo, se corrige el doc, no al revés. No edites sin que el usuario lo pida; ofrece las correcciones.
5. Cada desfase que sea trabajo real se registra como tarea en Orquestra de inmediato.

## Reglas
- Pregunta al usuario solo cuando algo bloquea; si no, recomienda y avanza.
- Salida corta; acota los comandos (`head`, `tail`, `grep -c`) y no vuelques archivos grandes.
- Completa en Orquestra todo campo que puedas llenar con datos reales; lo que no sepas queda vacío y lo dices.
- Sigue las reglas de commits y atribución del usuario o del proyecto.
