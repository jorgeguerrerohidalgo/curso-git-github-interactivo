# Módulo 6 – Git Avanzado: Rebase, Tags y Cherry-picking

## Objetivo
Explorar herramientas avanzadas de Git para gestionar el historial de commits de forma más limpia y flexible, marcar versiones importantes y aplicar commits específicos entre ramas.

## Conceptos Clave

### 1. Rebase (`git rebase`)
`git rebase` es una forma de reescribir el historial de commits. Su uso más común es para integrar los cambios de una rama en otra, pero aplicando los commits de tu rama actual *encima* de los commits más recientes de la otra rama, creando un historial lineal.

**`git rebase <rama-base>` (Ej: `git rebase main`)**
*   Supón que estás en una rama `feature` que creaste a partir de `main`. Mientras trabajabas en `feature`, `main` ha recibido nuevos commits.
*   Si ejecutas `git checkout feature` y luego `git rebase main`:
    1.  Git "guarda" temporalmente los commits que son únicos de tu rama `feature`.
    2.  Reinicia tu rama `feature` para que apunte al último commit de `main`.
    3.  Aplica uno por uno los commits que guardó de tu rama `feature` *encima* de los commits de `main`.
*   **Resultado:** El historial de `feature` aparecerá como si hubieras empezado a trabajar en ella a partir del último estado de `main`, resultando en un historial lineal y más fácil de seguir que un merge commit (aunque ambos son válidos).
*   **¡Advertencia importante sobre `rebase`!** Nunca uses `git rebase` en commits que ya has subido (push) a un repositorio compartido y que otros podrían estar usando. Reescribir el historial público puede causar serios problemas a tus colaboradores. Es más seguro usarlo en tu historial local antes de compartirlo.

**Rebase Interactivo (`git rebase -i` o `git rebase --interactive HEAD~N`)**
Esta es una herramienta muy poderosa para limpiar y organizar los commits de tu rama *antes* de fusionarlos o subirlos.
*   `git rebase -i HEAD~3` (por ejemplo) te permite reescribir los últimos 3 commits.
*   Se abrirá un editor de texto con una lista de los commits que vas a reescribir, y acciones que puedes tomar para cada uno:
    *   `pick` (p): Usar el commit tal cual.
    *   `reword` (r): Usar el commit, pero editar su mensaje.
    *   `edit` (e): Usar el commit, pero detenerse para modificarlo (ej. añadir/quitar cambios, dividirlo).
    *   `squash` (s): Usar el commit, pero fusionarlo con el commit anterior. Se combinarán los mensajes de commit.
    *   `fixup` (f): Como `squash`, pero descarta el mensaje de este commit y usa solo el del anterior.
    *   `drop` (d): Eliminar el commit.
*   Después de guardar y cerrar el editor, Git aplicará los cambios. Si elegiste `edit` o hay conflictos, el rebase se detendrá para que hagas los ajustes necesarios.

> *ENGLISH: `git rebase` reapplies commits on top of another base tip. Interactive rebase (`-i`) lets you modify individual commits in the process.*

### 2. Tags (`git tag`)
Los tags se usan para marcar puntos específicos en el historial de tu repositorio como importantes, típicamente para indicar lanzamientos de versiones (ej. `v1.0.0`, `v2.0-beta`).
*   Un tag es un puntero a un commit específico, como una etiqueta que no se mueve.

**Tipos de Tags:**
*   **Ligeros (Lightweight):** Simplemente un puntero a un commit. No almacena más información.
    *   `git tag <nombre-tag>` (ej. `git tag v1.0-lw`). Crea un tag ligero en el commit actual (HEAD).
    *   `git tag <nombre-tag> <hash-commit>` (ej. `git tag v1.0-lw abc1234`). Crea un tag ligero en un commit específico.
*   **Anotados (Annotated):** Son objetos completos en la base de datos de Git. Almacenan metadatos como el nombre del "tagueador", email, fecha y un mensaje de tagueo. Se recomiendan para lanzamientos públicos.
    *   `git tag -a <nombre-tag> -m "Mensaje del tag"` (ej. `git tag -a v1.0.0 -m "Versión 1.0.0 estable"`). Crea un tag anotado en el commit actual.
    *   `git tag -a <nombre-tag> <hash-commit> -m "Mensaje del tag"`

**Comandos útiles para tags:**
*   `git tag`: Lista todos los tags.
*   `git show <nombre-tag>`: Muestra la información del tag y el commit al que apunta.
*   `git push origin <nombre-tag>`: Por defecto, `git push` no envía los tags. Debes hacerlo explícitamente.
*   `git push origin --tags`: Envía todos tus tags locales al remoto.
*   `git tag -d <nombre-tag>`: Elimina un tag local.
*   `git push origin --delete <nombre-tag>`: Elimina un tag remoto.

> *ENGLISH: `git tag` creates, lists, deletes or verifies a tag object signed with GPG. Tags mark specific points in history as important.*

