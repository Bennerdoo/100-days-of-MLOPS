# Day 1

The xFusionCorp Industries data science team requires a standardized Python environment for their new machine learning project. Set up a virtual environment on the `controlplane` host that includes all necessary ML libraries.

The work is done on the `controlplane` host under `/root/code/`.

The end state must satisfy the following:

- a Python virtual environment named `ml-env` exists under `/root/code/`;
- the environment has `numpy`, `pandas`, `scikit-learn`, and `matplotlib` installed;
- a `requirements.txt` capturing the installed packages is saved at `/root/code/requirements.txt`.

## Solution

### 1. Create the Virtual Environment
First, ensure the directory exists and create the environment using the `venv` module.

```bash
mkdir -p /root/code/
python3 -m venv /root/code/ml-env
```

### 2. Activate and Install Packages
Once created, you need to enter the environment to ensure the libraries are installed locally within it rather than globally.

```bash
# Activate the environment
source /root/code/ml-env/bin/activate

# Upgrade pip for stability (optional but recommended)
pip install --upgrade pip

# Install the required ML libraries
pip install numpy pandas scikit-learn matplotlib
```

### 3. Generate requirements.txt
Finally, capture the specific versions of the installed packages to ensure reproducibility for the rest of the data science team.

```bash
pip freeze > /root/code/requirements.txt
```

### Verification
You can verify the setup by checking the content of the requirements file:

```bash
cat /root/code/requirements.txt
```

> **Note:** When you are finished working, you can exit the environment by simply typing `deactivate`.
