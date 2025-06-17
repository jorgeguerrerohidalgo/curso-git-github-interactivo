# Módulo 4 – Colaboración con Repositorios Remotos

## Objetivo
Aprender a colaborar con otros desarrolladores utilizando repositorios remotos, sincronizando cambios y entendiendo flujos de trabajo comunes.

## Conceptos Clave

### Repositorios Remotos
Hasta ahora, hemos trabajado con un repositorio local (la carpeta `.git` en tu proyecto). Un repositorio remoto es una versión de tu proyecto que está hospedada en internet o en una red (por ejemplo, en GitHub, GitLab, Bitbucket, o un servidor propio).
*   Permiten que múltiples personas colaboren en el mismo proyecto.
*   Sirven como una copia de seguridad de tu código.
*   Facilitan el acceso a tu código desde diferentes máquinas.

Por convención, el repositorio remoto principal suele llamarse `origin`.

### Comandos Esenciales para la Colaboración

#### `git clone <URL-del-repositorio>`
Crea una copia local de un repositorio remoto existente.
*   Descarga todo el historial del proyecto y los archivos de la rama principal (generalmente `main` o `master`).
*   Automáticamente configura el repositorio remoto original como `origin` en tu copia local, para que puedas interactuar con él fácilmente.

> *ENGLISH: `git clone` creates a local copy of a remote repository.*

#### `git remote -v`
Lista todos los repositorios remotos que tienes configurados para tu proyecto local, junto con sus URLs.
*   `git remote add <nombre-remoto> <URL-del-repositorio>`: Añade una conexión a un nuevo repositorio remoto. Por ejemplo, `git remote add origin https://github.com/usuario/proyecto.git`.
*   `git remote rm <nombre-remoto>`: Elimina la conexión a un repositorio remoto.

> *ENGLISH: `git remote -v` lists remote repositories. `git remote add <name> <url>` adds a new remote.*

#### `git fetch <nombre-remoto>`
Descarga los cambios (nuevos commits, nuevas ramas, etc.) desde el repositorio remoto especificado a tu repositorio local, PERO **no los fusiona** automáticamente con tu trabajo local.
*   Actualiza tus "ramas de seguimiento remoto" (remote-tracking branches, ej: `origin/main`). Estas son como marcadores de dónde estaban las ramas en el remoto la última vez que te comunicaste con él.
*   Es útil para ver qué han hecho otros sin afectar tu trabajo actual. Después de un `fetch`, puedes usar `git log origin/main` para ver los commits que están en el remoto pero no localmente, o `git diff main origin/main` para ver las diferencias.

> *ENGLISH: `git fetch <remote>` downloads objects and refs from another repository but does not merge.*

#### `git pull <nombre-remoto> <nombre-rama-remota>`
Es una combinación de dos comandos: `git fetch` seguido de `git merge`.
*   Descarga los cambios del repositorio remoto (como `fetch`) y luego intenta fusionar automáticamente la rama remota especificada (ej: `origin/main`) en tu rama local actual (ej: `main`).
*   Si tienes cambios locales que no has subido y que entran en conflicto con los cambios remotos, la fusión podría detenerse y tendrías que resolver conflictos.
*   Forma común: `git pull origin main` (si estás en tu rama `main` local y quieres traer los cambios de `origin/main`). Si tu rama local está configurada para seguir a una rama remota, a menudo puedes simplificarlo a `git pull`.

> *ENGLISH: `git pull <remote> <branch>` fetches from and integrates with another repository or a local branch.*

#### `git push <nombre-remoto> <nombre-rama-local>:<nombre-rama-remota>`
Envía tus commits locales a un repositorio remoto.
*   `<nombre-rama-local>` es el nombre de la rama en tu repositorio local cuyos commits quieres enviar.
*   `<nombre-rama-remota>` es el nombre que quieres que esa rama tenga en el repositorio remoto. A menudo son el mismo nombre.
*   Ejemplo: `git push origin main:main` (envía tu rama `main` local a la rama `main` del remoto `origin`).
*   Si la rama local y remota tienen el mismo nombre, puedes abreviar: `git push origin main`.
*   La primera vez que subes una nueva rama local, puede que necesites usar `git push -u origin <nombre-rama>` (o `git push --set-upstream origin <nombre-rama>`). Esto establece una relación de seguimiento entre tu rama local y la nueva rama remota. Después, solo necesitarás `git push`.

**Importante:** Git no te permitirá hacer `push` si la rama remota tiene commits que tú no tienes localmente (es decir, si alguien más ha subido cambios mientras tanto). Primero deberás hacer `git pull` (o `git fetch` y `git merge`) para integrar esos cambios remotos, resolver cualquier conflicto, y luego intentar el `push` de nuevo.

> *ENGLISH: `git push <remote> <local-branch>:<remote-branch>` updates remote refs along with associated objects.*

### Flujo de Trabajo de Colaboración Común (Ejemplo con GitHub/GitLab)

1.  **Fork (Bifurcar) el Repositorio (en GitHub/GitLab):**
    *   En lugar de clonar directamente el repositorio original (si no tienes permisos de escritura directa), primero creas una "bifurcación" (fork) en tu propia cuenta de GitHub/GitLab. Esto crea una copia del repositorio bajo tu control.

