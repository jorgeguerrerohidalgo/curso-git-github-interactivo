# Módulo 7 – Git Avanzado: Reflog, Bisect y Hooks

## Objetivo
Dominar herramientas avanzadas de Git para la recuperación de trabajo, la depuración eficiente de errores introducidos en el historial y la automatización de tareas mediante hooks.

## Conceptos Clave

### 1. Reflog (`git reflog`)
Git lleva un registro de todas las veces que las puntas de tus ramas (y HEAD) han cambiado en tu repositorio local. Este registro se llama "reflog".
*   Es una especie de historial de tu historial, o un "diario de operaciones" de Git.
*   **Utilidad principal:** Recuperar trabajo "perdido". Si accidentalmente borras una rama, haces un `reset --hard` a un commit incorrecto, o un `rebase` sale mal y crees que has perdido commits, `git reflog` es tu salvavidas.
*   El reflog es **local** para tu repositorio; no se comparte cuando haces `push`.
*   Las entradas del reflog expiran después de un tiempo (por defecto, 90 días para entradas accesibles, 30 días para inaccesibles), así que no es un almacenamiento permanente infinito.

**Uso:**
*   `git reflog`: Muestra el reflog de HEAD. Verás una lista de operaciones (commit, rebase, checkout, reset, merge, etc.) con un puntero `HEAD@{<número>}`.
*   Para recuperar un estado anterior, puedes:
    *   Crear una nueva rama desde ese punto: `git branch rama-recuperada HEAD@{<número>}`
    *   Hacer un reset a ese punto (¡con cuidado!): `git reset --hard HEAD@{<número>}` (si quieres descartar cambios posteriores y volver exactamente a ese estado).

> *ENGLISH: `git reflog` manages reflog information, showing a log of where your HEAD and branch tips have been. It's crucial for recovering lost commits.*

### 2. Bisect (`git bisect`)
`git bisect` es una herramienta increíblemente útil para encontrar el commit exacto que introdujo un error (bug) en tu código. Funciona realizando una búsqueda binaria a través de tu historial de commits.

**Proceso:**
1.  **Iniciar la bisección:**
    *   `git bisect start`
2.  **Marcar un commit "malo" (bad):**
    *   `git bisect bad <commit-malo>`
    *   Usualmente, `<commit-malo>` es el commit actual (`HEAD`) si sabes que el error está presente ahora.
3.  **Marcar un commit "bueno" (good):**
    *   `git bisect good <commit-bueno>`
    *   Este debe ser un commit en el pasado donde sabes que el error NO existía. Puede ser un tag de una versión anterior, o un commit de hace unas semanas.
4.  **Git hace checkout a un commit intermedio:**
    *   Git seleccionará un commit a mitad de camino entre el "bueno" y el "malo".
5.  **Probar el código:**
    *   En este commit intermedio, compila tu código, ejecuta tus pruebas, o verifica manualmente si el error está presente.
6.  **Informar a Git:**
    *   Si el error **está presente** en este commit: `git bisect bad`
    *   Si el error **NO está presente**: `git bisect good`
7.  **Repetir:** Git seleccionará otro commit en el rango restante y repetirás los pasos 5 y 6.
8.  **Resultado:** Después de varias iteraciones, Git te dirá cuál es el primer commit donde se introdujo el error.
9.  **Terminar la bisección:**
    *   `git bisect reset` (Esto te devuelve a la rama donde estabas antes de iniciar `git bisect start`).

**Automatización (opcional):**
*   `git bisect run <script> [argumentos]`
*   Si tienes un script que puede determinar si el código es "bueno" o "malo" (ej. un test que falla si el bug está presente, y pasa si no lo está), `git bisect run` puede automatizar todo el proceso de prueba. El script debe salir con código 0 si es "bueno", y un código entre 1 y 127 (excepto 125) si es "malo".

> *ENGLISH: `git bisect` uses binary search to find the commit that introduced a bug.*

### 3. Git Hooks
Los Git Hooks son scripts que Git ejecuta automáticamente antes o después de ciertos eventos, como `commit`, `push`, `receive` (en el servidor), `merge`, `rebase`, etc.
*   Permiten personalizar el flujo de trabajo y aplicar políticas o automatizar tareas.
*   Se encuentran en el directorio `.git/hooks/` de tu repositorio.
*   Git provee ejemplos de hooks con la extensión `.sample`. Para activar un hook, renómbralo quitándole la extensión `.sample` y asegúrate de que sea ejecutable.
*   Los hooks son **locales** a tu repositorio. No se copian cuando clonas un repositorio. Si quieres compartir hooks con tu equipo, necesitas un mecanismo adicional para distribuirlos e instalarlos.

