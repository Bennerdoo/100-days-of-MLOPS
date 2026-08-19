# Question

Complete the xFusionCorp Industries fraud-detection production DVC pipeline. Three stages are already wired in dvc.yaml, two remain, and the pipeline must finish as a reproducible, SeaweedFS-backed, v1.0-tagged release.

A project exists at `/root/code/ml-pipeline/` with Git and DVC initialised. The `params.yaml` is in place and the `.dvc/config` is pre-configured to push to the SeaweedFS bucket `dvc-storage` at `http://localhost:8333`.
The `ingest, `validate`, and `preprocess` stages are already declared in `dvc.yaml`, but one of them is misconfigured and prevents `dvc repro from completing — run `dvc repro `to see it fail. The two scripts for the remaining stages are pre-staged at `/root/code/ml-pipeline/scripts-staging/train.py and scripts-staging/evaluate.py`, and belong in `scripts/`.

**Acceptance criteria:**

1. The misconfigured existing stage is corrected so `dvc repro `can complete.
2. Two further stages are declared in `dvc.yaml`: 
    `train `– Depends on the preprocessed dataset and `scripts/train.py; reads `n_estimators, `max_depth, `test_size, and `random_seed `from `params.yaml; outputs `models/model.pkl `and `data/processed/test_split.csv; declares `metrics.json `as a DVC metric with cache: false.
    `evaluate `– Depends on `models/model.pkl, `data/processed/test_split.csv, and `scripts/evaluate.py; outputs `reports/evaluation.json `declared with cache: false.
3. The full pipeline has been reproduced, the cache pushed to the SeaweedFS remote, and the current state tagged v1.0.
4. Every change is committed to Git so the release is fully captured.

Open the `SeaweedFS Filer` button at the top of the lab and navigate to `/buckets/dvc-storage/` to confirm that the bucket holds the pushed artefacts under the `files/md5/...` layout.

# Step-by-Step Solution

### 1. Observe the current state.
In the **VS Code file explorer**, open `/root/code/ml-pipeline/` and click through `dvc.yaml`, `params.yaml`, and the `scripts-staging/` folder to see what has been pre-wired and what is still missing.

Then open the VS Code terminal and run the pipeline once so the shipped problem surfaces:
```
cd /root/code/ml-pipeline
dvc repro
```
DVC runs `ingest` and `validate` successfully, then fails at `preprocess` with:
```
ERROR: failed to reproduce 'preprocess': output 'data/processed/cleaned.csv' does not exist
```
The reason is a typo — `preprocess.outs` declares `data/processed/cleaned.csv`, but `scripts/preprocess.py` actually writes `data/processed/clean.csv`.

##### 2. Fix the preprocess output path.
Open `dvc.yaml` in the **VS Code editor**. In the `preprocess` stage, change the last line from:
```
      - data/processed/cleaned.csv
```
to:
```
      - data/processed/clean.csv
```
Save the file (`Ctrl+S`).

##### 3. Copy the remaining scripts into `scripts/`.
The `train.py` and `evaluate.py` files are pre-staged in `scripts-staging/` — visible in the file explorer. Copy them across from the terminal. Run each line on its own and wait for the prompt to return before submitting the next; pasting both lines together is fine, but do not chain them with the next step:
```
cp scripts-staging/train.py    scripts/train.py
cp scripts-staging/evaluate.py scripts/evaluate.py
```

##### 4. Declare the `train` and `evaluate` stages with `dvc stage add`.
Author the two remaining stages from the acceptance criteria. `dvc stage add` writes each stage into `dvc.yaml` for you — you translate the criteria into flags rather than hand-indenting YAML, and each command is a single line, so there is no multi-line terminal-paste hazard. Run each command on its own (wait for the prompt to return before the next).

The `train` stage — depends on the preprocessed data and its script, reads the four params, produces the model + test split, and declares `metrics.json` as a no-cache metric:
```
dvc stage add -n train \
  -d data/processed/clean.csv -d scripts/train.py \
  -p n_estimators,max_depth,test_size,random_seed \
  -o models/model.pkl -o data/processed/test_split.csv \
  -M metrics.json \
  python3 scripts/train.py
```

The `evaluate` stage — depends on the model, the test split, and its script, and writes the evaluation report as a no-cache output:
```
dvc stage add -n evaluate \
  -d models/model.pkl -d data/processed/test_split.csv -d scripts/evaluate.py \
  -O reports/evaluation.json \
  python3 scripts/evaluate.py
```

`-o` declares a **cached** output; `-M` declares a **metrics** file with `cache: false`; `-O` declares an output with `cache: false`. Open `dvc.yaml` afterwards and confirm it now ends with a `train:` stage (with `params`, `outs`, and a `metrics.json` entry under `metrics:` with `cache: false`) followed by an `evaluate:` stage (with `reports/evaluation.json` under `outs:` with `cache: false`).

##### 5. Run the full pipeline.
```
dvc repro
```
DVC executes every stage in order, regenerates `dvc.lock`, and produces the model, metrics, and report files. You should see five stages reach completion.

##### 6. Push artefacts to the SeaweedFS remote.
The remote is pre-configured in `.dvc/config`. Push the cache so the bucket holds every tracked artefact:
```
dvc push
```

##### 7. Commit every change and tag the release as `v1.0`.
```
git add .
git commit -m "Complete production DVC pipeline"
git tag v1.0
```

##### 8. Verify.
Confirm the pipeline is clean, the metrics surface, the bucket is populated, and the tag exists:
```
dvc status
dvc metrics show
git tag -l
AWS_ACCESS_KEY_ID=weedadmin AWS_SECRET_ACCESS_KEY=weedadmin123 \
  aws --endpoint-url=http://localhost:8333 s3 ls s3://dvc-storage/ --recursive | head
```
Then open the **SeaweedFS Filer** button at the top of the lab, navigate to `/buckets/dvc-storage/`, and confirm it now holds the pushed artefacts under the `files/md5/...` layout.

---