# Local SBVS Pipeline 🚀

Un flujo de trabajo completo para **Cribado Virtual Basado en Estructura (SBVS)** que combina la comodidad de Google Colab con la potencia de tu ordenador local (Si la tienes ;)).

Este proyecto nace para solucionar, por un lado, los largos tiempos de cómputo, interrupciones y límites de las plataformas gratuitas en la nube y por otro, la incomodidad de uso. Usando una arquitectura híbrida, este pipeline permite ejecutar análisis de docking masivos de manera eficiente, robusta y reanudable.

El código fue desarrollado mediante un enfoque de programación asistida por IA (*vibe coding*) con modelos de lenguaje avanzados, resultando en un notebook funcional y optimizado para la investigación.

## ¿Por qué he creado este proyecto?

Antes de explicar qué es exactamente y como funciona, he pensado en como contextualizar este proyecto, y creo que la mejor manera de hacerlo es explicar brevemente quien soy y por qué he creado esto.

Soy un estudiante de biotecnología de último año, actualmente estoy cursando prácticas extracurriculares en un grupo dedicado a la bioinformática estructural y química computacional llamado BIO-HPC, de la universidad UCAM Murcia. En el transcurso de estas prácticas, me he encontrado con dificultades a la hora de utilizar herramientas online gratuitas de tipo SBVS, por ello, con ayuda de la inteligencia artificial (Gemini 2.5 pro) y aplicando mis conocimientos básicos de programación, he creado este notebook en Google Colab.

El hecho de que este preparado para conectarse a tu ordenador personal es debido a la baja capacidad de cómputo que tiene la versión gratuita de Google Colab, al final de este README.md se indica que CPU he utilizado yo para estos cálculos.

Espero que le sea útil a alguien como me lo ha sido a mí.

## ✨ Características Principales

* **Preparación de Moléculas**: Convierte receptores y ligandos (desde PDB y SDF) al formato `PDBQT` usando **Meeko**.

* **Docking Individual**: Realiza un docking de prueba con **AutoDock-Vina** para validar la configuración.

* **Procesamiento de Librerías**: Prepara eficientemente librerías de miles de ligandos para el cribado.

* **SBVS de Alto Rendimiento**: Ejecuta el cribado virtual masivo con **Smina**, una bifurcación de Vina optimizada para mayor velocidad.

* **Refinamiento de Resultados**: Selecciona los mejores candidatos y realiza un docking más exhaustivo para predicciones de alta calidad.

* **Cálculo Paralelizado**: Aprovecha todos los núcleos de la CPU local gracias al módulo `multiprocessing` de Python.

* **Sistema Reanudable**: Incluye funciones para reanudar cálculos interrumpidos, evitando la pérdida de progreso.

## ⚙️ Arquitectura y Configuración

El sistema utiliza Google Colab como interfaz de usuario y una máquina local para la computación pesada. Para replicar el entorno, sigue estos pasos:

### 1. Configuración del Entorno Local (WSL)

Es necesario tener un entorno Linux. Se recomienda usar el **Subsistema de Windows para Linux (WSL)**.

### 2. Gestor de Paquetes

Instala **Micromamba** para una gestión de paquetes más rápida y eficiente. Es totalmente necesaria para crear los entornos y ejecutar los comandos de manera segura y eficiente.

Una vez instalado WSL, configuramos y abrimos la terminal para realizar la instalación de los entornos necesarios para trabajar:

```bash
# Ejemplo de instalación de Micromamba
"${SHELL}" <(curl -L micro.mamba.pm/install.sh)

# Actualizar micromamba a la ultima version
micromamba self-update

# Dar permisos de ejecución a Micromamba
chmod +x bin/micromamba
```

### 3. Crear el Entorno Virtual

Crea un entorno virtual con todas las herramientas bioinformáticas necesarias.

```bash
# Crear el entorno
micromamba create -n docking_env

# Activar el entorno
micromamba activate docking_env

# Instalar paquetes desde el canal conda-forge y pip
$ micromamba install -c conda-forge numpy swig boost-cpp libboost sphinx sphinx_rtd_theme tqdm rdkit meeko openbabel gemmi autogrid
$ pip install vina
```

### 4. Vincular Colab con el Entorno Local

Para conectar la interfaz de Colab con la potencia de tu máquina, necesitas iniciar un servidor de Jupyter.

```bash
# Crear el entorno
micromamba create -n colab_connect

# Activar el entorno
micromamba activate colab_connect

# Instalar JupyterLab en tu entorno
micromamba install jupyterlab

# Iniciar el servidor de Jupyter
jupyter lab --no-browser --port=8888
```

Sigue las instrucciones de la terminal para obtener la URL con el token y conéctate desde Google Colab (`Conectar a un entorno de ejecución local`).

(Simplemente copia y pega el link que aparece en la terminal que contenga "http://localhost:8888/lab?token..." en el espacio disponible para una URL en Google Colab dentro del menu (`Conectar a un entorno de ejecución local`))

## 💻 Rendimiento

Las pruebas de rendimiento muestran una eficiencia notable:

* **~1 hora por cada 1000 ligandos** (con 3 posibles conformaciones cada uno).

* **Hardware de prueba**: Intel(R) Core(TM) i5-7400 CPU @ 3.00 GHz (4 núcleos / 4 hilos).
  
* **Se recomienda tener entre 8 y 16 GB de RAM**.

## 🛠️ Stack Tecnológico

| Herramienta        | Propósito                               |
| :----------------- | :-------------------------------------- |
| **AutoDock-Vina** | Docking molecular de precisión          |
| **Smina** | Docking de alto rendimiento para SBVS   |
| **Meeko** | Preparación de moléculas (PDBQT)        |
| **RDKit** | Quimioinformática y manejo de librerías |
| **WSL** | Entorno Linux en Windows                |
| **Conda/Micromamba**| Gestión de paquetes y entornos          |
| **JupyterLab** | Servidor para conexión remota           |
| **Google Colab** | Interfaz de usuario y control           |

## 📄 Licencia

Distribuido bajo la Licencia MIT. Consulta el archivo `LICENSE` para más información.

## 👨‍💻 Autor

**Noé Paredes Alfonso**

* **GitHub**: [Paredes0](https://github.com/Paredes0)

* **LinkedIn**: [Tu Perfil](https://www.linkedin.com/in/tu-usuario/)
