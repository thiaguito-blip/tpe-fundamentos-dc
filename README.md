# TPE – Fundamentos de Ciencia de Datos

Análisis detallado de la **Stack Overflow Developer Survey 2025** para la materia
Fundamentos de Ciencia de Datos (Ingeniería en Sistemas).

El trabajo completo está en el notebook **`Trabajo final.ipynb`**.

---

## Contenido del repositorio

| Archivo | Descripción |
|---|---|
| `Trabajo final.ipynb` | Notebook con todo el análisis |
| `datos.zip` | Contiene `results.csv` (respuestas de la encuesta, ~140 MB descomprimido) |
| `schema.csv` | Diccionario de las columnas de la encuesta |
| `requirements.txt` | Dependencias de Python |
| `results.zip` | Archivo vacío/corrupto (2 bytes). **Ignorarlo**, se usa `datos.zip` |

---

## Requisitos

- **Python 3.10 o superior** (probado con 3.12; el autor lo usó con 3.13).
- **pip** actualizado.
- ~2 GB de RAM libres y ~300 MB de disco.
- Conexión a internet **solo** para instalar las dependencias (y para descargar los datos
  si no se usa `datos.zip`).

---

## Instalación paso a paso

### 1. Clonar el repositorio

```bash
git clone https://github.com/thiaguito-blip/tpe-fundamentos-dc.git
cd tpe-fundamentos-dc
```

### 2. Crear el entorno virtual

**Windows (PowerShell):**

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

> Si PowerShell bloquea la activación, ejecutá una vez:
> `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`
> (o usá `cmd` con `.venv\Scripts\activate.bat`).

**Linux / macOS:**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Cuando el entorno esté activo, el prompt mostrará `(.venv)` al comienzo.

### 3. Instalar las dependencias

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Esto instala, entre otras cosas, **Jupyter** (`jupyter`, `ipykernel`), `numpy`, `pandas`,
`scipy`, `scikit-learn`, `statsmodels`, `kmodes`, `matplotlib`, `seaborn` y `wget`.

Versiones con las que se probó el notebook:

| Paquete | Versión |
|---|---|
| numpy | 2.5.4 |
| pandas | 3.1.1 |
| scipy | 1.18.1 |
| scikit-learn | 1.9.1 |
| statsmodels | 0.15.0 |
| kmodes | 0.12.2 |
| matplotlib | 3.11.2 |
| seaborn | 0.13.2 |
| notebook | 7.6.5 |

### 4. Descomprimir los datos

El notebook busca `results.csv` y `schema.csv` **en la misma carpeta que el notebook**
(la raíz del repositorio). `schema.csv` ya está; falta extraer `results.csv`:

**Windows (PowerShell):**

```powershell
Expand-Archive -Path datos.zip -DestinationPath . -Force
```

**Linux / macOS:**

```bash
unzip -o datos.zip
```

Verificá que quedó `results.csv` junto al notebook (≈ 140 MB).

> **Alternativa:** si `results.csv` no existe, la primera celda intenta descargarlo
> automáticamente desde el repositorio oficial de Stack Overflow. Eso requiere internet y
> acceso a `media.githubusercontent.com`; si estás detrás de un proxy, VPN o red
> corporativa puede fallar con `getaddrinfo failed`. En ese caso usá `datos.zip`.

### 5. (Opcional) Registrar el kernel en Jupyter

```bash
python -m ipykernel install --user --name tpe-fundamentos --display-name "TPE Fundamentos"
```

Así podés elegir ese kernel desde Jupyter o VS Code/PyCharm.

### 6. Abrir el notebook

```bash
jupyter notebook
```

Se abrirá el navegador; abrí **`Trabajo final.ipynb`**. Si registraste el kernel en el
paso 5, seleccionalo en *Kernel → Change kernel*. Si usás JupyterLab:
`jupyter lab`.

Para ejecutar todo: **Kernel → Restart & Run All**.

---

## ⏱️ Tiempo de ejecución (importante)

El notebook **puede demorar bastante** en ejecutarse, principalmente por los **t-SNE**
(con distancia de Hamming) y por el clustering (K-modes con `n_init=10`, y el barrido de
K-means con silueta para distintos `k`).

Referencia: en una máquina con **1 solo núcleo y 3 GB de RAM**, el notebook completo
(178 celdas) se ejecutó **sin errores en unos 5 minutos**. Las celdas más pesadas fueron
las dos de K-modes + t-SNE (~130 s y ~100 s) y el barrido de K-means con silueta (~40 s).
Con más núcleos puede ser menor; con equipos más modestos, mayor.

Podés **editar libremente el código** para acelerarlo, por ejemplo trabajando con una
**muestra** de los datos antes del t-SNE:

```python
df = Stack_apartadas[cols_js + ["considera_cambio", "NewRole"]].dropna()
df = df.sample(n=5000, random_state=0)   # <- muestra (ajustar n)
X = df[cols_js]
```

También se puede bajar el costo reduciendo `n_init` en `KModes` o limitando el rango de
`k` en el barrido de K-means.

> ⚠️ **Advertencia:** usar una muestra **puede modificar los resultados** (forma y
> separación de los clusters en el t-SNE, asignaciones de K-modes, métricas de silueta,
> etc.). t-SNE además es sensible a sus hiperparámetros y a la semilla. Para reproducir
> exactamente lo reportado en el trabajo, ejecutá con **todos los datos** y
> `random_state=0`.

---

## Notas y problemas frecuentes

- **Las primeras celdas contienen `!pip install ...`.** Funcionan dentro de Jupyter y
  se instalan en el entorno del kernel. Si ya hiciste el paso 3 no hace falta que las
  ejecutes, pero no rompen nada.
- **`FileNotFoundError: results.csv`**: no extrajiste `datos.zip` en la carpeta del
  notebook, o abriste Jupyter desde otra carpeta. Ejecutá `jupyter notebook` desde la
  raíz del repositorio.
- **`getaddrinfo failed` / `URLError`**: problema de red al intentar descargar los datos.
  Extraé `datos.zip` (paso 4) y no hará falta descargar nada.
- **`DtypeWarning: Columns ... have mixed types`** al leer `results.csv`: es solo un
  aviso por columnas de tipos mixtos; no afecta la ejecución.
- **El kernel se reinicia por falta de memoria**: cerrá otros programas o usá una
  muestra (ver arriba).
- **Windows:** si `python` no se reconoce, probá con `py -m venv .venv`.

---

## Fuente de los datos

Stack Overflow Annual Developer Survey 2025 –
<https://github.com/StackExchange/Survey> (licencia ODbL).
