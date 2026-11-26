# AGC Padel Academy

Trabajas en este repositorio. La persona que te habla no es técnica. Respóndele en español llano. Los identificadores de código, ramas y archivos se quedan como están en el repo.

Las issues están en GitHub (`AGCPadelAcademy/web_application`). Léelas con `gh`. Los specs, el plan, las tareas y el código están en este clon. No des por hecho un comportamiento que no hayas leído en la issue y contrastado con el código.

Si en una sesión falta `git`, `gh`, `node` 22, `npm` o `pwsh`, instálalo tú con el gestor del sistema operativo y comprueba el comando en un terminal nuevo. La persona solo tiene que aceptar el permiso de instalación y, si hace falta, iniciar sesión en el navegador. Node tiene que ser la versión 22 (`.nvmrc`).

## Antes de un cambio que no sea trivial

Lee, en este orden, lo que afecte al cambio:

- `.specify/memory/constitution.md`
- `AGENTS.md`
- `.cursor/rules/brownfield-project.mdc`
- `.cursor/rules/sdd-workflow.mdc`
- `.cursor/rules/specify-rules.mdc`
- `.cursor/rules/verification.mdc`
- `docs/sdd-brownfield/project-context.md`
- la parte relevante de `specs/project-context/` y `specs/baseline-system/`
- el código en `src/` y `supabase/`

Si la documentación y el código discrepan, anótalo. Para lo que ya está en producción, manda el código. Para un encargo sin spec todavía, manda la issue.

## Producto

Aplicación de una academia de pádel en Suiza. Marca AGC en la interfaz y el dominio. Entidad legal CAG Padel Academy GmbH en facturas y textos legales. No unifiques esos nombres.

Stack fijo: React 18, Vite 7, Tailwind 3, Radix, React Router 6, JavaScript (JSX). Backend Supabase (Postgres, Auth, Storage, Edge Functions en Deno). Sin servidor Node propio. Sin TypeScript salvo un spec aprobado que lo autorice. Sin Stripe. PDF y QR solo en Edge Functions. Producción en Vercel desde `main`.

Pagos por transferencia y Bexio. Admin según `profiles.role`. RLS de mínimo privilegio. Secretos solo en variables de entorno. En el cliente, solo `VITE_SUPABASE_URL` y `VITE_SUPABASE_ANON_KEY`.

Este ordenador no tiene acceso a Supabase. Las migraciones y las Edge Functions se quedan escritas en el repo y las aplica quien administra Supabase, también cuando la persona te pida aplicarlas. Los tests de integración apuntan a un proyecto Supabase de test, nunca a producción, y aquí se saltan solos.

## Cómo se construye una feature

La feature actual es la issue que la persona nombre en esta conversación. No uses otra carpeta de `specs/features/` como contexto. No elijas la feature por `.specify/feature.json` ni por el bloque SPECKIT de más abajo: esos punteros pueden estar desfasados. El mapa de carpetas está en el paquete de configuración, archivo `04-mapa.md`, y hay que contrastarlo con el árbol real.

Skills, en `.claude/skills/`:

- `speckit-specify` — escribe o actualiza `spec.md`
- `speckit-clarify` — como máximo cinco preguntas, antes del plan
- `speckit-plan` — escribe `plan.md`. El script es `.specify/scripts/powershell/setup-plan.ps1 -Json`, con `pwsh`
- `speckit-tasks` — escribe `tasks.md`
- `speckit-analyze` — cruza spec, plan y tasks sin implementar
- `speckit-checklist` — calidad de los requisitos, no una prueba de la app
- `speckit-implement` — ejecuta `tasks.md` en una rama
- `speckit-converge` — añade a `tasks.md` lo que el código aún no cumple
- `speckit-constitution` — solo si la persona pide cambiar la constitución
- `speckit-taskstoissues` — solo si la persona pide issues a partir de las tareas
- `reverse-feature-spec` — documenta algo que ya está en producción. Solo `spec.md`

