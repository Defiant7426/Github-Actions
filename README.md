# Github Actions

❓¿Qué es GitHub Actions?

GitHub Actions es una plataforma de integración continua y entrega continua (CI/CD) que permite automatizar flujos de trabajo directamente desde el repositorio de GitHub. Con GitHub Actions, los desarrolladores pueden crear y gestionar pipelines para construir, probar y desplegar su código.

# Paso 1: Creación de un repositorio

En nuestra cuenta personal de Github vamos a crear un repositorio llamado GitHub-Actions: https://github.com/Defiant7426/Github-Actions

![Untitled](Github%20Actions%20b9c8e81ca6284ad4ab25fa304803b769/Untitled.png)

# Paso 2: Creación del script

Crearemos un script con el objetivo de mostrar algunas estadísticas básicas de mi cuenta de LeetCode en mi README principal de la siguiente manera:

```python
import requests

def get_leetcode_stats(username): # Función para obtener las estadísticas de LeetCode
    url = f"https://leetcode-stats-api.herokuapp.com/{username}" # URL de la API
    response = requests.get(url) # Realizar petición GET a la API
    return response.json() # Devolver respuesta en formato JSON

def update_readme(stats): # Función para actualizar el README.md con las estadísticas
    with open("README.md", "r") as file: # Abrir el archivo README.md en modo lectura
        readme_content = file.read() # Leer el contenido del archivo

    # Delimitadores para identificar la sección de estadísticas
    start_delimiter = "<!--START_LEETCODE_STATS-->"
    end_delimiter = "<!--END_LEETCODE_STATS-->"

    # Crear la nueva sección de estadísticas
    new_stats = f"""
{start_delimiter}
### LeetCode Stats
- **Total Problems Solved:** {stats['totalSolved']}
- **Easy Problems Solved:** {stats['easySolved']}
- **Medium Problems Solved:** {stats['mediumSolved']}
- **Hard Problems Solved:** {stats['hardSolved']}
{end_delimiter}
"""

    # Actualizar el contenido del README.md con las nuevas estadísticas
    updated_readme = readme_content.split(start_delimiter)[0] + new_stats + readme_content.split(end_delimiter)[1]

    with open("README.md", "w") as file: # Abrir el archivo README.md en modo escritura
        file.write(updated_readme) # Escribir el contenido actualizado en el archivo

username = "Defiant7426"  # Reemplaza con tu nombre de usuario de LeetCode
stats = get_leetcode_stats(username) # Obtener las estadísticas de LeetCode
update_readme(stats) # Actualizar el README.md con las estadísticas

```

Explicacion:

