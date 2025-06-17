# Referencia de Comandos Git

Esta es una lista de comandos útiles de Git, agrupados por funcionalidad.

## Comandos Esenciales (Módulos 1-4)

| Comando         | Descripción                                                                 | Visto en |
|-----------------|-----------------------------------------------------------------------------|----------|
| `git init`      | Inicializa un nuevo repositorio Git en el directorio actual.                | Módulo 1 |
| `git clone <url>`| Crea una copia local de un repositorio remoto.                             | Módulo 4 |
| `git status`    | Muestra el estado de los archivos en el directorio de trabajo y staging area. | Módulo 2 |
| `git add <arch>`| Agrega cambios de `<archivo>` (o todos con `.`) al staging area.            | Módulo 1/2 |
| `git commit -m "msg"`| Guarda los cambios del staging area en el historial con un mensaje.       | Módulo 1/2 |
| `git log`       | Muestra el historial de commits. Opciones: `--oneline`, `--graph`, `-p`.     | Módulo 2 |
| `git diff`      | Muestra diferencias entre estados (working dir vs stage, stage vs HEAD, etc.) | Módulo 2 |
| `git branch <nom>`| Crea una nueva rama llamada `<nombre>`.                                     | Módulo 3 |
| `git branch`    | Lista todas las ramas locales. `git branch -a` (locales y remotas).         | Módulo 3 |
| `git branch -d <nom>`| Elimina la rama local `<nombre>`. Usa `-D` para forzar.                   | Módulo 3 |
| `git checkout <rama>`| Cambia a la rama `<rama>`.                                                | Módulo 3 |
| `git checkout -b <nva>`| Crea la rama `<nueva>` y cambia a ella.                                   | Módulo 3 |
| `git merge <rama>`| Fusiona los cambios de `<rama>` en la rama actual.                          | Módulo 3 |
| `git remote add <nom> <url>`| Añade un repositorio remoto llamado `<nombre>` con la URL `<url>`.     | Módulo 4 |
| `git remote -v` | Lista los repositorios remotos configurados.                                | Módulo 4 |
| `git fetch <remoto>`| Descarga cambios del `<remoto>` pero no los fusiona localmente.             | Módulo 4 |
| `git pull <remoto> <rama>`| Descarga cambios del `<remoto>` y los fusiona en la rama local actual.  | Módulo 4 |
| `git push <remoto> <rama>`| Sube los commits de la `<rama>` local al `<remoto>`.                      | Módulo 4 |
| `git push -u <remoto> <rama>`| Sube y establece seguimiento entre rama local y remota.                 | Módulo 4 |

## Despliegue (Módulo 5)

| Comando                 | Descripción                                                                | Visto en |
|-------------------------|----------------------------------------------------------------------------|----------|
| Configuración en GitHub | (No es un comando) Publicar desde rama `main`, `gh-pages` o carpeta `/docs`. | Módulo 5 |

## Comandos Avanzados (Módulos 6-7)

### Rebase, Tags y Cherry-picking (Módulo 6)
| Comando                 | Descripción                                                                    | Visto en |
|-------------------------|--------------------------------------------------------------------------------|----------|
| `git rebase <base>`     | Re-aplica commits de la rama actual sobre `<base>`, creando historial lineal.  | Módulo 6 |
| `git rebase -i HEAD~N`  | Rebase interactivo de los últimos `N` commits (squash, reword, edit, drop).    | Módulo 6 |
| `git tag <tagname>`     | Crea un tag ligero en el commit actual.                                        | Módulo 6 |
| `git tag -a <tag> -m "msg"`| Crea un tag anotado con mensaje.                                               | Módulo 6 |
| `git tag`               | Lista todos los tags.                                                          | Módulo 6 |
| `git show <tagname>`    | Muestra información del tag y el commit asociado.                              | Módulo 6 |
| `git push <remoto> <tag>`| Sube un tag específico al remoto.                                              | Módulo 6 |
| `git push <remoto> --tags`| Sube todos los tags locales al remoto.                                         | Módulo 6 |
| `git cherry-pick <hash>`| Aplica el commit `<hash>` de otra rama a la rama actual.                       | Módulo 6 |

### Reflog, Bisect y Hooks (Módulo 7)
| Comando                 | Descripción                                                                    | Visto en |
|-------------------------|--------------------------------------------------------------------------------|----------|
| `git reflog`            | Muestra el log de referencia de HEAD (para recuperar "trabajo perdido").       | Módulo 7 |
| `git bisect start`      | Inicia una sesión de búsqueda binaria para encontrar un commit erróneo.        | Módulo 7 |
| `git bisect bad [<commit>]`| Marca un commit como "malo" (donde el error existe). Default: `HEAD`.        | Módulo 7 |
| `git bisect good <commit>`| Marca un commit como "bueno" (donde el error no existe).                       | Módulo 7 |
| `git bisect reset`      | Termina la sesión de `bisect` y vuelve a la rama original.                     | Módulo 7 |
| `git bisect run <script>`| Automatiza la bisección usando un `<script>` que retorna 0 (good) o 1 (bad).   | Módulo 7 |
| `.git/hooks/`           | (Directorio) Contiene scripts de hooks (ej. `pre-commit`, `commit-msg`).       | Módulo 7 |

Esta lista no es exhaustiva, pero cubre muchos de los comandos más utilizados y útiles para el desarrollo diario y avanzado con Git. Consulta la documentación oficial (`git help <comando>`) para más detalles.
