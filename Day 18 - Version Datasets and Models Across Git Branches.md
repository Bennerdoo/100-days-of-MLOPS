# Question

The xFusionCorp Industries MLOps team versions datasets and models on separate Git branches so it can reproduce and roll between versions cleanly. Tag the current state as v1.0, create a v2-improved branch built on a newer dataset (which retrains the model), and confirm that switching back restores the original data and model.

A project exists at /root/code/fraud-detection/ with a working DVC pipeline (it processes the data and trains a model) and the baseline data/raw/transactions.csv already tracked.
An improved dataset has been pre-staged at /root/code/fraud-detection/data/raw/transactions_v2.csv and is visible in the file explorer. Do not delete this file.

**Acceptance criteria:**

* On the main branch, the current state is tagged `v1.0`.
* A branch named `v2-improved` holds the v2 state: the tracked dataset carries the contents of the v2 file (re-tracked with DVC), the pipeline has been re-run so `models/model.pkl` is retrained and versioned alongside the dataset, and the changes are committed.
* Back on the main branch, the v1 dataset and model are restored on disk, matching the hashes recorded by the `v1.0` tag.
* The DVC extension's `DVC TRACKED` section in the EXPLORER panel reflects the tracked dataset and model for the branch you currently have checked out. To compare the exact hashes recorded on each branch, use `git show <ref>:dvc.lock` or `dvc status`.

# Step-by-Step Solution

### Step 1: Prepare main Branch and Create v1.0 Tag
Navigate to the project root, ensure all baseline files are tracked, and tag the baseline state:

```Bash

cd /root/code/fraud-detection/
git checkout main
```

### Step 2: Ensure the baseline dataset is tracked by DVC and pipeline is executed
```Bash


dvc add data/raw/transactions.csv
dvc repro

# Commit working tree state and tag v1.0
git add .
git commit -m "Baseline v1.0 pipeline state" || true
git tag -a v1.0 -m "Version 1.0 baseline dataset and model"
```
### Step 3: Create and Switch to v2-improved Branch
Create a new branch dedicated to the v2 dataset update:

```Bash


git checkout -b v2-improved
```

### Step 4: Replace Dataset and Force-Re-Track with DVC
Copy transactions_v2.csv over transactions.csv and re-add it to DVC so it updates the MD5 hash inside data/raw/transactions.csv.dvc:

```Bash


# Overwrite existing dataset file with v2 content
cp data/raw/transactions_v2.csv data/raw/transactions.csv

# Force DVC to compute new MD5 hash and update the pointer file
dvc add data/raw/transactions.csv
```
### Step 5: Reproduce the Pipeline
Re-run dvc repro so DVC processes the new data, retrains models/model.pkl, updates metrics.json, and records the new pipeline dependencies in dvc.lock:

```Bash


dvc repro
```
### Step 6: Commit updated trackers to Git
Stage and commit the new .dvc pointer, dvc.lock, and updated metrics to the v2-improved branch:

```Bash


git add .
git commit -m "Track transactions v2 dataset and retrain model"
```
### Step 7: Verify Distinct Dataset Hashes Across Branches
Compare the .dvc file contents between main and v2-improved to confirm that the recorded hashes are different:

```Bash


git show main:data/raw/transactions.csv.dvc
git show v2-improved:data/raw/transactions.csv.dvc
```

Expected output: The md5 value in v2-improved differs from the md5 value on main.

### Step 8: Verify Rollback on main
Switch back to main and execute dvc checkout to restore the baseline dataset and model files to disk:

```Bash

# Switch back to main
git checkout main

# Sync DVC working files on disk to match main's dvc.lock and .dvc pointers
dvc checkout

# Verify DVC reports everything is up to date
dvc status
```