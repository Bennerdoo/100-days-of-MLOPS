# Question

The xFusionCorp Industries Machine Learning platform team maintains a Cookiecutter template from which new ML projects are generated. A draft template is available at /root/code/mlops-template/, but it currently does not render properly. Correct the template and use it to generate a new project.

A Cookiecutter template exists at /root/code/mlops-template/. cookiecutter is installed system-wide, and the template is visible in the VS Code explorer. Run cookiecutter /root/code/mlops-template/ to see how it currently fails to render.
The end state must satisfy every one of the following:

- The cookiecutter.json declares four variables:
`project_name` (default `my-ml-project`)
`author` (default `xFusionCorp`)
`python_version` (default `3.11`)
`ml_framework` with the choices `sklearn`, `pytorch`, and `tensorflow`

- The generated `requirements.txt` logic:
Contains `scikit-learn` when `ml_framework` is `sklearn`
Contains `torch` when `ml_framework` is `pytorch`
Contains `tensorflow` when `ml_framework` is `tensorflow`

- The generated `README.md` content:
Must reference both the `project_name` and the `author` from cookiecutter variables.

- The template directory structure {{cookiecutter.project_name}}/ must contain:
Files: README.md and requirements.txt
Directories: data/, models/, src/, and tests/

- A project generated from the corrected template at /root/code/churn-model/ (with project_name=churn-model and ml_framework=sklearn) contains a requirements.txt listing scikit-learn and a README.md that mentions xFusionCorp.

# Step-by-Step Solution
**About Cookiecutter:** Cookiecutter generates new projects from a template directory. Template variables and their defaults are declared in `cookiecutter.json` — a *list* value becomes a **choice** variable, with the first entry as the default. The project files live under a `{{cookiecutter.project_name}}/` directory and are rendered with Jinja2: `{{ cookiecutter.<var> }}` substitutes a value, and `{% if %}`/`{% elif %}`/`{% endif %}` blocks include content conditionally (Jinja equality is `==`, and every `{% if %}` needs a matching `{% endif %}`). Running `cookiecutter <template> --no-input <var>=<value>` renders the template into a new project.

### 1. Observe the failure.
Try to render the template so that the current problem is visible.
```
cookiecutter /root/code/mlops-template/ -o /root/code/ --no-input project_name=churn-model ml_framework=sklearn
```
Cookiecutter aborts with an error such as `'collections.OrderedDict object' has no attribute 'Author'`, followed by further Jinja syntax errors once that is resolved. The template has four issues: the `cookiecutter.json` does not declare `ml_framework`, the `README.md` uses the wrong case for the author variable, the `requirements.txt` uses a single `=` in its `{% if %}` expressions, and the final `{% endif %}` is missing.

### 2. Correct `cookiecutter.json`.
Add the `ml_framework` variable as a list of choices. Cookiecutter uses the first entry as the default. You can edit the file directly in the VS Code explorer or replace it from the terminal:
```
cat > /root/code/mlops-template/cookiecutter.json << 'EOF'
{
    "project_name": "my-ml-project",
    "author": "xFusionCorp",
    "python_version": "3.11",
    "ml_framework": ["sklearn", "pytorch", "tensorflow"]
}
EOF
```

### 3. Correct the README template.
Jinja variable names are case-sensitive and must match the keys in `cookiecutter.json` exactly — the variable is `author`, not `Author`.
```
cat > /root/code/mlops-template/\{\{cookiecutter.project_name\}\}/README.md << 'EOF'
# {{cookiecutter.project_name}}

Created by {{cookiecutter.author}}.
EOF
```

### 4. Correct the requirements template.
Equality in Jinja is `==`, not `=`. Every `{% if %}` block must be closed with `{% endif %}`.
```
cat > /root/code/mlops-template/\{\{cookiecutter.project_name\}\}/requirements.txt << 'EOF'
{% if cookiecutter.ml_framework == 'sklearn' %}
scikit-learn
{% elif cookiecutter.ml_framework == 'pytorch' %}
torch
{% elif cookiecutter.ml_framework == 'tensorflow' %}
tensorflow
{% endif %}
EOF
```

### 5. Generate the project.
Run `cookiecutter` with `--no-input` so it uses the values supplied on the command line rather than prompting interactively.
```
cookiecutter /root/code/mlops-template/ -o /root/code/ --no-input project_name=churn-model ml_framework=sklearn
```

### 6. Verify.
Confirm the project was generated with the expected structure and content.
```
find /root/code/churn-model -type f
cat /root/code/churn-model/README.md
cat /root/code/churn-model/requirements.txt
```
The `README.md` must reference both `churn-model` and `xFusionCorp`. The `requirements.txt` must list `scikit-learn`.

---