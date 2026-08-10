# Question
A teammate has added the transactions dataset to the xFusionCorp Industries fraud-detection repository. However, it was committed directly to Git rather than being tracked with DVC. Your task is to align the repository with the team standard, ensuring that every dataset under the data/ directory is tracked by DVC instead of Git.

A project exists at /root/code/fraud-detection/ with DVC already initialised. The dataset data/raw/transactions.csv is currently tracked by Git, and the team standard requires DVC to own it instead.
## Acceptance criteria:
- Git no longer tracks the dataset, but the file remains on disk.
- The dataset is tracked by DVC instead: a .dvc pointer file exists and data/raw/.gitignore excludes the dataset itself.
- The new .dvc pointer and .gitignore are recorded in a Git commit with the message Track transactions dataset with DVC.
>Once tracking is moved to DVC, the DVC TRACKED section in the EXPLORER panel will list the dataset, confirming the extension recognises it as a DVC-managed file.


# Step-by-step solution
### Step 1: Navigate to the Repository
Change directory to the project root:

```bash
cd /root/code/fraud-detection/
```
### Step 2: Remove the Dataset from Git's Index (Keep on Disk)
Untrack data/raw/transactions.csv from Git without removing the actual file from the filesystem:

```Bash


git rm --cached data/raw/transactions.csv
```
### Step 3: Add the Dataset to DVC
Track data/raw/transactions.csv with DVC. This generates `data/raw/transactions.csv.dvc` and creates or updates `data/raw/.gitignore`:

```Bash


dvc add data/raw/transactions.csv
```
### Step 4: Stage the DVC Pointer and .gitignore Files in Git
Stage the newly created .dvc file and the updated .gitignore file into Git:

```Bash


git add data/raw/transactions.csv.dvc data/raw/.gitignore
```
### Step 5: Commit the Changes to Git
Commit the changes using the exact specified commit message:

```Bash


git commit -m "Track transactions dataset with DVC"
```
### Step 6: Verify the Setup
Ensure both Git and DVC statuses are clean and correct:

**Verify File Presence on Disk:**

```Bash


ls -la data/raw/transactions.csv
```
### Verify DVC Tracking:

```Bash


dvc status
```
**Expected output:** Data and pipelines are up to date.

### Verify Git Log:

```Bash


git log -1
```
**Expected output:** Shows commit message Track transactions dataset with DVC.