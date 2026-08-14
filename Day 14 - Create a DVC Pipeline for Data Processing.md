# Question

The xFusionCorp Industries ML team utilizes DVC pipelines to ensure the reproducibility of data processing. The fraud-detection project has the processing scripts and raw data in place but does not yet define a pipeline. Define a two-stage DVC pipeline so the data processing runs reproducibly from start to finish with dvc repro.

A project exists at `/root/code/fraud-detection/` with DVC initialised. The scripts are at `src/data/process_data.py` and `src/data/split_data.py`, and the raw input is at `data/raw/transactions.csv`. Do not modify the Python files or the input data.

# Acceptance criteria

A `dvc.yaml` defines two stages:
- `process_data` – runs `python3 src/data/process_data.py`; depends on `data/raw/transactions.csv` and `src/data/process_data.py`; produces `data/processed/clean_transactions.csv`.
- `split_data` – runs `python3 src/data/split_data.py`; depends on `data/processed/clean_transactions.csv` (the upstream stage's output, so DVC chains the stages) and `src/data/split_data.py`; produces `data/processed/train.csv` and `data/processed/test.csv`.
The pipeline has been reproduced so both stages execute in order and `dvc.lock` is written, and `dvc status` reports no stale stages.
Use `python3` (not `python`) in the stage commands. Once the pipeline is valid, `dvc dag` prints the dependency graph showing how the two stages chain together.

# Step-By-Step Solution

### Step 1: Navigate to the Repository Directory
Change directory to the repository root:

```bash
cd /root/code/fraud-detection/
```

### Step 2: Create the dvc.yaml Pipeline File
Create dvc.yaml in the root of the project with the two pipeline stages:

```bash

cat << 'EOF' > dvc.yaml
stages:
  process_data:
    cmd: python3 src/data/process_data.py
    deps:
      - data/raw/transactions.csv
      - src/data/process_data.py
    outs:
      - data/processed/clean_transactions.csv

  split_data:
    cmd: python3 src/data/split_data.py
    deps:
      - data/processed/clean_transactions.csv
      - src/data/split_data.py
    outs:
      - data/processed/train.csv
      - data/processed/test.csv
EOF
```

### Step 3: Reproduce the DVC Pipeline
Run dvc repro to execute both stages in sequence and write dvc.lock:

```bash


dvc repro
```

### Step 4: Verify the Pipeline Execution and DAG
Confirm that the pipeline ran cleanly and that DVC recognizes the dependency graph:

Verify DVC Pipeline Graph:

```bash


dvc dag
```

**Expected output:** Shows process_data pointing to split_data.

Check DVC Status:

```bash


dvc status
```

**Expected output:** Data and pipelines are up to date.

Verify Lockfile Creation:

```bash


ls -la dvc.lock
```