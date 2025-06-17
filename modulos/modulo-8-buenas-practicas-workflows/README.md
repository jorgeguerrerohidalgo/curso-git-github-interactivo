# Módulo 8 – Buenas Prácticas y Flujos de Trabajo en Git

## Objetivo
Consolidar el conocimiento de Git aprendiendo sobre las mejores prácticas para mantener un historial limpio y comprensible, y entender diferentes modelos de flujo de trabajo (workflows) que los equipos utilizan para colaborar eficientemente.

## 1. Buenas Prácticas para un Historial de Commits Limpio

Un historial de commits bien cuidado es invaluable. Facilita la comprensión del desarrollo del proyecto, la depuración de errores, la revisión de código y la colaboración.

### a. Commits Atómicos y Cohesivos
*   **Un commit, una idea:** Cada commit debe representar un cambio lógico y completo. Evita mezclar arreglos de bugs no relacionados con el desarrollo de una nueva funcionalidad en el mismo commit.
*   **Pequeños y frecuentes:** Hacer commits pequeños y frecuentes es generalmente mejor que hacer un solo commit gigante con muchos cambios. Esto facilita la revisión y la posibilidad de revertir cambios si algo sale mal.
    *   Usa `git add -p` (patch mode) para preparar solo ciertas partes de un archivo, permitiéndote dividir cambios grandes en commits más pequeños y lógicos.
*   **Funcionalidad completa (pero pequeña):** Aunque los commits deben ser pequeños, también deben dejar el proyecto en un estado funcional. No hagas commit de código a medias que rompa la compilación o las pruebas principales (a menos que estés en una rama experimental y lo indiques claramente).

### b. Mensajes de Commit Significativos
El mensaje de commit es tu comunicación con tu yo futuro y con tus compañeros de equipo.
*   **Formato común:**
    *   **Línea de Asunto Corta (Imperativo):** Menos de 50-72 caracteres. Escrita en imperativo (ej. "Añade función de login" en lugar de "Añadida función de login" o "Añadiendo función de login"). Piensa en ella como si completara la frase "Este commit va a...".
    *   **Cuerpo Detallado (Opcional):** Después de una línea en blanco, puedes añadir una descripción más larga explicando el *qué* y el *por qué* del cambio, no el *cómo* (el código muestra el cómo).
        ```
        feat: Implementa autenticación de dos factores (2FA)

        Se añade la posibilidad para los usuarios de configurar 2FA utilizando
        aplicaciones TOTP como Google Authenticator.
        Esto mejora la seguridad de las cuentas de usuario. El flujo incluye
        la generación del secreto, la muestra del QR y la validación del código.
        Resuelve: #123
        ```
*   **Estándares de Mensajes (Conventional Commits):** Es una convención ligera sobre los mensajes de commit. Define un prefijo para el tipo de cambio:
    *   `feat:` (nueva funcionalidad)
    *   `fix:` (corrección de un error)
    *   `docs:` (cambios en la documentación)
    *   `style:` (formato, comas faltantes, etc.; sin cambio en la lógica del código)
    *   `refactor:` (reescritura de código sin cambiar su comportamiento externo)
    *   `test:` (añadir o corregir pruebas)
    *   `chore:` (actualizar tareas de build, configuración, etc.; nada que ver con producción)
    *   `perf:` (mejora de rendimiento)
    *   Puedes añadir un ámbito opcional: `feat(api): ...`
    *   Puedes indicar "breaking changes" (cambios que rompen la compatibilidad) añadiendo `!` después del tipo/ámbito o un pie de página `BREAKING CHANGE: ...`.
    *   Ejemplo: `fix(parser): corrige error de parsing con comillas escapadas`
    *   Usar Conventional Commits facilita la generación automática de changelogs y la comprensión del impacto de los cambios.

### c. Uso Consciente de Ramas
*   **Ramas descriptivas:** Nombra tus ramas de forma clara (ej. `feature/user-authentication`, `bugfix/login-error`, `issue/42-fix-payment-gateway`).
*   **Ramas de corta duración:** Intenta que las ramas de funcionalidad no vivan demasiado tiempo separadas de la rama principal. Intégralas frecuentemente (después de que estén completas y probadas) para evitar conflictos de fusión masivos.
*   **Elimina ramas fusionadas:** Una vez que una rama ha sido fusionada (y ya no se necesita), elimínala del repositorio local (`git branch -d <rama>`) y del remoto (`git push origin --delete <rama>`) para mantener limpio el listado de ramas.

### d. Reescribir el Historial (Localmente y con Cuidado)
*   Usa `git rebase -i` para limpiar tus commits *locales* *antes* de subirlos a un repositorio compartido o antes de crear un Pull Request. Puedes:
    *   Reordenar commits.
    *   Unir (squash/fixup) commits pequeños o WIP ("Work In Progress") en commits más significativos.
    *   Mejorar mensajes de commit.
*   **NUNCA reescribas el historial que ya ha sido compartido y sobre el cual otros pueden estar trabajando.** Esto cambia los hashes de los commits y puede causar graves problemas a tus colaboradores. Si necesitas corregir algo ya subido, considera `git revert` (que crea un nuevo commit deshaciendo los cambios de otro) en lugar de `rebase`.

## 2. Flujos de Trabajo (Workflows) en Git

Un flujo de trabajo en Git es una receta o recomendación sobre cómo usar Git para realizar el trabajo de manera consistente y productiva. El flujo adecuado depende del tamaño del equipo, el tipo de proyecto y la cultura del equipo.

