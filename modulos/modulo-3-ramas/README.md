# Módulo 3 – Ramas (Branches)

## Objetivo
Comprender qué son las ramas, por qué son útiles y cómo gestionarlas para trabajar en diferentes líneas de desarrollo de forma aislada.

## Conceptos Clave

### ¿Qué es una Rama?
Imagina que tu proyecto es un árbol. La rama principal (normalmente llamada `main` o `master`) es el tronco. Cuando creas una nueva rama, es como si del tronco creciera una nueva rama independiente. Puedes trabajar en esta nueva rama (por ejemplo, para desarrollar una nueva funcionalidad o arreglar un error) sin afectar al tronco principal ni a otras ramas. Una vez que el trabajo en tu rama está completo y probado, puedes "fusionar" (merge) tus cambios de nuevo al tronco principal.

**Ventajas de usar ramas:**
*   **Desarrollo Paralelo:** Múltiples personas pueden trabajar en diferentes funcionalidades o arreglos simultáneamente.
*   **Aislamiento:** Los cambios para una nueva funcionalidad o un experimento no afectan la versión estable del código hasta que estén listos.
*   **Organización:** Permite mantener un historial más limpio y organizado, ya que los commits relacionados con una tarea específica se agrupan en su propia rama.
*   **Experimentación Segura:** Puedes probar ideas nuevas sin miedo a romper el código principal. Si no funciona, simplemente descartas la rama.

### HEAD: El Puntero Actual
Git tiene un puntero especial llamado `HEAD` (o `head`, es insensible a mayúsculas/minúsculas en algunos sistemas pero se recomienda `HEAD` por convención). `HEAD` normalmente apunta a la rama en la que estás trabajando actualmente. Si estás en la rama `main`, `HEAD` apunta a `main`. Si te cambias a una rama `feature-nueva`, `HEAD` apuntará a `feature-nueva`. Indirectamente, `HEAD` también apunta al último commit de la rama actual.

### Comandos Esenciales

#### `git branch`
*   `git branch`: Lista todas las ramas locales en tu repositorio. La rama actual se marcará con un asterisco (`*`).
*   `git branch <nombre-de-rama>`: Crea una nueva rama con el nombre especificado. Esta acción solo crea la rama, no te cambia a ella. La nueva rama apuntará al mismo commit que la rama actual en la que te encuentras.
*   `git branch -d <nombre-de-rama>`: Elimina la rama especificada. Git te advertirá si la rama tiene cambios que no han sido fusionados.
*   `git branch -D <nombre-de-rama>`: Fuerza la eliminación de la rama, incluso si tiene cambios sin fusionar (¡úsalo con cuidado!).

> *ENGLISH: `git branch` lists, creates, or deletes branches.*

#### `git checkout <nombre-de-rama>`
Se utiliza para cambiar de una rama a otra. Cuando te cambias a otra rama, Git actualiza los archivos en tu directorio de trabajo para que coincidan con la instantánea del último commit de esa rama.
*   `git checkout -b <nombre-de-nueva-rama>`: Es un atajo útil que crea una nueva rama y te cambia a ella en un solo paso. Equivale a `git branch <nombre-de-nueva-rama>` seguido de `git checkout <nombre-de-nueva-rama>`.

> *ENGLISH: `git checkout` switches branches or restores working tree files. `git checkout -b <name>` creates and switches to a new branch.*

#### `git merge <nombre-de-rama-a-fusionar>`
Combina los cambios de la `<nombre-de-rama-a-fusionar>` en la rama actual en la que te encuentras.
*   Por ejemplo, si estás en `main` y ejecutas `git merge feature-login`, Git intentará integrar todos los commits de `feature-login` que no estén ya en `main`.
*   **Fast-forward merge:** Si la rama actual no ha tenido nuevos commits desde que se creó la rama a fusionar, Git simplemente moverá el puntero de la rama actual hacia adelante para que apunte al mismo commit que la rama fusionada. Es una fusión "limpia" y lineal.
*   **Three-way merge (Fusión de tres vías):** Si ambas ramas (la actual y la que se va a fusionar) tienen commits divergentes desde un ancestro común, Git crea un nuevo "commit de fusión" (merge commit) que une ambos historiales. Este commit tiene dos padres.

> *ENGLISH: `git merge` joins two or more development histories together.*

