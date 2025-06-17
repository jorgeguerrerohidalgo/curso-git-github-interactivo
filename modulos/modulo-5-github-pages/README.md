# Módulo 5 – Despliegue con GitHub Pages

## Objetivo
Aprender a publicar sitios web estáticos directamente desde un repositorio de GitHub utilizando GitHub Pages.

## Conceptos Clave

### ¿Qué es GitHub Pages?
GitHub Pages es un servicio de alojamiento de sitios estáticos que toma archivos HTML, CSS y JavaScript directamente desde un repositorio en GitHub, opcionalmente ejecuta los archivos a través de un proceso de construcción (por ejemplo, con Jekyll) y publica un sitio web.

**Características principales:**
*   **Gratuito:** Para repositorios públicos (y uno por cuenta personal/organización para repositorios privados con ciertas limitaciones).
*   **Fácil de configurar:** Se integra directamente con tu flujo de trabajo de Git y GitHub.
*   **Ideal para:** Sitios personales, blogs, documentación de proyectos, portafolios.
*   **Tipos de sitios:**
    *   **Usuario/Organización:** Se publica desde una rama específica (usualmente `main` o `master`) de un repositorio especial llamado `<nombredeusuario>.github.io` o `<nombredelaorganizacion>.github.io`. La URL será `https://<nombredeusuario>.github.io`.
    *   **Proyecto:** Se publica desde una rama específica (ej. `main`, `gh-pages`) o una carpeta `/docs` en la rama `main` de cualquier repositorio. La URL será `https://<nombredeusuario>.github.io/<nombre-del-repositorio>`.

### ¿Qué es un Sitio Estático?
Un sitio estático está compuesto por archivos fijos (HTML, CSS, JavaScript, imágenes) que se entregan al navegador del usuario tal como están almacenados. No involucra procesamiento del lado del servidor ni bases de datos complejas para generar el contenido dinámicamente en cada visita (como lo haría WordPress o un sitio con Python/Django, Node.js/Express, etc.).

### Proceso Básico de Publicación
1.  **Crear/Tener un repositorio en GitHub:** Tu proyecto debe estar alojado en GitHub.
2.  **Añadir archivos del sitio:** Asegúrate de que tu repositorio contenga los archivos HTML, CSS, JS e imágenes que componen tu sitio. Como mínimo, necesitarás un archivo `index.html` en la raíz de la fuente de publicación.
3.  **Configurar la fuente de publicación:** En la configuración de tu repositorio en GitHub, seleccionas la rama (y opcionalmente la carpeta) desde la cual se publicará tu sitio. Las opciones comunes son:
    *   Rama `main` (o `master`)
    *   Rama `gh-pages`
    *   Carpeta `/docs` en la rama `main`
4.  **GitHub construye y despliega:** Una vez configurado, GitHub intentará construir (si es necesario) y desplegar tu sitio.
5.  **Acceder a tu sitio:** Estará disponible en la URL correspondiente. Puede tardar unos minutos en aparecer la primera vez o después de actualizaciones.

## Pasos y Ejercicio Práctico

**Requisito previo:** Necesitas tener una cuenta de GitHub y tu proyecto (o al menos el `index.html` que crearemos) subido a un repositorio en GitHub. Para este ejercicio, asumiremos que continuarás trabajando con el proyecto de los módulos anteriores. Si aún no lo has subido a GitHub:

1.  Ve a [GitHub](https://github.com) y crea un nuevo repositorio (por ejemplo, `mi-curso-git-pages`). Hazlo público.
2.  Sigue las instrucciones que te da GitHub para conectar tu repositorio local existente con este nuevo repositorio remoto y subir tu código. Usualmente implica:
    ```bash
    # En tu carpeta local mi-proyecto-git
    git remote add origin https://github.com/TU_USUARIO/mi-curso-git-pages.git
    git branch -M main  # Asegura que tu rama principal se llame main
    git push -u origin main
    ```

**Ejercicio: Publicar una página de perfil simple**

1.  **Crea un archivo `index.html`:**
    *   En la raíz de tu repositorio local (`mi-proyecto-git`), crea un archivo llamado `index.html`.
    *   Añade el siguiente contenido HTML básico:
        ```html
        <!DOCTYPE html>
        <html lang="es">
        <head>
            <meta charset="UTF-8">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>Mi Página Personal</title>
            <style>
                body { font-family: sans-serif; line-height: 1.6; margin: 20px; background-color: #f4f4f4; color: #333; }
                .container { max-width: 700px; margin: auto; background: #fff; padding: 20px; border-radius: 8px; box-shadow: 0 0 10px rgba(0,0,0,0.1); }
                h1 { color: #333; }
            </style>
        </head>
        <body>
            <div class="container">
                <h1>¡Hola! Soy [Tu Nombre]</h1>
                <p>Bienvenido/a a mi página personal creada con GitHub Pages como parte del curso de Git.</p>
                <p>He aprendido sobre:</p>
                <ul>
                    <li>Commits y estados</li>
                    <li>Ramas y fusiones</li>
                    <li>Colaboración con remotos</li>
                    <li>¡Y ahora GitHub Pages!</li>
                </ul>
                <p>Puedes encontrar el repositorio de este proyecto <a href="https://github.com/TU_USUARIO/mi-curso-git-pages">aquí</a>.</p>
                <!-- Asegúrate de reemplazar [Tu Nombre] y el enlace del repositorio -->
            </div>
        </body>
        </html>
        ```
    *   **Importante:** Reemplaza `[Tu Nombre]` con tu nombre y `https://github.com/TU_USUARIO/mi-curso-git-pages` con la URL real de tu repositorio en GitHub.

2.  **Añade, commitea y sube los cambios:**
    *   `git add index.html`
    *   `git commit -m "Añade página index.html para GitHub Pages"`
    *   `git push origin main` (o la rama principal que estés usando)

3.  **Configura GitHub Pages en GitHub:**
    *   Ve a tu repositorio en GitHub (`https://github.com/TU_USUARIO/mi-curso-git-pages`).
    *   Haz clic en la pestaña "Settings" (Configuración).
    *   En el menú lateral izquierdo, baja hasta la sección "Code and automation" y haz clic en "Pages".
    *   En la sección "Build and deployment":
        *   Bajo "Source", selecciona "Deploy from a branch".
        *   Bajo "Branch", selecciona tu rama principal (`main` o `master`) y la carpeta `/ (root)`.
        *   Haz clic en "Save".

4.  **Espera y visita tu sitio:**
    *   GitHub tardará unos segundos o minutos en desplegar tu sitio.
    *   En la misma sección de configuración de Pages, una vez que esté listo, verás un mensaje como "Your site is live at `https://TU_USUARIO.github.io/mi-curso-git-pages/`".
    *   Haz clic en el enlace para ver tu página publicada.

5.  **Haz un cambio y observa la actualización (opcional):**
    *   Modifica algo en tu `index.html` local (por ejemplo, añade un nuevo hobby a la lista).
    *   `git add index.html`
    *   `git commit -m "Actualiza contenido de la página personal"`
    *   `git push origin main`
    *   Espera unos minutos y refresca tu página de GitHub Pages. Deberías ver los cambios aplicados automáticamente.

¡Felicidades! Has publicado tu primer sitio web utilizando GitHub Pages. Esta es una herramienta muy poderosa para mostrar tus proyectos y compartir información fácilmente.
