# Fraud Detection Module Unit Testing, Packaging, and Building Guide

## Question

The xFusionCorp Industries deployment team requires the fraud-detection module to be validated through unit tests and to be packaged as an installable Python distribution. The module's source code and a draft `pyproject.toml` file can be found at `/root/code/fraud-detection/`. Your task is to create unit tests for the module, rectify the packaging configuration, and build a compliant wheel.

The project at `/root/code/fraud-detection/` contains the module source under `src/fraud_detection/` — a `predict()` function that flags a transaction as fraud when its amount (the first feature value) exceeds `100`. The source is complete; you do not need to modify it. `pytest` and `build` are already installed. Use `python3` rather than `python`.

**The end state must satisfy the following:**

Unit tests: tests/test_predict.py contains at least two tests that import predict from fraud_detection and assert on its output — one fraudulent row (amount > 100, expect 1) and one legitimate row (amount <= 100, expect 0); pytest run from the project directory passes.

Packaging configuration: the corrected pyproject.toml satisfies every one of the following:a [build-system] section with requires = ["setuptools>=61.0", "wheel"] and build-backend = "setuptools.build_meta";

name is fraud_detection;

version is 0.1.0;

requires-python is >=3.10;

dependencies is ["scikit-learn", "pandas", "numpy"];

pytest can import the package from src/ — declare [tool.pytest.ini_options] with pythonpath = ["src"].

Built artifact: building the package produces a wheel named fraud_detection-0.1.0-*.whl under dist/.

the existing pyproject.toml:

[project]

name = "fraud-detection"

version = "0.0.1"

description = "Fraud detection model for xFusionCorp Industries"

requires-python = ">=3.8"

dependencies = []



[tool.setuptools.packages.find]

where = ["src"] 


---

# Step-by-Step Solution

### Step 1: Navigate to the Project Directory
Change directory to the project repository root:

```bash
cd /root/code/fraud-detection/
```

### Step 2: Create the Unit Tests Directory and File
Create a `tests/` directory and create `tests/test_predict.py`.

(**Note:** `predict()` iterates over rows expecting subscriptable sequence inputs. Therefore, pass 2D values such as `df.values` or nested lists `[[...]]` rather than passing a raw DataFrame directly).

```bash
mkdir -p tests
```


```bash
cat << 'EOF' > tests/test_predict.py
import pandas as pd
from fraud_detection import predict

def test_predict_fraudulent():
    # Amount > 100 should return 1 (fraud)
    df = pd.DataFrame([[150.0, 0.5, 1.2]])
    predictions = predict(df.values)
    assert predictions[0] == 1

def test_predict_legitimate():
    # Amount <= 100 should return 0 (legitimate)
    df = pd.DataFrame([[50.0, 0.2, 0.8]])
    predictions = predict(df.values)
    assert predictions[0] == 0
EOF
```

### Step 3: Update pyproject.toml
Replace or update `/root/code/fraud-detection/pyproject.toml` to meet all specified packaging metadata and pytest configuration requirements:

```bash


cat << 'EOF' > pyproject.toml
[build-system]
requires = ["setuptools>=61.0", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "fraud_detection"
version = "0.1.0"
description = "Fraud detection model for xFusionCorp Industries"
requires-python = ">=3.10"
dependencies = [
    "scikit-learn",
    "pandas",
    "numpy"
]

[tool.setuptools.packages.find]
where = ["src"]

[tool.pytest.ini_options]
pythonpath = ["src"]
EOF
```

### Step 4: Run Pytest to Validate Unit Tests
Execute `pytest` from the project directory to verify that all unit tests pass successfully:

```bash


pytest
```

### Step 5: Build the Python Distribution Wheel
Use the `build` module with `python3` to compile the package distribution artifacts:

```bash
python3 -m build
```

### Step 6: Verify the Output Wheel Artifact
Confirm that the wheel has been correctly generated inside the dist/ directory matching the required naming pattern:

```bash
ls -l dist/
```

(**Expected** output should include `fraud_detection-0.1.0-py3-none-any.whl` or similar matching `fraud_detection-0.1.0-*.whl`)