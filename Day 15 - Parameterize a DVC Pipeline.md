# Question - task

The xFusionCorp Industries ML team manages model hyperparameters using `params.yaml`, enabling experiments to be conducted without altering the code. In the fraud-detection project, the train stage retrieves the n_estimators parameter from `params.yaml`, but this parameter is not declared to DVC, which means that changing its value does not initiate retraining. Integrate the parameter into the pipeline and illustrate the concept of parameter-driven reproducibility.

A project exists at `/root/code/fraud-detection/` with a three-stage DVC pipeline (`process_data`, `split_data`, `train`) and a `params.yaml` declaring `n_estimators: 100`. `src/models/train.py` already reads `n_estimators` from `params.yaml`. Do not modify the Python files.
The `train` stage in `dvc.yaml` currently has no `params:` section, so DVC does not track `n_estimators` — changing it would not re-run the stage.

## Acceptance criteria:
The `train` stage in `dvc.yaml` lists `n_estimators` under a `params:` section, and the pipeline has been reproduced.
Parameter-driven retraining is demonstrated: with `n_estimators` changed to a different value (for example `200`), re-running the pipeline re-executes only the `train` stage, records the new value in `dvc.lock`, and regenerates `models/model.pkl`.
`dvc params diff` reports changes to the tracked parameter values across Git commits, which is useful when comparing experiments.

# Step-by-Step Solution

### Step 1: Navigate to the Repository Directory
Change directory to the project repository root:

```bash
cd /root/code/fraud-detection/

```

### Step 2: Update dvc.yaml to Track n_estimators
Edit dvc.yaml and add the params: section containing n_estimators to the train stage:

```YAML


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

  train:
    cmd: python3 src/models/train.py
    deps:
      - data/processed/train.csv
      - src/models/train.py
    params:        # Add this section
      - n_estimators 
    outs:
      - models/model.pkl

```

### Step 3: Initial Pipeline Reproduction
Run dvc repro to record the initial parameter state (n_estimators: 100) in dvc.lock:

```bash


dvc repro
```

Commit the baseline state to Git:

```bash


git add dvc.yaml dvc.lock params.yaml
git commit -m "Track n_estimators parameter in DVC train stage"
```

### Step 4: Demonstrate Parameter-Driven Retraining
Change the value of n_estimators to 200 in params.yaml:

```bash


sed -i 's/n_estimators: 100/n_estimators: 200/' params.yaml
```

Re-run dvc repro:

```bash


dvc repro
```

Observe the output: DVC skips process_data and split_data because their inputs haven't changed, and re-runs only the train stage.

### Step 5: Verify Changes with dvc params diff
Inspect the parameter differences against the previous Git commit:

```bash


dvc params diff
```

**Expected output:**

```
Path         Param         Head    Workspace
params.yaml  n_estimators  100     200
```