# 🐒 Filogenómica Mitocondrial y Especiación del Género *Saguinus* en la Amazonía Peruana

[![Workflow Status](https://img.shields.io/badge/Workflow-50%25%20Completado-blue?style=for-the-badge)](./scripts/main_workflow.nf)
[![Ambiente Bioinformático](https://img.shields.io/badge/Environment-Nextflow%20%7C%20Conda-green?style=for-the-badge)](./scripts/config/conda_envs.yml)
[![Tipo de Datos](https://img.shields.io/badge/Dataset-ADN%20Mitocondrial-yellowgreen?style=for-the-badge)](#dataset-y-muestras)
[![Plataforma de Ejecución](https://img.shields.io/badge/Execution-Google%20Colab-orange?style=for-the-badge)](./Colab_Execution.ipynb)

---

## 💡 Hipótesis

El proyecto busca probar la hipótesis de especiación en los tamarines amazónicos (género *Saguinus*), utilizando el **ADNmt** como un **marcador haploide** que revela la historia de los linajes maternos.

**Hipótesis Principal (Especiación):**
> La historia evolutiva del **ADN Mitocondrial** de *Saguinus imperator* y *Saguinus mystax* revela una **profunda divergencia filogenética**, consistente con la formación de barreras geográficas (aislamiento por ríos) que limitaron el **flujo genético materno** entre ambas especies, validando un escenario de **especiación alopátrica**.

---

## 🎯 Objetivos

* **Objetivo General:** Adaptar un *pipeline* bioinformático de NGS para analizar las secuencias del **genoma mitocondrial completo** y así inferir la **historia filogenética y el tiempo de divergencia** entre dos especies de *Saguinus* de la Amazonía peruana.
* **Objetivos Específicos:**
    1.  Procesar datos crudos de secuenciación (NGS) de ADNmt para obtener **secuencias consenso** de alta calidad para cada individuo.
    2.  Generar un **alineamiento de secuencias múltiples (MSA)** del genoma mitocondrial completo ($~16.5 \text{ Kbp}$).
    3.  Reconstruir la **filogenia molecular** (usando métodos de Máxima Verosimilitud, ML) y estimar el tiempo de divergencia (*TMRCA*) de los linajes.

---

## 📊 Dataset y Muestras

El análisis se centra en el **genoma mitocondrial completo** (aproximadamente $16.5\text{ Kbp}$) para la reconstrucción filogenética. Los datos serán descargados bajo demanda dentro de la sesión de Google Colab.

| Categoría | Especie | Fuente de Datos | $N$ (Muestras) |
| :--- | :--- | :--- | :--- |
| **Especie A (Foco)** | *Saguinus imperator* | Datos de resecuenciación de ADNmt (NCBI/SRA) | 4 |
| **Especie B (Foco)** | *Saguinus mystax* | Datos de resecuenciación de ADNmt (NCBI/SRA) | 4 |
| **Outgroup** | *Leontocebus fuscicollis* | Secuencia completa de ADNmt (GenBank) | 1 |

**Estructura de la Carpeta de Datos en GitHub (`dataset/`):**
La carpeta contiene únicamente los metadatos y referencias, ya que los archivos FASTQ se descargan en tiempo de ejecución en Colab.

---

## 🛠️ Métodos y Workflow

El *pipeline* está diseñado para ejecutarse en **Google Colab** (o una infraestructura en la nube) aprovechando su hardware, lo que garantiza la **reproducibilidad y escalabilidad** del flujo de trabajo de Genómica Mitocondrial.

### 💻 Tipos de Scripts y Herramientas

| Componente | Tipo de Script/Lenguaje | Herramientas Clave | Uso Específico |
| :--- | :--- | :--- | :--- |
| **Orquestación** | Nextflow DSL2 | Nextflow, Conda | Gestión de tareas y ambientes en Colab. |
| **Adquisición de Datos** | Shell Script (Colab) | `fastq-dump` (SRA-toolkit) | Descarga de lecturas FASTQ de mtDNA. |
| **Alineamiento (ADNmt)** | Nextflow Scripts | `BWA-MEM` o `Bowtie2`, `SAMtools` | Mapeo de lecturas a la referencia mitocondrial. |
| **Filogenia/Análisis** | Nextflow Scripts | `MAFFT`, `RAxML` o `IQ-TREE` | Generación de MSA e inferencia del árbol ML. |

### 🚧 Estado del Proyecto: Avance al 50%

El *workflow* se encuentra al **50% de completitud**, abarcando la etapa de **preparación y ensamblaje de la secuencia consenso mitocondrial**.

#### 📁 Avance Completado (`scripts/`)
1.  **`process_QC_and_Trimming`**: Control de calidad y *trimming*.
2.  **`process_Alignment`**: Mapeo de lecturas al genoma de referencia mitocondrial.
3.  **`process_Consensus`**: Generación de un archivo FASTA de la **secuencia consenso de ADNmt** para cada individuo.

#### ➡️ Siguientes Pasos (50% Restante - Inferencia Evolutiva)
* **Alineamiento de Secuencias Múltiples (MSA)** con `MAFFT`.
* **Inferencia del Árbol Filogenético** (Método de Máxima Verosimilitud).
* **Visualización** y anotación del árbol.

---

## 🚀 Cómo Ejecutar el Workflow en Google Colab

1.  **Acceder al Notebook:** Abre el archivo `Colab_Execution.ipynb`.
2.  **Clonar y Configurar:** La primera celda del *notebook* clona este repositorio e instala Nextflow y el SRA-toolkit.
3.  **Ejecución del Pipeline:**
    ```python
    # Dentro de una celda de código de Colab
    !nextflow run scripts/main_workflow.nf -profile conda --max-cpus 4
    ```

---

## 💾 Estrategia de Adquisición de Dataset (ADNmt)

Para construir tu archivo `SRA_accession_list.txt`, debes buscar proyectos de secuenciación de genomas mitocondriales completos.

### 1. Encontrar la Secuencia de Referencia (FASTA)

Para el paso de alineamiento (`BWA-MEM`), necesitarás un genoma mitocondrial de referencia de alta calidad de una especie de *Saguinus* (ej. *S. imperator*).

* **Búsqueda:** Ve a [NCBI Nucleotide](https://www.ncbi.nlm.nih.gov/nucleotide/) y busca:
    `Saguinus imperator complete genome mitochondrial`
* **Resultado:** Guarda la secuencia FASTA que obtengas en `dataset/referencias/Saguinus_mtDNA_Ref.fasta`.

### 2. Encontrar los Códigos de Acceso (FASTQ)

Para las 8 muestras (**4 de *S. imperator*** y **4 de *S. mystax***) usarás el **Sequence Read Archive (SRA)**.

* **Búsqueda en SRA:**
    1.  Ve a [NCBI SRA](https://www.ncbi.nlm.nih.gov/sra/).
    2.  Busca proyectos que resecuenciaron el genoma nuclear pero que incluyen **lecturas de mtDNA de alto *coverage***. Busca términos como: `Saguinus imperator resequencing` o `Saguinus mystax sequencing`.
* **Filtrado:** Una vez que encuentres un **BioProject** relevante, dirígete a la lista de **experimentos** y filtra por los códigos que corresponden a las especies que necesitas.
* **Colección:** Selecciona 4 códigos SRA (`SRRxxxxxx`) para cada especie y agrégalos a tu archivo `SRA_accession_list.txt`.

El *notebook* de Colab se encargará de descargar estos archivos crudos y enviarlos al *workflow* de Nextflow:

```bash
# Comando ejecutado en Colab por el pipeline (ejemplo):
fastq-dump SRR1234567