**Tipos comunes de Hooks (lado del cliente):**
*   **`pre-commit`:** Se ejecuta antes de que se cree un commit, después de que escribas el mensaje. Útil para:
    *   Ejecutar linters o formateadores de código (ej. Prettier, ESLint).
    *   Verificar que los tests pasen.
    *   Comprobar si hay secretos o claves API accidentalmente añadidas.
    *   Si el script sale con un código distinto de cero, el commit se aborta.
*   **`prepare-commit-msg`:** Se ejecuta antes de que se abra el editor de mensajes de commit, pero después de que se cree el mensaje por defecto. Útil para:
    *   Modificar el mensaje de commit por defecto (ej. añadir un número de ticket de un issue tracker).
*   **`commit-msg`:** Se ejecuta después de que hayas escrito el mensaje de commit. Útil para:
    *   Validar que el mensaje de commit siga un formato específico (ej. Conventional Commits).
    *   Si el script sale con un código distinto de cero, el commit se aborta.
*   **`post-commit`:** Se ejecuta después de que un commit se ha completado. Útil para:
    *   Enviar notificaciones.
*   **`pre-push`:** Se ejecuta antes de que tus cambios se suban a un remoto. Útil para:
    *   Ejecutar tests una última vez.
    *   Asegurar que no estás subiendo a la rama equivocada.
    *   Si el script sale con un código distinto de cero, el push se aborta.

**Hooks del lado del servidor (ej. `pre-receive`, `update`, `post-receive`):**
*   Se ejecutan en el repositorio remoto.
*   Útiles para aplicar políticas a nivel de proyecto (ej. asegurar que los commits que llegan cumplen ciertos estándares, desplegar código después de un push exitoso).

> *ENGLISH: Git Hooks are scripts that Git executes before or after events such as: commit, push, and receive. They are a way to customize Git's internal behavior and trigger customizable actions at key points in the development life cycle.*

## Ejercicios Prácticos

**Preparación:**
1.  Asegúrate de estar en tu repositorio `mi-proyecto-git`.
2.  Si no tienes la rama `modulo6-experimentos` del módulo anterior, créala y añade algunos commits. Si la tienes, úsala.
    `git checkout -b modulo7-experimentos` (basada en `main` o `modulo6-experimentos`).

**Ejercicio 1: Reflog - Recuperar un "accidente"**
1.  Haz algunos commits en `modulo7-experimentos`:
    *   Crea `feature-x.txt` con "Trabajo inicial en X". `git add . && git commit -m "WIP: Progreso en feature X"`
    *   Modifica `feature-x.txt` a "Trabajo finalizado en X". `git add . && git commit -m "feat: Completa feature X"`
2.  Anota el hash del commit "feat: Completa feature X" (usa `git log -1`).
3.  Ahora, simula un accidente: `git reset --hard HEAD~1`. Esto te devolverá al commit "WIP: Progreso en feature X" y el commit "feat: Completa feature X" parecerá perdido.
4.  ¡Pánico! Pero recuerda `git reflog`. Ejecútalo.
5.  Busca en la salida del `reflog` la línea que corresponde al commit "feat: Completa feature X". Debería ser algo como `HEAD@{1}: commit: feat: Completa feature X`.
6.  Hay varias formas de recuperarlo:
    *   **Opción A (Crear una nueva rama):** `git branch feature-x-recuperada HEAD@{1}` (o el número correcto del reflog). Luego puedes inspeccionar `feature-x-recuperada`.
    *   **Opción B (Reset de nuevo):** Si estás seguro, `git reset --hard HEAD@{1}`.
7.  Verifica que `feature-x.txt` contenga "Trabajo finalizado en X" y que el log muestre el commit recuperado.

