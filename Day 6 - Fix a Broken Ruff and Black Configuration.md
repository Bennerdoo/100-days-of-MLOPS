# Question

The xFusionCorp Industries ML team enforces code quality standards using ruff and black for every pull request. The current project located at /root/code/fraud-detection/ is failing both tools. Apply the necessary modifications to ensure it passes the checks for both ruff and black.

The project at `/root/code/fraud-detection/` contains a `pyproject.toml` and sample sources under `src/`. `ruff` and `black` are already installed. From the project directory, run `ruff check src/` and `black --check src/` to see how they currently fail.

### The end state must satisfy the following:
- `ruff` and `black` are both configured with a line length of `120`.
- `ruff` lint rule selection includes `E`, `F`, `W`, and `I`.
- Running `ruff check src/` from the project directory exits with status `0`.
- Running `black --check src/` from the project directory exits with status `0`.

## Step-by-Step Solution

### Step 1: Navigate to the Project Directory

```bash
cd /root/code/fraud-detection/
```

### Step 2: Configure pyproject.toml

Edit the `pyproject.toml` file to add or update the `[tool.ruff]` and `[tool.black]` sections to match the required standards. Use a text editor like nano or vim, or a command-line tool like `cat` with a heredoc.

```bash
cat << 'EOF' > pyproject.toml
[tool.black]
line-length = 120

[tool.ruff]
line-length = 120
select = ["E", "F", "W", "I"]
EOF
```

### Step 3: Verify the Configuration

Run `ruff check` and `black --check` from the project root to verify that the new configuration is applied and that the code passes the quality checks.

```bash
ruff check src/
black --check src/
```

Both commands should exit with a status of 0, indicating that no issues were found.