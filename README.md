# Examen Parcial 1

Este repositorio contiene el código fuente, recursos y documentación necesarios para la ejecución del proyecto dedicado al examen práctico del primer parcial de la materia de Manejo Masivo de Datos.

Los repositorios Git permiten almacenar el historial completo de cambios realizados sobre un proyecto, facilitando la colaboración entre múltiples desarrolladores, el control de versiones y la recuperación de estados anteriores del código.

Para trabajar con este proyecto es necesario realizar una copia local del repositorio mediante el proceso de clonación, el cual descargará todos los archivos y registros de cambios disponibles.

---

## Requisitos Previos

Antes de comenzar, asegúrese de contar con:

- Git instalado en su equipo
- Python 3.10 o superior
- Pip (gestor de paquetes de Python)
- Acceso al repositorio

Puede verificar las instalaciones ejecutando:

```bash
git --version
python --version
pip --version
```

---

## Clonación del Repositorio

1. Copie la URL del repositorio.

2. Abra una terminal y navegue hasta la carpeta donde desea almacenar el proyecto:

```bash
cd ruta/deseada
```

3. Clone el repositorio:

```bash
git clone https://github.com/AriLeo-droid/Examen_parcial.git
```

4. Acceda al directorio creado:

```bash
cd Examen_parcial
```

---

## Creación de Entorno Virtual (Recomendado)

Crear un entorno virtual permite aislar las dependencias del proyecto.

### Windows

```bash
python -m venv .venv
venv\Scripts\activate
```

### Linux / macOS

```bash
python3 -m venv .venv
source venv/bin/activate
```

---

## Instalación de Dependencias

Instale todas las bibliotecas necesarias mediante el archivo `requirements.txt`:

```bash
pip install -r requirements.txt
```

---

## Estructura General del Proyecto

```text
repositorio/
│
├── data/
│   ├── sensores_industriales.csv
│
├── src/
│   └── Examen_Parcial.ipynb
│
├── Export/
│   └── Alertas_térmicas_sensores_industriales.csv
│
├── requirements.txt
├── README.md
└── .gitignore
```

---

## Ejecución del Proyecto

Una vez instaladas las dependencias, ejecute jupyter lab desde la carpeta en que se situa el proyecto:
```bash
jupyter lab
```
Dentro de la interfaz de Jupyter lab ejecute el proyecto (ubicado en la carpeta `src`)
