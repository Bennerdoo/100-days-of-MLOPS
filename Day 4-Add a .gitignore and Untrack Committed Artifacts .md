# Question

The xFusionCorp Industries fraud-detection repository was committed without a `.gitignore` file. As a result, Python caches, a trained model file, a virtual environment, notebook checkpoints, and a local secrets file have all been included in version control. Your task is to create a `.gitignore` file and appropriately stop tracking the artifacts that should not be included in Git.

The Git repository is at `/root/code/fraud-detection/`. Standard Python / ML artifacts were committed before any `.gitignore` existed, so ignoring them is not enough — a `.gitignore` never untracks files Git already tracks.

### Requirements:
* A `.gitignore` at the repository root excludes the standard Python / ML artifacts:
  * Python bytecode caches — `__pycache__/` and `*.pyc`
  * Virtual environments — `venv/`
  * Jupyter checkpoints — `.ipynb_checkpoints/`
  * Trained model files — `*.pkl`
  * Local environment files — `.env`
* Those artifacts are removed from Git's index (while remaining on disk) and the cleanup is committed.
* The project sources remain tracked: everything under `src/fraud_detection/`, `README.md`, and `requirements.txt`.

---

# Step-by-Step Solution

### Step 1: Navigate to the Git Repository
Change directory to the repository root:

```bash
cd /root/code/fraud-detection/
```

### Step 2: Create the .gitignore File
Create a .gitignore file at the repository root with patterns matching the artifacts to exclude:

```bash
cat << 'EOF' > .gitignore
__pycache__/
*.pyc
venv/
.ipynb_checkpoints/
*.pkl
.env
EOF
```

### Step 3: Remove Artifacts from Git's Index (Keep on Disk)
Use git rm -r --cached to untrack files that are already tracked in Git without deleting them from the local filesystem:

```Bash


git rm -r --cached __pycache__/ *.pyc venv/ .ipynb_checkpoints/ *.pkl .env 2>/dev/null || git rm -r --cached .
```

>**Note**: If specific files or folders are missing or nested, running git rm -r --cached . after adding .gitignore will unstage all ignored files in bulk while preserving files specified for tracking.

### Step 4: Re-add Required Project Files and .gitignore
Add the new .gitignore file and re-stage the project source files:

```Bash


git add .gitignore src/fraud_detection/ README.md requirements.txt
```

### Step 5: Commit the Cleanup
Commit the untracked artifacts and the new .gitignore file to complete the repository cleanup:

```Bash


git commit -m "Add .gitignore and stop tracking build artifacts, venv, models, and secrets"
```

### Step 6: Verify Repository Status
Ensure that source files remain tracked, excluded files are ignored, and the working tree is clean:

```Bash


git status
```