### 3. Cherry-picking (`git cherry-pick`)
`git cherry-pick <hash-commit>` te permite tomar un commit específico de cualquier rama y aplicarlo como un nuevo commit en tu rama actual.
*   Es útil cuando quieres incorporar un arreglo de error o una pequeña funcionalidad de otra rama sin fusionar la rama entera.
*   Git intentará aplicar los cambios introducidos por ese commit en tu rama actual. Pueden ocurrir conflictos si los cambios del commit elegido se solapan con cambios en tu rama actual.
*   Crea un **nuevo commit** en tu rama actual, con el mismo mensaje y cambios que el commit original, pero con un nuevo hash (porque es un nuevo commit en un contexto diferente).

**Advertencia:** Si la rama de donde tomaste el commit (`cherry-pick`) eventualmente se fusiona con tu rama actual, podrías terminar con el mismo cambio aplicado dos veces (una vez por el cherry-pick, otra por el merge), lo que podría causar conflictos o duplicación de cambios si no se maneja con cuidado.

> *ENGLISH: `git cherry-pick <commit-hash>` applies the changes introduced by an existing commit to the current branch.*

## Ejercicios Prácticos

**Preparación:**
1.  Asegúrate de estar en tu repositorio `mi-proyecto-git`.
2.  Crea y cámbiate a una nueva rama para estos ejercicios: `git checkout -b modulo6-experimentos`.
3.  Realiza algunos commits "de prueba" en esta rama:
    *   Crea `archivo-a.txt` con "contenido A1". `git add . && git commit -m "feat: Añade archivo A"`
    *   Modifica `archivo-a.txt` a "contenido A2". `git add . && git commit -m "fix: Corrige contenido A"`
    *   Crea `archivo-b.txt` con "contenido B1". `git add . && git commit -m "feat: Añade archivo B"`
    *   Modifica `archivo-b.txt` a "contenido B2". `git add . && git commit -m "refactor: Mejora archivo B"`
    *   Revisa tu historial con `git log --oneline`.

**Ejercicio 1: Rebase Interactivo**
1.  Vamos a limpiar los últimos 3 commits. Queremos:
    *   Unir "feat: Añade archivo B" y "refactor: Mejora archivo B" en un solo commit.
    *   Cambiar el mensaje de "fix: Corrige contenido A".
2.  Ejecuta: `git rebase -i HEAD~3` (o el número de commits que quieras modificar desde el último).
3.  Tu editor se abrirá. Modifica las líneas para que se vean así (el orden de los commits importa):
    ```
    pick <hash_de_fix_Corrige_contenido_A> fix: Corrige contenido A
    pick <hash_de_feat_Añade_archivo_B> feat: Añade archivo B
    squash <hash_de_refactor_Mejora_archivo_B> refactor: Mejora archivo B
    ```
    *   Cambia `pick` a `reword` (o `r`) para "fix: Corrige contenido A".
    *   Deja `pick` para "feat: Añade archivo B".
    *   Cambia `pick` a `squash` (o `s`) para "refactor: Mejora archivo B".
4.  Guarda y cierra.
5.  Git primero se detendrá para que reescribas el mensaje de "fix: Corrige contenido A". Cámbialo a algo como "fix: Contenido de archivo A corregido y finalizado". Guarda y cierra.
6.  Luego, Git combinará "feat: Añade archivo B" y "refactor: Mejora archivo B". Te pedirá que edites el mensaje de commit combinado. Crea un buen mensaje, por ejemplo: "feat: Implementa funcionalidad completa de archivo B". Guarda y cierra.
7.  Revisa tu historial con `git log --oneline`. Debería estar más limpio.

**Ejercicio 2: Tags**
1.  Supongamos que el estado actual de `modulo6-experimentos` es una versión estable.
2.  Crea un tag anotado: `git tag -a v0.1.0-alpha -m "Versión alfa del módulo 6"`
3.  Verifica el tag: `git tag` y `git show v0.1.0-alpha`.
4.  (Opcional, si tienes un remoto configurado para este proyecto): `git push origin v0.1.0-alpha`.

**Ejercicio 3: Cherry-picking**
1.  Vuelve a tu rama principal (asumamos `main`): `git checkout main`.
2.  Supongamos que el commit "fix: Contenido de archivo A corregido y finalizado" de la rama `modulo6-experimentos` contiene un arreglo urgente que necesitas en `main` inmediatamente, sin traer el resto de `modulo6-experimentos`.
3.  Primero, encuentra el hash de ese commit en `modulo6-experimentos`:
    *   `git log modulo6-experimentos --oneline`
    *   Copia el hash del commit "fix: Contenido de archivo A corregido y finalizado".
4.  En tu rama `main`, ejecuta: `git cherry-pick <hash-del-commit-copiado>`.
5.  Verifica el historial de `main`: `git log --oneline`. Deberías ver el commit "fix: Contenido de archivo A corregido y finalizado" como el más reciente. `archivo-a.txt` debería estar en `main` con el contenido "contenido A2". `archivo-b.txt` no debería existir en `main`.

Estos comandos avanzados te dan mucho control sobre tu historial. ¡Úsalos con sabiduría, especialmente `rebase`!
