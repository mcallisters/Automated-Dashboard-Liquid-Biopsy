# Automated Liquid Biopsy Dashboard Pipeline

## Overview

The **Automated Liquid Biopsy Dashboard Pipeline** is a Python-based system designed to:

* Upload Genialis Liquid Biopsy reports
* Identify driving mutations
* Match patients with similar mutation profiles
* Update a database with patient-level insights
* Enable downstream analysis and visualization in tools like Tableau

The system integrates **database operations**, **mutation parsing**, **patient matching using cosine similarity**, and a **Tkinter GUI** for end-to-end interaction.

---

## Database Schema (High-Level)

The pipeline interacts with three main tables:

1. **FILE_SOURCE**

   * Stores raw Genialis report data.
   * Key fields: `file_name`, `Chromosome`, `Position`, `ID`, `Impact`, `Sequence_ontology`, `Amino_Acid_Change`, `Gene`, `Allele_Frequency`

2. **MATCHING_RESULT**

   * Stores processed mutation data with COSMIC annotation and match scores.
   * Key fields: `file_name`, `Gene`, `Amino_Acid_Change`, `Allele_Frequency`, `Mutation_cosmic`, `Type`, `Score`

3. **PATIENTS**

   * Stores patient-level matching results after running the similarity algorithm.
   * Key fields: `file_name`, `file_name_sc`, `Cancer_Type`, `Matched`, `Patient_ID`, `Best_Score`, `Potential_patient`

**Workflow**:

1. Files uploaded → `FILE_SOURCE`
2. Parsing, COSMIC mapping → `MATCHING_RESULT`
3. Patient similarity and clustering → `PATIENTS`

---

## Project Structure

```
project-root/
│
├─ assets/                  # GUI assets (icons, buttons, images)
├─ gui_exe.py               # Tkinter GUI script
├─ analysis_pipeline.py     # Main processing pipeline (DB, parsing, matching)
├─ patient_matching.py      # Patient similarity and clustering logic
├─ gui_exe.spec             # PyInstaller spec file for building executable
├─ requirements.txt         # Python dependencies
└─ README.md                # Project documentation
```

---

## Installation

### 1. Clone Repository

```bash
git clone <repo-url>
cd <repo-folder>
```

### 2. Virtual Environment & Dependencies

Create and activate a virtual environment:

```bash
# Windows
python -m venv venv
venv\Scripts\activate

# Linux / macOS
python -m venv venv
source venv/bin/activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

**`requirements.txt`**

```text
numpy==1.22.2
pandas==1.4.1
PyMySQL==1.0.2
scikit_learn==1.1.0
seaborn==0.11.2
tqdm==4.63.1
```

---

## GUI Usage

1. Launch the GUI:

```bash
python gui_exe.py
```

2. Steps:

* Click **Browse** to select a folder with Genialis reports (`.txt`)
* Check **Replace existing files** if you want to overwrite database entries
* Click **Upload** to start processing

3. The pipeline will:

* Validate folder and files
* Parse reports, map to COSMIC mutations
* Update `FILE_SOURCE`, `MATCHING_RESULT`, and `PATIENTS` tables
* Display a message when processing is complete

---

## Building an Executable

1. Install PyInstaller:

```bash
pip install pyinstaller
```

2. Build the executable using your `.spec` file:

```bash
pyinstaller --onefile --windowed --name gui_exe gui_exe.spec
```

* `--windowed` → GUI mode (no console)
* `--onefile` → single executable
* Executable located in `dist/gui_exe.exe`

3. Run:

```bash
dist\gui_exe.exe
```

**Note:** Ensure `assets/` folder is bundled via the `.spec` file or placed alongside the `.exe`.

---

## Developer Notes

* The pipeline is modular:

  * `analysis_pipeline.py` → handles DB operations, report parsing, COSMIC mapping
  * `patient_matching.py` → implements patient similarity and clustering logic
  * `gui_exe.py` → Tkinter GUI frontend
* For testing without affecting the database, comment out insertion lines in `doStuff()`.
* Database must contain the three tables: `FILE_SOURCE`, `MATCHING_RESULT`, `PATIENTS`.

---

## High-Level Workflow

```
Genialis Report (.txt)
        │
        ▼
FILE_SOURCE (Raw Data)
        │
        ▼
Parse Mutations → COSMIC Mapping
        │
        ▼
MATCHING_RESULT (Processed Report)
        │
        ▼
Patient Similarity & Clustering (Cosine Similarity + KMeans)
        │
        ▼
PATIENTS Table (Patient IDs, Best Matches, Scores)
```

---

## License

[Your License Info Here]