**Ejercicio 2: Bisect - Encontrar un bug**
1.  **Introducir un "bug" en el pasado (simulado):**
    *   Primero, asegúrate de tener al menos 5-6 commits en tu rama `modulo7-experimentos`. Si no, haz algunos cambios simples en `README.md` o `carta.txt` y commitealos.
    *   Usa `git log --oneline` para ver tu historial. Elige un commit que esté unos 3-4 commits atrás del `HEAD`. Copia su hash.
    *   Ahora, vamos a "reescribir la historia" (solo para este ejercicio, ¡cuidado en proyectos reales!) para insertar un bug.
        `git rebase -i <hash-del-commit-elegido>~1` (el `~1` es para empezar el rebase *antes* del commit que elegiste).
    *   En el editor de rebase, cambia `pick` a `edit` para el commit que elegiste. Guarda y cierra.
    *   Git se detendrá en ese commit. Abre algún archivo (ej. `carta.txt`) y añade una línea que claramente sea un "bug", por ejemplo: "ERROR_INTRODUCIDO_AQUI". Guarda el archivo.
    *   `git add .`
    *   `git rebase --continue`
    *   Si todo va bien, el rebase terminará. Ahora tienes un bug en tu historial.

2.  **Iniciar la bisección:**
    *   `git bisect start`
    *   `git bisect bad HEAD` (asumimos que el bug está en el commit actual)
    *   Usa `git log --oneline` para encontrar un commit *anterior* al que editaste (donde sabes que el bug no existía). Copia su hash.
    *   `git bisect good <hash-del-commit-bueno-sin-bug>`

3.  **Proceso de bisección:**
    *   Git hará checkout a un commit.
    *   Revisa `carta.txt` (o el archivo que modificaste).
        *   Si ves "ERROR_INTRODUCIDO_AQUI", ejecuta `git bisect bad`.
        *   Si NO ves "ERROR_INTRODUCIDO_AQUI", ejecuta `git bisect good`.
    *   Repite hasta que Git te diga: `<hash-del-commit> is the first bad commit`. Este es el commit que introdujo el bug.

4.  **Finalizar:**
    *   `git bisect reset` (para volver a `HEAD` de `modulo7-experimentos`).
    *   Ahora que sabes qué commit introdujo el bug, puedes arreglarlo (ej. con `git revert <hash-del-commit-malo>`, o editando y haciendo un nuevo commit).

**Ejercicio 3: Hook `pre-commit` (Conceptual)**
Este ejercicio es más para entender cómo funcionan.
1.  Navega a `.git/hooks/` en tu repositorio.
2.  Verás archivos `.sample`. Renombra `pre-commit.sample` a `pre-commit`.
    *   `mv .git/hooks/pre-commit.sample .git/hooks/pre-commit`
3.  Abre `.git/hooks/pre-commit` en un editor. Es un script de shell.
4.  Puedes modificarlo o reemplazarlo. Por ejemplo, para un test simple, borra su contenido y añade:
    ```bash
    #!/bin/sh
    echo ">>> Ejecutando hook pre-commit..."

    # Ejemplo: No permitir commits con la palabra "WIP" en el mensaje
    COMMIT_MSG_FILE=$1
    # Git pasa la ruta al archivo temporal que contiene el mensaje de commit como primer argumento.
    # Sin embargo, para pre-commit, es más fácil verificarlo en commit-msg.
    # Aquí vamos a hacer un chequeo simple en los archivos que se van a commitear.

    # Chequeo: No permitir archivos llamados 'temp.txt'
    if git diff --cached --name-only | grep -q "temp.txt"; then
        echo "ERROR: No se permite commitear archivos llamados 'temp.txt'."
        exit 1 # Aborta el commit
    fi

    echo ">>> ¡Chequeo pre-commit pasado!"
    exit 0 # Permite el commit
    ```
5.  Haz el script ejecutable: `chmod +x .git/hooks/pre-commit`.
6.  Ahora intenta esto:
    *   Crea un archivo `test.txt`. `git add test.txt`. Intenta hacer `git commit -m "Test normal"`. Debería funcionar.
    *   Crea un archivo `temp.txt`. `git add temp.txt`. Intenta hacer `git commit -m "Test con temp"`. Debería fallar y mostrar tu mensaje de error.
    *   Elimina `temp.txt` y el commit debería funcionar.

Recuerda que los hooks no se versionan con el repositorio. Si quieres compartirlos, busca herramientas como `husky` (para proyectos JavaScript) o incluye un script para instalarlos.

Estas herramientas avanzadas te dan un control aún mayor y capacidades de depuración y automatización en Git.
