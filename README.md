# TFG — Intrusion Detection con UNSW-NB15

Este repositorio contiene los archivos del Trabajo Fin de Grado (TFG) relacionados con la detección de intrusiones usando el dataset UNSW-NB15.

Contenido principal
- `notebooks/Intrussion_Detecto_UNSW-NB15.ipynb` — cuaderno principal con el análisis y experimentos.
- `environment.yml` — entorno Conda recomendado.
- `requeriments.txt` — dependencias pip alternativas.
- `DATASETS/` — CSV del dataset UNSW-NB15 (archivos grandes).

Requisitos
- Conda (recomendado) o Python 3.8+.

Instalación (Conda)

```bash
conda env create -f environment.yml
conda activate unsw-nb15-tfg
```

Instalación (pip)

```bash
python -m pip install -r requeriments.txt
```

Notas sobre los datos (archivos grandes)
- La carpeta `DATASETS/` contiene varios CSV grandes (varios archivos >100 MB). GitHub puede rechazar push de archivos >100 MB.
- Si planeas versionar esos CSV en el repositorio, instala y usa Git LFS:

```bash
# Instala git-lfs (solo una vez)
git lfs install
# Trackea los CSV
git lfs track "DATASETS/*.csv"
git add .gitattributes
git add DATASETS/*.csv
git commit -m "Track CSVs with Git LFS"
git push origin main
```

Si no quieres o no puedes usar Git LFS, descarga los CSV desde la fuente original y mantenlos fuera del repo (añade `DATASETS/` a `.gitignore`), o elimina los archivos grandes del historial con herramientas como `git filter-repo` o `bfg`.

Ejecutar los notebooks
- Levanta Jupyter Lab o Notebook y abre `notebooks/Intrussion_Detecto_UNSW-NB15.ipynb`.

```bash
jupyter lab
```

Buenas prácticas
- No fuerces `git push --force` a menos que estés seguro; puede sobrescribir trabajo remoto.
- Mantén los datos sensibles fuera del repositorio público.

Contacto
- Autor: Roberto García García

--
README generado automáticamente para el TFG.
