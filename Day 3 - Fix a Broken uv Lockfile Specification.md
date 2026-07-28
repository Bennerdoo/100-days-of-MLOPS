# Python Dependency Management & Compilation Guide using `uv`

## Question

The xFusionCorp Industries ML team utilizes `uv` and lockfiles to maintain consistent Python dependencies across different machines. A teammate has submitted a `requirements.in` specification that does not adhere to the team's standards. Correct the specification and compile it into a pinned lockfile.

A high-level dependency specification exists at `/root/code/fraud-detection/requirements.in`, but it does not match the team's standards. `uv` is already installed.

### Requirements:
* The corrected `/root/code/fraud-detection/requirements.in` must list **exactly** these four top-level packages:
  * `scikit-learn`
  * `mlflow`
  * `pandas`
  * `numpy`
* Any version constraint must be one `uv` can satisfy against PyPI (bare package names are fine — `uv` pins exact versions when it compiles the lockfile).
* A pinned lockfile `requirements.txt` must be compiled from the corrected specification, pinning each of the four top-level packages to an exact version using `==` and including all transitive dependencies resolved by `uv`.

---

## Step-by-Step Solution

### Step 1: Navigate to the Target Directory
Change into the project directory where the dependency files are located:

```bash
cd /root/code/fraud-detection
```

### Step 2: Correct requirements.in
Overwrite or edit ```/root/code/fraud-detection/requirements.in``` so that it strictly contains the four required top-level packages:

```bash
cat << 'EOF' > /root/code/fraud-detection/requirements.in
pandas
numpy
scikit-learn
mlflow
EOF
```
### Step 3: Compile requirements.in into requirements.txt
Use uv pip compile to generate the pinned requirements.txt lockfile with exact version constraints (==) and all resolved transitive dependencies:

```bash
uv pip compile requirements.in -o requirements.txt
```
### Step 4: Verify the Generated Files
Confirm that both files satisfy all requirements:

Verify requirements.in:

```bash
cat requirements.in
```
Expected output: Exactly scikit-learn, mlflow, pandas, and numpy.

Verify requirements.txt:

```bash
head -n 20 requirements.txt
```
Expected output: Pinned versions (==) for the top-level packages as well as all resolved transitive dependencies.