Este script obtiene estadísticas de LeetCode mediante una API, actualiza el contenido del archivo [README.md](http://readme.md/) con las estadísticas recuperadas y guarda los cambios. La función `get_leetcode_stats` realiza una solicitud GET a la API de LeetCode Stats y devuelve los datos en formato JSON. La función `update_readme` actualiza el contenido del README con las nuevas estadísticas, delimitadas por comentarios especiales para facilitar su reemplazo.

# Paso 3: Vamos a Github Actions

Subimos el script anterior a nuestro repositorio. Ahora vamos a crear nuestro archivo yml. Para ello, en nuestro repositorio, hacemos clic en la pestaña `Actions` y luego en `set up workflow yourself`.

![Untitled](Github%20Actions%20b9c8e81ca6284ad4ab25fa304803b769/Untitled%201.png)

# Paso 4: Configuración del archivo de flujo de trabajo

Cuando realizamos el paso anterior nos va a salir nuestro entono de trabajo asi:

![Untitled](Github%20Actions%20b9c8e81ca6284ad4ab25fa304803b769/Untitled%202.png)

Crearemos un archivo YAML para definir nuestro flujo de trabajo. Este archivo se ubicará en `.github/workflows/leetcode-stats.yml` 

![Untitled](Github%20Actions%20b9c8e81ca6284ad4ab25fa304803b769/Untitled%203.png)

y contendrá las instrucciones para ejecutar nuestro script cada vez que se realice un push:

```yaml
name: Update README with LeetCode Stats

on:
  schedule:
    - cron: '0 0 * * *'  # Ejecutar diariamente
  push:
    branches:
      - main  # Ejecutar cuando se hagan push a la rama principal

jobs:
  update-readme:
    runs-on: ubuntu-latest

    permissions:
      contents: write
    
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - name: Install dependencies
        run: pip install requests

      - name: Run script
        run: python update_readme.py

      - name: Commit and push changes
        uses: stefanzweifel/git-auto-commit-action@v5
        with:
          commit_message: Update Readme
          commit_user_name: Defiant7426
          commit_user_email: pldelacruzv@gmail.com
          commit_author: Defiant7426 <pldelacruzv@gmail.com>
         
```

Vamos a explicar el contenido de este yaml paso a paso:

```python
name: Update README with LeetCode Stats
```

Explicacion:

En esta línea, estamos nombrando nuestro flujo de trabajo como "Update README with LeetCode Stats".

```python
on:
  schedule:
    - cron: '0 0 * * *'  # Ejecutar diariamente
  push:
    branches:
      - main  # Ejecutar cuando se hagan push a la rama principal
```

Explicacion:

Este bloque de código especifica que el flujo de trabajo se ejecutará diariamente a medianoche y cada vez que se haga un push a la rama `main`.

Luego, en `jobs` estarán los procesos que se ejecutarán automáticamente al cumplirse el tiempo establecido. En este caso, solo tenemos un `job` porque solo tenemos una tarea para realizar. Dentro de este `job` tenemos:

```python
update-readme:
    runs-on: ubuntu-latest
```

Explicacion:

En esta línea, estamos indicando que el flujo de trabajo se ejecutará en la última versión de Ubuntu disponible.

```python
permissions:
  contents: write

```

Explicación:

Este bloque de código otorga permisos de escritura al flujo de trabajo, permitiendo que pueda actualizar el contenido del repositorio.

Luego, los steps del job son un conjunto de pasos que este job debe seguir secuencialmente para poder ejecutarse.

Dentro de el se encuentra:

```python
- name: Checkout repository
        uses: actions/checkout@v4
```

Explicacion:

Este paso descarga el contenido del repositorio para que los archivos puedan ser utilizados en el flujo de trabajo.

```python
- name: Set up Python
  uses: actions/setup-python@v5
  with:
    python-version: '3.12'

```

Explicacion:

Este paso configura el entorno de Python en la versión 3.12 para que el script pueda ejecutarse correctamente.

```python
- name: Install dependencies
  run: pip install requests

- name: Run script
  run: python update_readme.py

```

Explicacion:

Estos pasos instalan las dependencias necesarias para el script (en este caso, la librería `requests`) y luego ejecutan el script `update_readme.py`.

```python
- name: Commit and push changes
  uses: stefanzweifel/git-auto-commit-action@v5
  with:
    commit_message: Update Readme
    commit_user_name: Defiant7426
    commit_user_email: pldelacruzv@gmail.com
    commit_author: Defiant7426 <pldelacruzv@gmail.com>

```

Explicación:

Este paso utiliza una acción de GitHub para automáticamente hacer commit y push de los cambios realizados en el archivo `README.md`.

Al subir los cambios y al aparecer que no existen errores nos aparecera los siguiente:

![Untitled](Github%20Actions%20b9c8e81ca6284ad4ab25fa304803b769/Untitled%204.png)

Esto garantiza que todos los pasos de nuestro archivo `yml` se están ejecutando con éxito.

Al revisar nuestro archivo README podemos notar los cambios:

![Untitled](Github%20Actions%20b9c8e81ca6284ad4ab25fa304803b769/Untitled%205.png)