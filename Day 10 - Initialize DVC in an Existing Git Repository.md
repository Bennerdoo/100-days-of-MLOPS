# Question

The xFusionCorp Industries ML team is implementing DVC to ensure that datasets and model files are versioned independently from the codebase. Initialize DVC within the existing Git repository located at /root/code/fraud-detection/ and record this initialization in Git.

A Git repository already exists at `/root/code/fraud-detection/` with an initial commit.
**Acceptance criteria:**
- DVC is initialised inside that repository, so the standard `.dvc/` control directory and `.dvcignore` file exist alongside the existing Git working tree.
- Every file DVC produces during initialisation is recorded in a new Git commit with the message Initialize DVC.
- Once initialisation is complete, the DVC extension will detect the new `.dvc/` directory and surface the DVC TRACKED section in the EXPLORER panel together with a DVC indicator in the bottom status bar.

# Step-by-Step Solution

### Step 1: Navigate to the Git Repository
Change directory to the repository root:

```bash
cd /root/code/fraud-detection/
```
### Step 2: Initialize DVC
Run dvc init to create the DVC configuration directory (.dvc/) and .dvcignore file:

```Bash


dvc init
```
### Step 3: Stage the DVC Files in Git
Stage all files generated during the DVC initialization process:

```Bash


git add .dvc/ .dvcignore
```
### Step 4: Commit the DVC Initialization
Commit the staged DVC files with the exact required commit message:

```Bash


git commit -m "Initialize DVC"
```
### Step 5: Verify Initialization and Commit
Verify that DVC is active and that the Git log reflects the commit:

>Check Git Log:

```Bash


git log -1
```
***Expected output:** Shows the commit message Initialize DVC.

>Check Working Directory:

```Bash


git status
```
***Expected output:*** nothing to commit, working tree clean.