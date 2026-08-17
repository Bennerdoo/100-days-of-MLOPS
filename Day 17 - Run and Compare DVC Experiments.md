# Question

The xFusionCorp Industries MLOps team needs every model training run to be reproducible, automatically tracked, and easy to compare so a chosen configuration can be promoted into version control. The fraud-detection pipeline is parameterized by max_depth, currently set shallow enough to underfit. Using DVC experiments, run three tracked experiments over different max_depth values, compare their recorded f1_score on the held-out test set, and promote the best-scoring run so its parameters, metrics, and model become the tracked workspace state.

A project exists at `/root/code/fraud-detection/` with a parameterised DVC pipeline already in place. params.yaml declares n_estimators: 100 and max_depth: 4, and the baseline pipeline has been run once. src/models/train.py reads both parameters, trains the model, and evaluates it on the held-out test set, writing the real accuracy and f1_score to metrics.json. Do not modify the Python files.
**Acceptance criteria:**
- Three DVC experiments have been run, each with a different value for `max_depth` across a reasonable range (for example` 2, 6, and 12`); each experiment retrains the model and produces a fresh metrics.json.
- The experiment with the highest f1_score is applied to the workspace, so its `max_depth`, `metrics.json`, and `models/model.pkl `become the tracked state.
- The DVC extension's EXPERIMENTS view (open the DVC panel from the Activity Bar) lists every experiment alongside its parameters and metrics, which is a convenient way to compare runs at a glance.

# Step-By-Step Solution

### Step 1: Navigate to the Repository Directory
Change directory to the project repository root:

```Bash

cd /root/code/fraud-detection/
```

### Step 2: Ensure max_depth is Declared in dvc.yaml
Ensure dvc.yaml includes max_depth under the params: section for the train stage:

```YAML


  train:
    cmd: python3 src/models/train.py
    deps:
      - data/processed/train.csv
      - src/models/train.py
    params:
      - n_estimators
      - max_depth
    outs:
      - models/model.pkl
    metrics:
      - metrics.json:
          cache: false
```

### Step 3: Run Three DVC Experiments
Run dvc exp run three times with different -S / --set-param values for max_depth:

```Bash


# Experiment 1: max_depth = 2
dvc exp run -S max_depth=2

# Experiment 2: max_depth = 6
dvc exp run -S max_depth=6

# Experiment 3: max_depth = 12
dvc exp run -S max_depth=12

```

### Step 4: Compare Experiment Results
Display and compare the metrics and parameters across all completed experiment runs:

```Bash

dvc exp show --only-changed
```

Example output:

```
Experiment            Created    f1_score    accuracy    max_depth
workspace             -          ...         ...         12
├── main              ...        ...         ...         4
├── exp-dept2 (2)     10:00 AM   0.7821      0.8100      2
├── exp-dept6 (6)     10:01 AM   0.9654      0.9710      6
└── exp-dept12 (12)   10:02 AM   0.9210      0.9350      12
```

### Step 5: Promote / Apply the Best Experiment
Identify the experiment name or revision hash corresponding to the highest f1_score (e.g., the run with max_depth=6) and apply it to the workspace:

```Bash


# Apply the experiment run with max_depth=6 to the workspace
dvc exp apply <exp_name_or_rev>
```
***(Replace <exp_name_or_rev> with the specific experiment name or Git short-hash shown in your dvc exp show output, such as exp-dept6 or exp-1a2b3c).***

### Step 6: Verify the Workspace State
Confirm that the winning parameters, metrics, and model artifact are active in your working tree:

```Bash

# Check params.yaml:

cat params.yaml
```

***Expected output: Shows max_depth: 12.***

### Step 7: Verify the Metrics

```Bash

# Check metrics.json:

dvc metrics show
```

***Expected output: Shows max_depth: 6.***

### Step 8: Verify the DVC Pipeline Status


```Bash

dvc status

```

***Expected output: Data and pipelines are up to date.***