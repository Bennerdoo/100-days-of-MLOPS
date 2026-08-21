# Question

The xFusionCorp Industries ML team is in the process of adopting MLflow for their experiment tracking. Your task is to set up a local MLflow tracking server on the ML pipeline workstation, enabling the team to log experiments from their training code.

MLflow is pre-installed on the controlplane. Launch the tracking server in the background and choose the flags that satisfy every end-state requirement below.
The server listens on port 5000 and is reachable on all network interfaces, not only localhost.

The backend store is a SQLite database at /root/code/mlflow-backend/mlflow.db. Create any parent directory first — MLflow aborts at startup if the backend directory is missing.

The artifact root is `/root/code/mlflow-artifacts/`.

The MLflow UI button at the top of the lab routes through the lab proxy, which reaches the server with a non-localhost host header and a different origin. Launch the server so it accepts any host header and any origin; otherwise the button returns a 403 or CORS error.

The server process persists in the background so it survives terminal closure.
Once the server is running, the Default experiment can be viewed from the MLflow UI button. The experiment is empty.

# Step By Step Solution

### 1. Create the backend and artifact directories.
MLflow needs two filesystem locations: a backend directory for its SQLite database (metadata store) and an artifact directory for logged models, plots, and datasets. Keep both under `/root/code/` so they stay visible in the VS Code file explorer.
```
mkdir -p /root/code/mlflow-backend /root/code/mlflow-artifacts
```

##### 2. Start the MLflow tracking server in the background.
Launch the server with the SQLite backend, the local artifact directory, and the flags required for the lab's browser proxy. The `--allowed-hosts` and `--cors-allowed-origins` flags let the `5000-port-*.kk-lab-dev.kodekloud.com` proxy URL reach the API — without them, the browser returns a CORS error even though `curl` against `localhost` succeeds. The trailing `&` backgrounds the process so it outlives the terminal session.
```
mlflow server \
  --backend-store-uri sqlite:////root/code/mlflow-backend/mlflow.db \
  --default-artifact-root /root/code/mlflow-artifacts \
  --host 0.0.0.0 \
  --port 5000 \
  --allowed-hosts '*' \
  --cors-allowed-origins '*' &
```

##### 3. Verify that the server is reachable from the controlplane.
Once the server is up, `curl` returns the MLflow homepage HTML:
```
curl -s http://localhost:5000/ | head -c 200
```
A successful response begins with `<!doctype html>` and references the MLflow favicon.

##### 4. Confirm the UI in the browser.
Open the **MLflow UI** button at the top of the lab. The `Default` experiment is listed in the left-hand panel. The experiment is empty.
