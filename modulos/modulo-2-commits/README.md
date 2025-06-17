# Módulo 2 – Commits y Estado

## Objetivo
Comprender cómo Git rastrea los cambios y cómo inspeccionar el estado y el historial del repositorio.

## Conceptos Clave

### El Área de Preparación (Staging Area)
Antes de que un cambio se registre permanentemente en el historial del proyecto (es decir, antes de hacer un `commit`), Git nos pide que lo preparemos. Imagina que tienes una caja (el área de preparación) donde vas poniendo solo los cambios específicos que quieres guardar en la siguiente "foto" (commit) de tu proyecto. Esto te permite ser selectivo y agrupar cambios relacionados en un solo commit, incluso si tienes muchos archivos modificados en tu directorio de trabajo.

*   **Directorio de Trabajo (Working Directory):** Contiene los archivos actuales de tu proyecto. Aquí es donde haces modificaciones.
*   **Área de Preparación (Staging Area / Index):** Una instantánea de los cambios que se incluirán en el próximo commit. Usas `git add <archivo>` para pasar cambios del directorio de trabajo al área de preparación.
*   **Repositorio Local (.git directory):** Donde Git almacena permanentemente el historial de tu proyecto en forma de commits. Usas `git commit` para tomar lo que está en el área de preparación y guardarlo en el repositorio.

### Comandos Esenciales

#### `git status`
Este es tu comando de referencia constante. Te muestra:
*   En qué rama estás.
*   Si hay archivos modificados en tu directorio de trabajo que no han sido preparados.
*   Si hay archivos preparados que están listos para ser commiteados.
*   Si hay archivos que Git no está rastreando (untracked files).

Ejemplo de salida:
```
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   README.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        nuevo_archivo.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

> *ENGLISH: `git status` shows the current state of your working directory and staging area.*

#### `git add <archivo>`
Añade los cambios del archivo especificado (o todos los cambios de un directorio si especificas un directorio) desde el directorio de trabajo al área de preparación.
*   `git add mi_archivo.txt`: Prepara `mi_archivo.txt`.
*   `git add .`: Prepara todos los archivos nuevos y modificados en el directorio actual y subdirectorios. (¡Úsalo con cuidado!)

> *ENGLISH: `git add` moves changes from the working directory to the staging area.*

#### `git commit -m "Mensaje descriptivo"`
Toma todos los cambios que están en el área de preparación y los guarda permanentemente en el historial del repositorio.
*   El `-m` es para escribir el mensaje del commit directamente en la línea de comandos. Si lo omites, Git abrirá un editor de texto para que escribas un mensaje más largo.
*   **Buenas prácticas para mensajes de commit:**
    *   Deben ser claros, concisos y describir QUÉ cambio hiciste y POR QUÉ.
    *   La primera línea suele ser un resumen corto (ej. "Corrige error en cálculo de total").
    *   Puedes añadir más detalles en líneas subsiguientes si es necesario.

> *ENGLISH: `git commit` records the staged changes to the repository's history.*

#### `git log`
Muestra el historial de commits del proyecto, desde el más reciente al más antiguo.
*   Cada commit tiene un identificador único (hash), el autor, la fecha y el mensaje del commit.
*   **Opciones útiles:**
    *   `git log --oneline`: Muestra una versión condensada del log, con solo el hash y el mensaje del commit.
    *   `git log --graph`: Muestra el log con una representación gráfica de las ramas y fusiones.
    *   `git log -p`: Muestra los cambios (diffs) introducidos en cada commit.
    *   `git log --stat`: Muestra estadísticas de los archivos modificados en cada commit.

> *ENGLISH: `git log` displays the commit history.*

#### `git diff`
Muestra las diferencias entre varios estados en Git.
*   `git diff`: Muestra los cambios en el directorio de trabajo que **no** están en el área de preparación (lo que has modificado pero no has hecho `git add`).
*   `git diff --staged` (o `git diff --cached`): Muestra los cambios que **están** en el área de preparación y listos para el próximo commit (lo que has hecho `git add` pero no `git commit`).
*   `git diff HEAD`: Muestra todos los cambios en tu directorio de trabajo y área de preparación comparados con el último commit (HEAD).
*   `git diff <commit_hash_1> <commit_hash_2>`: Muestra las diferencias entre dos commits específicos.

> *ENGLISH: `git diff` shows differences between commits, commit and working tree, etc.*

## Pasos y Ejercicio Práctico

1.  **Verifica el estado inicial:**
    *   Asegúrate de estar en la raíz de tu repositorio.
    *   Ejecuta `git status`. Deberías ver que tu rama está limpia si acabas de hacer el commit del Módulo 1.

2.  **Modifica un archivo:**
    *   Abre `carta.txt` (creado en el Módulo 1) y añade una nueva línea con tu comida favorita. Guarda el archivo.
    *   Abre `README.md` y añade una línea que diga "Aprendiendo sobre commits.". Guarda el archivo.

3.  **Revisa el estado:**
    *   Ejecuta `git status`. Verás que Git ha detectado cambios en `carta.txt` y `README.md`. Están listados como "Changes not staged for commit".
    *   Ejecuta `git diff`. Observa cómo te muestra las líneas exactas que añadiste en ambos archivos.

4.  **Prepara uno de los archivos:**
    *   Ejecuta `git add carta.txt`.
    *   Ejecuta `git status` nuevamente. Ahora `carta.txt` aparece bajo "Changes to be committed" y `README.md` sigue como "Changes not staged for commit".
    *   Ejecuta `git diff`. ¿Qué observas? Debería mostrar solo los cambios de `README.md`, porque los de `carta.txt` ya no están solo en el directorio de trabajo, sino preparados.
    *   Ejecuta `git diff --staged`. Ahora verás los cambios de `carta.txt` que están listos para el commit.

5.  **Haz un commit:**
    *   Ejecuta `git commit -m "Añade comida favorita a carta.txt"`.
    *   Ejecuta `git status`. `carta.txt` ya no debería aparecer, pero `README.md` sí, como no preparado.

6.  **Prepara y commitea el segundo archivo:**
    *   Ejecuta `git add README.md`.
    *   Ejecuta `git commit -m "Actualiza README con progreso del módulo 2"`.
    *   Ejecuta `git status`. Ahora tu directorio de trabajo debería estar limpio.

7.  **Inspecciona el historial:**
    *   Ejecuta `git log`. Observa los commits que has hecho.
    *   Prueba `git log --oneline`.
    *   Prueba `git log -p -2` (para ver los cambios de los últimos 2 commits).
    *   Copia el hash de tu primer commit (el de "Primer commit" del Módulo 1) y el hash del commit "Añade comida favorita a carta.txt".
    *   Ejecuta `git diff <hash_primer_commit> <hash_commit_carta>`. Observa cómo te muestra solo los cambios realizados en `carta.txt` entre esos dos puntos.

¡Felicidades! Has profundizado en cómo Git maneja los cambios y cómo puedes revisar el estado y el historial de tu proyecto. Mantener commits pequeños y bien descritos es clave para un buen manejo de versiones.