### Conflictos de Fusión (Merge Conflicts)
Un conflicto ocurre cuando Git intenta fusionar cambios de dos ramas que modifican la misma línea de un archivo de manera diferente, o si una rama elimina un archivo que la otra rama modificó. Git no puede decidir automáticamente qué versión es la correcta.
*   Cuando ocurre un conflicto, Git detiene el proceso de fusión y marca los archivos conflictivos.
*   Debes abrir los archivos marcados, buscar las marcas de conflicto (<<<<<<<, =======, >>>>>>>), editar el archivo para resolver las diferencias (decidiendo qué código mantener o combinando ambos), guardar el archivo, hacer `git add` sobre el archivo resuelto, y finalmente `git commit` para completar la fusión.

## Pasos y Ejercicio Práctico

1.  **Verifica tu rama actual:**
    *   Ejecuta `git branch`. Deberías estar en `main` (o `master`) y ser la única rama.

2.  **Crea y cámbiate a una nueva rama:**
    *   Vamos a simular que trabajaremos en una nueva sección para nuestra `carta.txt`.
    *   Ejecuta `git checkout -b seccion-hobbies`.
    *   Ejecuta `git branch` de nuevo. Verás `seccion-hobbies` marcada con `*` y `main`.

3.  **Haz cambios en la nueva rama:**
    *   Abre `carta.txt` y añade una nueva sección titulada "Mis Hobbies" y lista un par de hobbies.
    *   Guarda el archivo.
    *   Ejecuta `git add carta.txt`.
    *   Ejecuta `git commit -m "Añade sección de hobbies a carta.txt"`.

4.  **Haz otro cambio en la misma rama:**
    *   Edita `README.md` y añade la línea "Trabajando en la rama de hobbies".
    *   Guarda el archivo.
    *   Ejecuta `git add README.md`.
    *   Ejecuta `git commit -m "Actualiza README sobre el trabajo en hobbies"`.
    *   Ejecuta `git log --oneline --graph`. Observa cómo tu rama `seccion-hobbies` ha avanzado.

5.  **Regresa a la rama principal y haz un cambio diferente:**
    *   Ejecuta `git checkout main`.
    *   Abre `carta.txt`. Notarás que la sección "Mis Hobbies" ¡NO está aquí! Esto es porque esos cambios solo existen en la rama `seccion-hobbies`.
    *   Ahora, en `main`, añade una nueva línea al final de `carta.txt` que diga: "Esta carta está siendo actualizada en la rama principal.".
    *   Guarda el archivo.
    *   Ejecuta `git add carta.txt`.
    *   Ejecuta `git commit -m "Añade nota sobre actualización en main a carta.txt"`.

6.  **Fusiona la rama de hobbies en main:**
    *   Asegúrate de estar en la rama `main` (`git branch` para confirmar).
    *   Ejecuta `git merge seccion-hobbies`.
    *   **Posible Escenario 1: Fast-forward.** Si no hiciste el paso 5 (el commit en `main` después de crear `seccion-hobbies`), Git podría hacer un fast-forward.
    *   **Posible Escenario 2: Conflicto.** ¡Aquí es donde se pone interesante! Como modificamos `carta.txt` en ambas ramas (`main` y `seccion-hobbies`) en líneas cercanas o iguales, es muy probable que tengas un conflicto.
        *   Git te dirá que hay un conflicto en `carta.txt`.
        *   Ejecuta `git status`. Te mostrará `Unmerged paths`.
        *   Abre `carta.txt`. Verás algo como:
            ```
            <<<<<<< HEAD
            Esta carta está siendo actualizada en la rama principal.
            =======
            Mis Hobbies:
            - Leer
            - Programar
            >>>>>>> seccion-hobbies
            ```
        *   **Resuelve el conflicto:** Edita el archivo. Decide cómo quieres que quede. Por ejemplo, podrías querer ambas secciones:
            ```
            Mis Hobbies:
            - Leer
            - Programar

            Esta carta está siendo actualizada en la rama principal.
            ```
        *   Guarda el archivo `carta.txt` después de resolverlo.
        *   Ejecuta `git add carta.txt`.
        *   Ejecuta `git commit`. Git propondrá un mensaje de commit como "Merge branch 'seccion-hobbies'". Puedes dejarlo o modificarlo. Guarda y cierra el editor.
    *   Si no hubo conflicto y fue un fast-forward, o después de resolver el conflicto, ejecuta `git log --oneline --graph`. Observa cómo se unieron los historiales.

7.  **Elimina la rama (opcional pero buena práctica después de fusionar):**
    *   Una vez que los cambios de `seccion-hobbies` están integrados en `main` y ya no necesitas la rama, puedes eliminarla:
    *   Ejecuta `git branch -d seccion-hobbies`.

¡Excelente! Has aprendido a crear, moverte entre, fusionar ramas y, muy importante, a resolver conflictos de fusión. Las ramas son una herramienta fundamental en Git para cualquier desarrollo que no sea trivial.