### a. Gitflow
Es uno de los flujos de trabajo más conocidos y estructurados, propuesto por Vincent Driessen. Es ideal para proyectos con ciclos de lanzamiento programados.
*   **Ramas Principales:**
    *   `main` (o `master`): Siempre refleja el estado listo para producción. Contiene el historial oficial de lanzamientos (versionado con tags).
    *   `develop`: Es la rama principal de desarrollo. Contiene los últimos cambios entregados para el próximo lanzamiento. Es la rama de la que salen las ramas de funcionalidad.
*   **Ramas de Soporte:**
    *   **`feature/*` (o `feature/<nombre-feature>`):** Se crean a partir de `develop`. Se usan para desarrollar nuevas funcionalidades. Se fusionan de nuevo en `develop` cuando están completas.
        *   Ej: `feature/user-profile`
    *   **`release/*` (o `release/<version>`):** Se crean a partir de `develop` cuando `develop` está lista para un lanzamiento. En esta rama solo se hacen pequeñas correcciones de bugs y se prepara la metainformación para el lanzamiento (número de versión, etc.). Una vez lista, la rama `release` se fusiona en `main` (y se taguea) y también en `develop` (para incorporar las últimas correcciones).
        *   Ej: `release/v1.2.0`
    *   **`hotfix/*` (o `hotfix/<descripcion>`):** Se crean a partir de `main` para corregir urgentemente un bug en producción. Una vez lista, se fusiona de nuevo en `main` (y se taguea con una nueva versión patch) y también en `develop` (o la `release` actual si existe) para asegurar que el bug también se corrige en el desarrollo activo.
        *   Ej: `hotfix/critical-security-vuln`
*   **Ventajas:** Muy estructurado, claro para lanzamientos versionados.
*   **Desventajas:** Puede ser demasiado complejo para proyectos pequeños o aquellos con integración/entrega continua donde los lanzamientos son más frecuentes y menos formales.

### b. GitHub Flow
Un flujo de trabajo más simple, popularizado por GitHub. Adecuado para equipos que practican integración continua y despliegue continuo (CI/CD).
*   **`main` es la única rama principal:** Todo en `main` es desplegable (o ya desplegado) a producción.
*   **Para trabajar en algo nuevo (funcionalidad, bug):**
    1.  Crea una **rama descriptiva** a partir de `main` (ej. `fix-user-login-bug`, `add-profile-page`).
    2.  Haz commits en esa rama localmente y súbelos regularmente a la misma rama en el servidor.
    3.  Cuando estés listo para retroalimentación o fusión, abre un **Pull Request (PR)** o Merge Request (MR).
    4.  Otros miembros del equipo revisan el código, discuten los cambios. Se pueden añadir más commits a la rama basados en la retroalimentación.
    5.  Una vez que el PR es aprobado (y las pruebas automatizadas pasan), se fusiona en `main`.
    6.  Idealmente, los cambios en `main` se despliegan a producción inmediatamente.
*   **Ventajas:** Simple, rápido, promueve la revisión de código y la CI/CD.
*   **Desventajas:** Requiere una buena cultura de pruebas y despliegue automatizado. Los hotfixes se manejan igual (rama nueva, PR, merge).

### c. GitLab Flow
Similar a GitHub Flow, pero con más flexibilidad y opciones para diferentes entornos y tipos de lanzamientos.
*   Combina la simplicidad de GitHub Flow con ramas de entorno (ej. `production`, `staging`, `develop`) si es necesario.
*   Para CI/CD, `main` se despliega a `staging`, y luego se puede promover a `production` (a menudo fusionando `main` en una rama `production`).
*   También soporta ramas de `release` para lanzamientos versionados si es necesario, similar a Gitflow pero potencialmente más simple.

### d. Trunk-Based Development (TBD)
Un flujo de trabajo donde los desarrolladores colaboran en el código en una única rama llamada "trunk" (equivalente a `main` o `develop` en otros flujos).
*   Los cambios se integran en el trunk al menos una vez al día.
*   Las "feature flags" (interruptores de funcionalidad) se usan a menudo para ocultar funcionalidades incompletas en el trunk, permitiendo que el código se integre sin afectar a los usuarios.
*   Las ramas, si se usan, son de muy corta duración (pocas horas o un día) y se fusionan rápidamente al trunk.
*   **Ventajas:** Fomenta una verdadera integración continua, reduce la complejidad de las fusiones.
*   **Desventajas:** Requiere una disciplina de equipo muy alta, pruebas automatizadas exhaustivas y un buen uso de feature flags.

## 3. Consejos Adicionales
*   **Comunícate con tu equipo:** Elige y acuerda un flujo de trabajo y unas convenciones (mensajes de commit, nombrado de ramas) que funcionen para todos.
*   **Usa un archivo `.gitignore`:** Especifica archivos y directorios que Git debe ignorar (ej. archivos de compilación, logs, dependencias de `node_modules`, archivos de IDE).
*   **Revisa antes de commitear:** Usa `git diff` y `git status` para asegurarte de que estás commiteando solo los cambios que quieres y que entiendes lo que has modificado.
*   **Integra a menudo:** Si trabajas en ramas de funcionalidad, intenta traer los últimos cambios de la rama principal (ej. `main` o `develop`) a tu rama frecuentemente (usando `merge` o `rebase`) para evitar conflictos grandes y dolorosos más tarde.
*   **Prueba antes de subir (push):** Asegúrate de que tus cambios no rompen nada importante antes de compartirlos.

Ser un "experto" en Git no solo implica conocer los comandos, sino también entender cómo usarlos de manera efectiva para colaborar y mantener un proyecto saludable a lo largo del tiempo. La elección del flujo de trabajo y la adherencia a buenas prácticas son fundamentales para ello.
