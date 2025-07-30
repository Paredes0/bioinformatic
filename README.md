# Local SBVS Pipeline 🚀

Un flujo de trabajo completo para **Cribado Virtual Basado en Estructura (SBVS)** que combina la comodidad de Google Colab con la potencia de tu ordenador local (Si la tienes ;)).

Este proyecto nace para solucionar, por un lado, los largos tiempos de cómputo, interrupciones y límites de las plataformas gratuitas en la nube y por otro, la incomodidad de uso. Usando una arquitectura híbrida, este pipeline permite ejecutar análisis de docking masivos de manera eficiente, robusta y reanudable.

El código fue desarrollado mediante un enfoque de programación asistida por IA (*vibe coding*) con modelos de lenguaje avanzados, resultando en un notebook funcional y optimizado para la investigación.

## ¿Por qué he creado este proyecto?

Antes de explicar qué es exactamente y como funciona, he pensado en como contextualizar este proyecto, y creo que la mejor manera de hacerlo es explicar brevemente quien soy y por qué he creado esto.

Soy un estudiante de biotecnología de último año, actualmente estoy cursando prácticas extracurriculares en un grupo dedicado a la bioinformática estructural y química computacional llamado [BIO-HPC](https://bio-hpc.eu/), de la universidad UCAM Murcia. En el transcurso de estas prácticas, me he encontrado con dificultades a la hora de utilizar herramientas online gratuitas de tipo SBVS, por ello, con ayuda de la inteligencia artificial (Gemini 2.5 pro) y aplicando mis conocimientos básicos de programación, he creado este notebook en Google Colab.

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
micromamba install -c conda-forge numpy swig boost-cpp libboost tqdm rdkit meeko openbabel
pip install vina
```

### 4. Vincular Colab con el Entorno Local

Para conectar la interfaz de Colab con la potencia de tu máquina, necesitas iniciar un servidor de Jupyter.

```bash
# Si estas en el entorno docking_env todavía, debes salir de él
micromamba deactivate docking_env

# Crear el entorno
micromamba create -n colab_connect

# Activar el entorno
micromamba activate colab_connect

# Instalar JupyterLab en tu entorno y htop para controlar los procesos
micromamba install jupyterlab htop

# Iniciar el servidor de Jupyter
jupyter lab --no-browser --NotebookApp.allow_origin='https://colab.research.google.com' --port=8888
```

Sigue las instrucciones de la terminal para obtener la URL con el token y conéctate desde Google Colab (`Conectar a un entorno de ejecución local`).

(Simplemente copia y pega el link que aparece en la terminal que contenga "http://localhost:8888/lab?token..." en el espacio disponible para una URL en Google Colab dentro del menu (`Conectar a un entorno de ejecución local`)

### 5. Uso Habitual

Lo primero que debes hacer antes de nada, es descargar el archivo .ipynb y subirlo a Google Colab mediante --> "Archivo -> Subir cuaderno" y seleccionar el archivo.

Una vez tienes los dos enviroment instalados (docking_env y colab_connect) con todos los paquetes instalados, tan solo tendrás que realizar el paso anterior sin necesidad de instalar JupyterLab de nuevo, solo activas el enviroment de colab_connect e inicias el servidor de Jupyter y lo pegas en Google Colab para conectarte.

```bash
# Activar el entorno
micromamba activate colab_connect

# Iniciar el servidor de Jupyter de forma segura
jupyter lab --no-browser --NotebookApp.allow_origin='https://colab.research.google.com' --port=8888
```

El enviroment de docking_env trabajará ejecutando las ordenes mandadas desde el notebook de Google Colab al servidor de JupyterLab, no es necesario que te conectes a él desde la terminal, tan solo deja activo el entorno colab_connect y el servidor iniciado.

### 6. Organización de Archivos y Rutas

Quizás lo más enrevesado al principio es organizar todas las rutas a las carpetas y archivos para realizar los trabajos en el propio notebook de GC, para algunas celdas se dan ejemplos de como debería ser la ruta donde esta cada archivo, por ejemplo en los "executable path", que tuve que poner debido a problemas de ejecución que me dieron, pero en otros no.

Para que el notebook funcione correctamente, es crucial entender cómo WSL accede a tus archivos de Windows. La ruta a tu disco `C:` será `/mnt/c/` y a tu disco `D:` será `/mnt/d/`.

Se recomienda crear una carpeta principal para el proyecto. Por ejemplo, en tu disco `D:`:

D:/Bioinformatica/Mi_Proyecto_SBVS/
  - data/
      - receptor.pdb
      - ligandos.sdf
  - results/
      - docking_results.csv
      - best_ligands/
    
Cuando el notebook te pida una ruta, deberás usar el formato de WSL. Por ejemplo, la ruta a `receptor.pdb` sería:
`/mnt/d/Bioinformatica/Mi_Proyecto_SBVS/data/receptor.pdb`

## 🚀 Flujo de Trabajo en el Notebook

Una vez que has conectado tu entorno local, el proceso dentro del notebook de Google Colab es el siguiente:

1.  **Celda 1: Configuración de Rutas**: Lo primero es editar las variables que contienen las rutas a tus archivos (receptor, ligandos, ejecutables y carpetas de resultados). Asegúrate de usar el formato de rutas de WSL (`/mnt/c/...`).
2.  **Celda 2: Preparación de Moléculas**: Ejecuta esta celda para convertir tu receptor y tus ligandos al formato `PDBQT`.
3.  **Celda 3: Docking de Prueba**: Realiza un docking con un solo ligando para asegurar que la caja de unión (`bounding box`) y la configuración son correctas.
4.  **Celda 4: Cribado Virtual Masivo (SBVS)**: Esta celda iniciará el cálculo pesado en tu ordenador local. Recuerda que este proceso puede tardar horas dependiendo de la cantidad de ligandos y parámetros usados (exhaustividad y conformaciones).
5.  **Celda 5: Análisis y Refinamiento**: Una vez finalizado el cribado, esta sección te permite analizar los resultados, seleccionar los mejores candidatos y realizar un docking de mayor precisión con AutoDock Vina.

## ⚠️ ADVERTENCIA: Cómo Detener una Ejecución Local

Cuando inicias un script desde Google Colab, este se ejecuta en tu PC de forma independiente. **Detener la celda en Colab NO detendrá el proceso en tu ordenador.** Para parar una ejecución larga (como un cribado virtual), debes hacerlo manualmente en tu terminal WSL.

Tienes dos maneras principales:

### Opción 1: La Opción Drástica (Cerrar el Servidor Jupyter) 🚨

Esta es la forma más rápida de parar todo de golpe.

1.  Ve a la terminal donde tienes el servidor de `jupyter lab` corriendo.
2.  Presiona `Ctrl + C`.
3.  Te preguntará si quieres detener el servidor. Escribe `y` y presiona `Enter`.

* **Consecuencia:** Esto matará el servidor Jupyter y, por lo general, todos los procesos que inició. Perderás la conexión con Colab y tendrás que reiniciar el servidor para volver a ejecutar cualquier cosa.

### Opción 2: La Opción Precisa y Recomendada (Usar `htop`) ✅

Este método te permite detener un script específico sin tumbar el servidor Jupyter, dándote más control.

1.  **Abre una NUEVA terminal de WSL.** (Deja la terminal de Jupyter corriendo sin tocarla).

2.  **Inicia el monitor de procesos instalado anteriormente junto con JupyterLab:**
    ```bash
    htop
    ```

3.  **Encuentra tu script:** Dentro de `htop`, tienes dos formas de encontrar el proceso:
    * **Filtrar (F4):** Presiona la tecla `F4` y escribe el nombre de tu script, por ejemplo: `run_sbvs.py`.
    * **Ordenar (F6):** Presiona `F6` y selecciona `PERCENT_CPU` para ordenar por uso de CPU. Tu script debería aparecer de los primeros.

4.  **Detén el proceso:**
    * Usa las flechas para seleccionar el proceso principal (ej. `python run_sbvs.py`).
    * Presiona `F9` (Kill).
    * Asegúrate de que esté seleccionada la señal `15 SIGTERM` (terminación ordenada) y presiona `Enter`.

Con este método, solo detienes el cálculo que te interesa y mantienes el servidor Jupyter listo para recibir nuevas órdenes.


## 💻 Rendimiento

Las pruebas de rendimiento muestran una eficiencia notable:

* **~30' por cada 1000 ligandos**.

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

* **LinkedIn**: [Tu Perfil](https://www.linkedin.com/in/no%C3%A9-paredes-alfonso-395328267/)



## Agradecimientos, Licencias y Responsabilidades

Este proyecto es una canalización computacional que integra múltiples herramientas de software de código abierto y científico. Su funcionamiento depende por completo del trabajo excepcional de los desarrolladores y las comunidades que mantienen estos paquetes. A continuación, se detallan las dependencias clave, sus licencias y la forma correcta de citar su trabajo.

### Herramientas Científicas Principales

Este flujo de trabajo utiliza los siguientes programas para la preparación de estructuras, el docking molecular y el cribado virtual. Se recomienda encarecidamente citar las publicaciones originales si utilizas los resultados de este pipeline en tu investigación.

* **Smina**
    * **Función:** Utilizado para el cribado virtual de alto rendimiento y el re-docking de refinamiento.
    * **Licencia:** [Apache License 2.0](https://github.com/smina/smina/blob/master/LICENSE)
    * **Cita Académica:** Koes, D. R., Baumgartner, M. P., & Camacho, C. J. (2013). Lessons learned in empirical scoring with smina from the CSAR 2011 benchmarking exercise. *Journal of chemical information and modeling*, 53(8), 1893-1904.

* **AutoDock Vina**
    * **Función:** Utilizado para el docking de una sola molécula.
    * **Licencia:** [Apache License 2.0](https://github.com/ccsb-scripps/AutoDock-Vina?tab=Apache-2.0-1-ov-file#)
    * **Cita Académica:** Trott, O., & Olson, A. J. (2010). AutoDock Vina: improving the speed and accuracy of docking with a new scoring function, efficient optimization, and multithreading. *Journal of computational chemistry*, 31(2), 455-461.

* **Meeko**
    * **Función:** Preparación de las moléculas del receptor y los ligandos para convertirlos al formato PDBQT.
    * **Licencia:** [GNU General Public License v3.0](https://github.com/forlilab/Meeko/blob/master/LICENSE)

* **RDKit: Open-Source Cheminformatics**
    * **Función:** Utilizado para el manejo de estructuras moleculares (lectura de archivos SDF) y como dependencia fundamental de Meeko.
    * **Licencia:** [BSD 3-Clause License](https://github.com/rdkit/rdkit/blob/master/license.txt)

* **Open Babel**
    * **Función:** Utilizado para la conversión de formatos, específicamente para generar SMILES a partir de los resultados en PDBQT.
    * **Licencia:** [GNU General Public License v2.0](http://openbabel.org/wiki/License)
    * **Cita Académica:** O'Boyle, N. M., et al. (2011). Open Babel: An open chemical toolbox. *Journal of Cheminformatics*, 3(1), 33.

### Entorno y Librerías de Soporte

La ejecución y el análisis de datos son posibles gracias a las siguientes herramientas:

* **Mamba / Micromamba**
    * **Función:** Gestión de paquetes y entornos, permitiendo la instalación robusta de las complejas dependencias científicas.
    * **Licencia:** [BSD 3-Clause License](https://github.com/mamba-org/mamba/blob/main/LICENSE)

* **Pandas**
    * **Función:** Utilizado para la manipulación, análisis y exportación de los datos de resultados.
    * **Licencia:** [BSD 3-Clause License](https://github.com/pandas-dev/pandas/blob/main/LICENSE)

* **NumPy**
    * **Función:** Dependencia fundamental de Pandas para el cálculo numérico.
    * **Licencia:** [BSD 3-Clause License](https://github.com/numpy/numpy/blob/main/LICENSE.txt)

* **Tqdm**
    * **Función:** Proporciona barras de progreso para monitorizar los procesos largos.
    * **Licencia:** [MIT License](https://github.com/tqdm/tqdm/blob/master/LICENCE)

### Descargo de Responsabilidad (Disclaimer)

Este software se proporciona "TAL CUAL", sin garantía de ningún tipo, expresa o implícita. Los resultados generados por este pipeline (como las afinidades de unión) son predicciones teóricas y requieren una validación experimental para ser confirmados.

El autor de este repositorio no se hace responsable de ninguna reclamación, daño u otra responsabilidad derivada del uso o la interpretación de este software o sus resultados. El usuario asume toda la responsabilidad de verificar que el uso de cada una de las herramientas de software subyacentes cumple con sus respectivas licencias.