Para en cada fase. Al terminar el spec, explícalo en español y espera. Igual con el plan y con las tareas. Implementa cuando la persona diga que está bien o que sigas.

Si pide documentar algo que ya está en la web, usa `reverse-feature-spec`. No crees `plan.md` ni `tasks.md` en ese caso.

## Git

No implementes en `main`. El remoto `origin` es la copia de GitHub de esta persona, no `AGCPadelAcademy/web_application`. Crea `sdd/<carpeta-de-la-feature>` desde el `main` de `origin`. Push y pull requests van a `origin`, con `gh pr create --repo <usuario>/web_application`. Las issues se leen en `AGCPadelAcademy/web_application`. No escribas en esas issues un comentario que sea exactamente `/sdd plan`, `/sdd tasks`, `/sdd implement` o `/sdd converge`. Si ya hay una rama remota de esa feature, díselo antes de abrir otra.

Un commit por historia de usuario terminada. Push solo a la rama de la feature. Con lint y tests en verde, pregunta si abres el pull request. En este punto manda este archivo sobre `AGENTS.md`: el pull request se abre antes de verificar el `quickstart`, porque la verificación se hace en el preview de Vercel y el preview solo existe con el pull request. Primero das lint y tests; con el pull request abierto, el enlace del preview y los escenarios a probar. No hagas merge a `main`. Cuando Vercel ya apunta a esta copia, ese merge publica agcpadelacademy.com.

## Verificación

Toda implementación termina con `npm run lint` y `npm test` en el ordenador. Si cambian Edge Functions, corre también los tests Deno de `supabase/functions/`. Si cambia una migración, corre las aserciones de `tests/sql/` de esa migración cuando existan.

No pidas un `.env` ni las claves de Supabase. La persona no levanta la web en local. Cuando el pull request exista, pásale el enlace del preview de Vercel y los escenarios del `quickstart.md` para que los pruebe ahí. `https://agcpadelacademy.com` cambia solo al fusionar en `main`. No hagas un recorrido automático del navegador salvo que lo pida.

El secreto de los tests de integración se llama `SUPABASE_TEST_SERVICE_ROLE_KEY`.

## Cambios de base de datos

Cada cambio de esquema va en una migración numerada nueva, hacia delante. No rellenes los huecos `0006`, `0007` ni `0025`: `0025` está reservado al plan de F1.26. El dinero se guarda como `numeric`. No arregles deuda de esquema dentro de una feature que no es la dueña de esa deuda.

No apliques migraciones ni despliegues Edge Functions. Este ordenador no tiene acceso a Supabase. El archivo queda en el repo y se lo dices a la persona: el preview no incluye ese cambio de base de datos hasta que quien administra Supabase lo aplique.

## Qué dejas como está

- `.cursor/`
- `.github/workflows/ci.yml`, `sdd-command-dispatcher.yml` y `sdd_stage_dispatcher.yml`
- Las skills ya copiadas, salvo un cambio que la persona pida
- La constitución, salvo un cambio que la persona pida

No borres funcionalidad, tablas, columnas ni archivos salvo que la persona lo pida de forma explícita.

## Plan que el hook de Spec Kit mantiene

El hook `speckit-agent-context-update` reescribe el bloque de abajo después de specify y de plan. Ejecuta siempre la variante PowerShell, `.specify/extensions/agent-context/scripts/powershell/update-agent-context.ps1`, con `pwsh`. La variante bash necesita Python y aquí se salta sin avisar. Pásale la ruta del `plan.md` de la feature actual: sin argumento usa `.specify/feature.json`, que puede apuntar a otra. Su config, `.specify/extensions/agent-context/agent-context-config.yml`, está escrita en JSON a propósito. No la conviertas a YAML.

<!-- SPECKIT START -->
For additional context about technologies to be used, project structure,
shell commands, and other important information, read the current plan
at specs/features/011-adult-memberships/plan.md
<!-- SPECKIT END -->
