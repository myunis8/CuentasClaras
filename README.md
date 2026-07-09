# Cuentas Claras

App para repartir gastos entre hermanos (alquiler, servicios, limpieza, supermercado, tarjeta de crédito y gastos mensuales), publicada como un único archivo HTML.

## Cómo sincroniza los datos

No hay servidor propio: la app usa un archivo `datos.json` dentro de este mismo repositorio de GitHub como si fuera una base de datos compartida. Cada persona lee y escribe ese archivo directamente desde el navegador, usando la API de GitHub. Funciona bien para dos o tres personas que no necesitan ver los cambios del otro en el instante — alcanza con tocar "Sincronizar" o esperar el chequeo automático cada 45 segundos.

Cada registro (un gasto, una cuota, un ajuste) tiene su propia marca de tiempo interna. Al sincronizar, se descarga el archivo remoto y se combina con lo que tenés localmente registro por registro (no se pisa el archivo entero de una) — gana siempre la versión más reciente de cada ítem. Esto significa que, salvo que ustedes dos editen exactamente el mismo gasto casi al mismo segundo, no se pierden cambios.

## 1. Crear un token personal de GitHub (uno cada uno, por dispositivo)

Cada persona necesita su propio token para que la app pueda leer/escribir el archivo en su nombre. Es información que **solo se guarda en el navegador de cada uno**, nunca se sincroniza ni se sube al repositorio.

1. Andá a [github.com/settings/personal-access-tokens/new](https://github.com/settings/personal-access-tokens/new) (token "fine-grained").
2. Elegí un nombre cualquiera (ej. "Cuentas Claras").
3. En "Repository access", elegí **Only select repositories** y seleccioná este repositorio.
4. En "Permissions" → "Repository permissions", buscá **Contents** y ponelo en **Read and write**. Dejá todo lo demás como está.
5. Generá el token y copialo — GitHub solo lo muestra una vez. Empieza con `github_pat_...`.
6. Repetilo cada uno con su propia cuenta de GitHub (o, más simple, ambos pueden usar la misma cuenta y el mismo token si prefieren no crear dos cuentas — ahí no hace falta repetir este paso).

## 2. Subir la app a GitHub Pages

Desde la carpeta donde está `index.html`:

```bash
git init
git add index.html README.md
git commit -m "Cuentas Claras: primera versión"
git branch -M main
git remote add origin https://github.com/TU-USUARIO/TU-REPO.git
git push -u origin main
```

Creá antes el repositorio vacío en GitHub (botón "New", sin agregar README para no pisar el que ya tenés).

Después, en el repositorio: **Settings** → **Pages** → "Build and deployment" → Source: **Deploy from a branch** → Branch: **main**, carpeta **/ (root)** → Guardar. En uno o dos minutos vas a tener la URL pública, algo como:

```
https://TU-USUARIO.github.io/TU-REPO/
```

Esa es la URL que comparten vos y tu hermano.

## 3. Conectar la app con el repositorio (una vez por dispositivo)

1. Abrí la app y andá a la pestaña **Configuración**.
2. En "Sincronizar con tu hermano (GitHub)", completá:
   - **Usuario u organización**: tu usuario de GitHub (el dueño del repositorio).
   - **Repositorio**: el nombre del repositorio, tal cual en la URL de GitHub.
   - **Archivo de datos**: dejalo en `datos.json` (no hace falta crearlo a mano, la app lo crea sola la primera vez que guarda algo).
   - **Token personal**: el que generaste en el paso 1.
3. Tocá **Guardar y sincronizar**. Si todo está bien configurado, vas a ver "✓ Conectado y sincronizado correctamente."
4. Repetí este paso 3 en el dispositivo de tu hermano, apuntando al mismo usuario/repositorio (con su propio token o el mismo, según lo que hayan definido en el paso 1).

Mientras esto no esté configurado en un dispositivo, esa persona ve solo lo que carga en su propio navegador (se lo avisa con un cartel naranja arriba de todo).

## 4. Uso diario

- Los cambios se guardan solos en GitHub apenas cargás algo.
- Cada 45 segundos, mientras la app esté abierta, se fija en segundo plano si hay novedades (sin interrumpirte si estás completando un formulario).
- Si querés ver de una los cambios del otro, tocá **↻ Sincronizar** arriba a la derecha.
- Si dos personas cargan cosas distintas casi al mismo tiempo, ambas quedan guardadas — no se pisan. Solo si los dos editan **el mismo** gasto casi en simultáneo gana el que guardó último.

## 5. Actualizar la app más adelante

Si en algún momento le pedís a Claude otra modificación al `index.html`:

```bash
git add index.html
git commit -m "Descripción del cambio"
git push
```

GitHub Pages tarda uno o dos minutos en reflejar el cambio. La configuración de sincronización (usuario/repo/token) vive en el navegador de cada uno y no se pierde al actualizar la app.

## Notas de seguridad

- El token queda guardado en el navegador de cada uno. Por eso conviene que sea un token "fine-grained" acotado solo a este repositorio y solo con permiso de "Contents" — así, aunque alguien accediera a ese navegador, no podría tocar nada más de la cuenta de GitHub.
- Si el repositorio es público, cualquiera puede leer `datos.json` navegando el repo. Si preferís que nadie más pueda ver los montos, creá el repositorio como **privado** — GitHub Pages puede publicarse igual desde un repo privado en cuentas gratuitas: la página (el código de la app) queda accesible en su URL pública, pero el archivo `datos.json` solo se puede leer o escribir con un token válido, igual que cualquier otro contenido del repo privado.
