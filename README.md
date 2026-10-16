# Detección de intrusiones en redes con UNSW-NB15

Trabajo de Fin de Grado (TFG) --- Roberto García García

Este repositorio contiene el análisis experimental de modelos de
aprendizaje automático para la detección y clasificación de tráfico de
red utilizando el conjunto de datos **UNSW-NB15**. El trabajo se
desarrolla en un notebook de Jupyter e incluye análisis del conjunto de
datos, preprocesamiento, comparación de modelos clásicos y dos
implementaciones de una red neuronal MLP: Scikit-learn y PyTorch.

> **Estado del proyecto:** están implementados los experimentos de
> modelos clásicos y las primeras versiones del MLP con Scikit-learn y
> PyTorch. El benchmark de inferencia, `torch.compile` y la
> implementación con Triton forman parte de las siguientes etapas de
> trabajo y no deben considerarse terminados hasta que se incorporen y
> validen en el notebook.

## Objetivos

-   Analizar el conjunto de datos UNSW-NB15 y comprobar la estructura y
    distribución de sus particiones oficiales.
-   Preparar las características de entrada sin utilizar las etiquetas
    como atributos predictivos.
-   Comparar modelos clásicos de clasificación binaria y multiclase.
-   Estudiar distintas formulaciones del problema multiclase, incluida
    una formulación agrupada.
-   Implementar un MLP con Scikit-learn y reimplementar la misma
    arquitectura con PyTorch.
-   Preparar una base experimental para comparar el rendimiento de
    inferencia de PyTorch, `torch.compile` y, en una fase posterior,
    Triton.

## Contenido del repositorio

La estructura mínima esperada es:

``` text
.
├── README.md
├── Intrussion_Detecto_UNSW-NB15.ipynb
├── data/
│   └── (archivos del conjunto UNSW-NB15, no incluidos necesariamente)
└── .gitignore
```

Los nombres pueden variar según cómo se organice el repositorio. El
notebook actual contiene el flujo principal de análisis y
experimentación; no se presupone que exista todavía una estructura
modular de paquetes Python.

## Dataset

El proyecto utiliza los siguientes archivos de UNSW-NB15:

-   `UNSW_NB15_training-set.csv`: partición oficial de entrenamiento.
-   `UNSW_NB15_testing-set.csv`: partición oficial de prueba.
-   `NUSW-NB15_features.csv`: metadatos/descripción de las
    características, según el nombre utilizado en el notebook.

El notebook compara distintas posibilidades de particionado y documenta
la decisión de conservar la partición oficial. Los experimentos
posteriores deben utilizar `TRAIN_DF` y `TEST_DF`, derivados de las
particiones oficiales, y no las particiones candidatas auxiliares del
análisis.

**Los datos no se incluyen necesariamente en el repositorio.** Descarga
el dataset desde una fuente autorizada de UNSW-NB15 y coloca los
archivos en el directorio local elegido. Comprueba que los nombres de
archivo coincidan con las rutas configuradas en el notebook.

### Rutas de los datos

El notebook contiene inicialmente rutas locales de Windows. Antes de
ejecutarlo en otro equipo, adapta las constantes `TRAIN_PATH`,
`TEST_PATH` y `FEAT_PATH` para que apunten a los archivos existentes en
tu entorno. Se recomienda usar rutas relativas al repositorio para
facilitar la reproducción, por ejemplo:

``` python
from pathlib import Path

ROOT_DIR = Path.cwd()
DATA_DIR = ROOT_DIR / "data"

TRAIN_PATH = DATA_DIR / "UNSW_NB15_training-set.csv"
TEST_PATH = DATA_DIR / "UNSW_NB15_testing-set.csv"
FEAT_PATH = DATA_DIR / "NUSW-NB15_features.csv"
```

Si el notebook no se ejecuta desde la raíz del repositorio, ajusta
`ROOT_DIR` en consecuencia. Verifica también el nombre exacto del
archivo de metadatos, ya que el nombre aparece escrito como
`NUSW-NB15_features.csv` en el código actual.

## Entorno e instalación

Se recomienda Python 3.10 o 3.11 y un entorno virtual. El notebook
imprime las versiones de Python, NumPy y Pandas para facilitar el
seguimiento del entorno.

### 1. Clonar el repositorio

``` bash
git clone <URL_DEL_REPOSITORIO>
cd <NOMBRE_DEL_REPOSITORIO>
```

Sustituye los valores entre `<...>` por la URL y el nombre reales del
repositorio.

### 2. Crear y activar un entorno virtual

**Windows (PowerShell):**

``` powershell
py -3.11 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
```

**Linux / macOS:**

``` bash
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
```

### 3. Instalar dependencias

El notebook utiliza, como mínimo, las siguientes librerías:

-   NumPy
-   Pandas
-   Matplotlib
-   Scikit-learn
-   PyTorch
-   Jupyter Notebook o JupyterLab

Instalación inicial:

``` bash
python -m pip install numpy pandas matplotlib scikit-learn torch jupyter
```

Para abrir el notebook:

``` bash
jupyter notebook
```

También puede utilizarse JupyterLab:

``` bash
python -m pip install jupyterlab
jupyter lab
```