2.  **Clonar tu Fork:**
    *   Clonas el repositorio desde *tu* cuenta (tu fork) a tu máquina local: `git clone https://github.com/TU_USUARIO/proyecto-fork.git`

3.  **Configurar el Repositorio Original como "Upstream":**
    *   Es una buena práctica añadir el repositorio original como otro remoto, comúnmente llamado `upstream`, para poder traer actualizaciones de él.
    *   `git remote add upstream https://github.com/USUARIO_ORIGINAL/proyecto-original.git`

4.  **Crear una Rama para tu Trabajo:**
    *   `git checkout -b nueva-funcionalidad`

5.  **Trabajar Localmente:**
    *   Haz tus cambios, `git add`, `git commit`.

6.  **Mantener tu Rama Actualizada (opcional pero recomendado):**
    *   Antes de subir tus cambios, es bueno traer los últimos cambios del repositorio original para evitar conflictos mayores.
    *   `git fetch upstream`
    *   `git merge upstream/main` (o la rama principal que corresponda) en tu rama `nueva-funcionalidad`. Resuelve conflictos si los hay.

7.  **Subir tu Rama a tu Fork:**
    *   `git push origin nueva-funcionalidad`

8.  **Crear un Pull Request (PR) o Merge Request (MR):**
    *   Desde tu fork en GitHub/GitLab, verás una opción para crear un "Pull Request" (o "Merge Request").
    *   Esto propone que tus cambios en la rama `nueva-funcionalidad` de tu fork se fusionen en la rama `main` (o la que corresponda) del repositorio original.
    *   El propietario del repositorio original puede revisar tus cambios, hacer comentarios, solicitar modificaciones, y finalmente decidir si fusiona (merge) tu PR.

## Pasos y Ejercicio Práctico (Simulado)

Como configurar un servidor Git real está fuera del alcance de este módulo básico, simularemos la interacción.

1.  **Simular la clonación de un "repositorio central":**
    *   Imagina que `mi-proyecto-git` (el que has estado usando) es tu repositorio local.
    *   Crea un nuevo directorio *fuera* de `mi-proyecto-git`, llámalo `repositorio-central-simulado`.
    *   Dentro de `repositorio-central-simulado`, inicializa un repositorio bare (un tipo de repositorio que solo guarda el historial de Git, sin directorio de trabajo, usado a menudo en servidores):
        ```bash
        mkdir repositorio-central-simulado
        cd repositorio-central-simulado
        git init --bare
        cd ..
        ```
        (Anota la ruta completa a `repositorio-central-simulado/.git` o simplemente `repositorio-central-simulado` si estás en el mismo directorio padre)

2.  **Conectar tu proyecto local al "remoto simulado":**
    *   Vuelve a tu directorio `mi-proyecto-git`.
    *   Añade el repositorio bare como un remoto:
        ```bash
        # Reemplaza /ruta/a/repositorio-central-simulado con la ruta real
        git remote add origin /ruta/a/repositorio-central-simulado
        ```
        Si `repositorio-central-simulado` está al mismo nivel que `mi-proyecto-git`, puedes usar una ruta relativa: `git remote add origin ../repositorio-central-simulado`
    *   Verifica con `git remote -v`.

3.  **Subir (push) tu rama `main` actual al remoto simulado:**
    *   `git push origin main`
    *   (Si usas `master` en lugar de `main`, usa `git push origin master`)

4.  **Simular que otro desarrollador (o tú en otra carpeta) clona el repositorio:**
    *   Crea otro directorio, llámalo `otro-desarrollador-proyecto`.
    *   Clona desde tu `repositorio-central-simulado`:
        ```bash
        mkdir otro-desarrollador-proyecto
        cd otro-desarrollador-proyecto
        # Reemplaza /ruta/a/repositorio-central-simulado con la ruta real
        git clone /ruta/a/repositorio-central-simulado .
        # El "." al final clona en el directorio actual
        ```
    *   Verifica el log (`git log --oneline`) en `otro-desarrollador-proyecto`. Debería tener los commits que subiste.

5.  **Hacer cambios en `otro-desarrollador-proyecto` y subirlos:**
    *   En `otro-desarrollador-proyecto`, crea un nuevo archivo llamado `notas_colaborador.txt`.
    *   Añade algo de texto, guárdalo.
    *   `git add notas_colaborador.txt`
    *   `git commit -m "Añade notas del colaborador"`
    *   `git push origin main` (o `master`)

6.  **Obtener cambios en tu proyecto original (`mi-proyecto-git`):**
    *   Vuelve a tu directorio `mi-proyecto-git`.
    *   **Opción A: Fetch y Merge manual**
        *   `git fetch origin`
        *   `git log --oneline main origin/main` (para ver la diferencia)
        *   `git merge origin/main`
    *   **Opción B: Pull (más directo)**
        *   `git pull origin main`
    *   Verifica que `notas_colaborador.txt` ahora exista en `mi-proyecto-git` y que el log incluya el "Añade notas del colaborador".

Este ejercicio simula el flujo básico de `push` y `pull` que usarías al colaborar con otros. En un entorno real con GitHub o GitLab, el `fork` y `pull request` añaden capas de revisión y gestión sobre este proceso.

¡Has aprendido los fundamentos para colaborar usando Git y repositorios remotos!