**Nota sobre PyTorch:** el comando anterior instala una distribución
adecuada para el entorno detectado por `pip`, pero la disponibilidad de
CUDA depende de la versión de PyTorch, del sistema operativo, de los
controladores y del hardware. Consulta las instrucciones oficiales de
instalación de PyTorch si quieres habilitar GPU. El notebook está
preparado para seleccionar CUDA cuando `torch.cuda.is_available()`
devuelve `True`; en caso contrario, utiliza CPU.

### 4. Ejecutar el notebook

1.  Coloca los CSV en la carpeta configurada.
2.  Actualiza las rutas de los archivos en las celdas iniciales.
3.  Abre `Intrussion_Detecto_UNSW-NB15.ipynb`.
4.  Ejecuta las celdas en orden, desde el principio.
5.  Comprueba que las rutas, dimensiones de los datos y etiquetas se
    cargan correctamente.

Los experimentos dependen de variables creadas en celdas anteriores. No
se recomienda ejecutar las últimas secciones en una sesión limpia sin
ejecutar primero las celdas de carga, preprocesamiento y entrenamiento.

## Flujo experimental del notebook

### 1. Inspección y validación de datos

-   Carga de los conjuntos de entrenamiento y prueba y de los metadatos.
-   Comprobaciones de columnas, tipos de datos, etiquetas y
    distribuciones.
-   Análisis de diferencias entre las particiones.
-   Decisión documentada de mantener el split oficial.

### 2. Preprocesamiento

-   Separación de las variables objetivo y las características
    predictivas.
-   Tratamiento de las variables numéricas y categóricas.
-   Escalado de las variables numéricas y codificación one-hot de las
    variables categóricas.
-   Ajuste del preprocesador con entrenamiento y transformación del
    conjunto de prueba.

El pipeline genera una matriz de características de **190 atributos**
para los experimentos actuales.

### 3. Modelos clásicos

Se estudian las formulaciones binarias y multiclase, incluyendo:

-   Random Forest
-   Logistic Regression
-   Extra Trees
-   SGD
-   Linear SVM (`LinearSVC`)

También se comparan formulaciones multiclase planas, de solo ataques,
agrupadas y jerárquicas. La formulación agrupada reúne las categorías en
siete grupos para estudiar el efecto de reducir la granularidad de las
etiquetas.

### 4. MLP con Scikit-learn

El notebook utiliza `MLPClassifier` con la arquitectura:

``` text
190 → 128 → 64 → 7
```

Configuración principal documentada en el código:

-   Activación oculta: ReLU
-   Optimizador: Adam
-   `alpha`: `1e-4`
-   Batch size: `256`
-   Learning rate inicial: `1e-3`
-   Máximo: `100` iteraciones
-   Semilla: `42`

Se calculan Accuracy, Macro-F1 y Weighted-F1, además del informe de
clasificación y la matriz de confusión.

### 5. MLP con PyTorch

La implementación de PyTorch conserva la arquitectura
`190 → 128 → 64 → 7`. Utiliza `nn.Linear`, ReLU, `CrossEntropyLoss`,
Adam y batches de 256 registros. El entrenamiento se implementa
explícitamente y registra la pérdida por época.

El notebook evalúa el modelo sobre el conjunto de prueba, genera un
informe de clasificación y una matriz de confusión y guarda la curva de
pérdida como `mlp_pytorch_learning_curve.png`.

## Métricas de evaluación

-   **Accuracy:** proporción total de predicciones correctas.
-   **Macro-F1:** media no ponderada del F1 de las clases; métrica
    principal para valorar el rendimiento con desequilibrio entre
    clases.
-   **Weighted-F1:** media del F1 ponderada por el soporte de cada
    clase.
-   **Classification report:** precisión, recall, F1 y soporte por
    clase.
-   **Matriz de confusión:** distribución de predicciones frente a las
    etiquetas reales.

Los resultados dependen de la ejecución, las versiones de las librerías
y el hardware. Las cifras mostradas en el notebook deben considerarse
resultados experimentales de esa ejecución, no garantías para todos los
entornos.

## Reproducibilidad y consideraciones

-   El notebook establece `SEED = 42` para NumPy y utiliza
    `random_state=SEED` en los modelos que lo admiten.
-   La semilla por sí sola no garantiza determinismo absoluto en todos
    los dispositivos y operaciones de PyTorch.
-   Mantén separadas las particiones de entrenamiento y prueba; ajusta
    el preprocesador únicamente con entrenamiento.
-   No incluyas archivos CSV grandes, modelos entrenados ni entornos
    virtuales en Git salvo que exista una razón explícita para
    versionarlos.
-   No subas rutas personales, credenciales, datos privados ni archivos
    generados de gran tamaño.
-   Si cambias el preprocesamiento o la agrupación de etiquetas,
    documenta el cambio y vuelve a ejecutar los experimentos
    comparables.

## Tecnologías

-   Python
-   Jupyter Notebook
-   NumPy y Pandas
-   Matplotlib
-   Scikit-learn
-   PyTorch
-   Dataset UNSW-NB15

## Autoría

**Roberto García García**\
Trabajo de Fin de Grado --- Ingeniería Informática, Ingeniería de
Computadores.

## Aviso

Este repositorio tiene una finalidad académica y experimental. Los
modelos desarrollados son objeto de evaluación y no deben interpretarse,
sin validaciones adicionales, como un sistema de detección de
intrusiones listo para desplegar en producción.
