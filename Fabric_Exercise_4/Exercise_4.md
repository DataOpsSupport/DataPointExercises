# Exercise 4: Deploy Fabric CICD to the Workspace Created in Exercise 3

## Objective

In this exercise, you will:
- Use the same GitHub repository from Exercise 3
- Update the provided Fabric CICD parameter file with the workspace ID created in Exercise 3
- Add or verify service principal secrets in GitHub Actions
- Create a GitHub Actions workflow to deploy to the target workspace using Fabric CICD
- Test the workflow in your GitHub repository

**Prerequisites:**
- Completed Exercise 3 and the GitHub repository used there
- A GitHub repository containing the Fabric CICD parameter file and deployment scripts
- Service principal credentials provided during the exercise:
  - CLIENT_ID
  - CLIENT_SECRET
  - TENANT_ID
- [Git installed](https://git-scm.com/downloads)
- [Fabric CLI documentation](https://microsoft.github.io/fabric-cli/)
- A code editor such as VS Code

---

## Part A — Use the Existing Repository from Exercise 3

1. Open a terminal or PowerShell window in the repository you already cloned for Exercise 3.

2. Confirm the repository contains the Fabric CICD parameter file and scripts.
   - Look for files such as `parameters.json`, `fabric-cicd-parameters.json`, or a provided parameter file name
   - Look for the authentication script `auth_spn_secret_AzDo.py`
   - Verify there is an existing `.github/workflows` directory or create one if needed

```bash
ls -la .github/workflows   # macOS/Linux
dir .github\workflows        # Windows PowerShell
```

---

## Part B — Update the Workspace ID in the Parameter File

The repository should already include the Fabric CICD parameter file. You must update the workspace ID in that file so the deployment targets the workspace created in Exercise 3.

### Step 1: Find your workspace ID

1. In Microsoft Fabric, open the workspace you created in Exercise 3.
2. Find the workspace ID in the workspace details or use the Fabric CLI to list workspaces.
3. Capture the workspace ID value for the next step.

### Step 2: Edit the parameter file

1. Open the provided parameter file in your editor.
2. Locate the property named `workspaceId`.
3. Replace its current value with the workspace ID from Exercise 3.

Example JSON structure:

```json
{
  "workspaceId": "WS_FABRIC_USER_01_TEST_ID_PLACEHOLDER",
  "otherParameter": "value"
}
```

After editing:

```json
{
  "workspaceId": "your-exercise-3-workspace-id"
}
```

> If the parameter file is not a simple JSON file, update the same `workspaceId` property in the file format used by your repository.

### Step 3: Save the file

Save the parameter file and verify the change was persisted.

---

## Part C — Store Service Principal Credentials as GitHub Secrets

The workflow will use service principal credentials via GitHub Actions secrets. If you already created these secrets in Exercise 3, verify they are present.

### Required secrets

Create or verify these secrets in your repository settings under **Settings → Secrets and variables → Actions**:

- `FABRIC_CLIENT_ID`
- `FABRIC_CLIENT_SECRET`
- `FABRIC_TENANT_ID`

If you prefer to keep the workspace ID outside the parameter file, also create:

- `FABRIC_WORKSPACE_ID`

### Notes

- These credentials are provided during the exercise by the lab instructor.
- Secrets are masked in logs and encrypted at rest.
- Do not commit secrets directly into GitHub or source control.

---

## Part D — Create the GitHub Actions Workflow

### Step 1: Create the workflow file

Create a new workflow file at `.github/workflows/fabric-cicd-deploy.yml`.

### Step 2: Add the workflow definition

Use the following workflow definition as your starting point. It is based on the referenced workflow at `https://github.com/chantifiedlens/GH-fabric-cicd-demo/blob/main/.github/workflows/fabric-cicd-demo-variables.yml` and the Python auth file at `https://github.com/chantifiedlens/GH-fabric-cicd-demo/blob/main/auth_spn_secret_AzDo.py`.

```yaml
name: Fabric CICD Deploy

on:
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.10'

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          if [ -f requirements.txt ]; then pip install -r requirements.txt; fi

      - name: Authenticate with service principal
        env:
          AZURE_CLIENT_ID: ${{ secrets.FABRIC_CLIENT_ID }}
          AZURE_CLIENT_SECRET: ${{ secrets.FABRIC_CLIENT_SECRET }}
          AZURE_TENANT_ID: ${{ secrets.FABRIC_TENANT_ID }}
        run: |
          python auth_spn_secret_AzDo.py

      - name: Deploy Fabric CICD to workspace
        run: |
          # Update the path to the parameter file if needed
          python auth_spn_secret_AzDo.py
          # Add your repository-specific deploy command below,
          # for example: python deploy.py --parameters parameters.json
          echo "Replace this with the repository's Fabric CICD deploy command"
```

```

### Step 3: Customize the workflow

1. If your repository uses a different Python version, update `python-version` accordingly.
2. If the parameter file is not named `parameters.json`, update the workflow comments and deploy command to reference the actual filename.
3. Replace the placeholder deploy command with the actual deployment command from your repository.
4. If you want, rename the workflow in the top line `name: Fabric CICD Deploy` to include your user number.

### Recommended workflow name examples

- `Fabric CICD Deploy - FabricUser-01`
- `Deploy to Fabric Workspace - 01`
- `fabric-cicd workspace deploy`

---

## Part E — Optionally Use `FABRIC_WORKSPACE_ID` as a Secret

If you prefer not to edit the parameter file directly in source control, you can store the workspace ID as a secret and update the parameter file inside the workflow.

Add `FABRIC_WORKSPACE_ID` as a new repository secret, then include this step before deployment:

```yaml
      - name: Update workspaceId in parameter file
        env:
          FABRIC_WORKSPACE_ID: ${{ secrets.FABRIC_WORKSPACE_ID }}
        run: |
          python - <<'PY'
          import json, pathlib
          param_file = pathlib.Path('parameters.json')
          data = json.loads(param_file.read_text())
          data['workspaceId'] = '${{ env.FABRIC_WORKSPACE_ID }}'
          param_file.write_text(json.dumps(data, indent=2))
          PY
```

Adjust `parameters.json` to the actual parameter filename in your repository.

---

## Part F — Commit and Push the Workflow

1. Add and commit the workflow file and any parameter-file changes:

```bash
git add .github/workflows/fabric-cicd-deploy.yml
# If you modified the parameter file, stage that too
# git add parameters.json
git commit -m "Add GitHub Actions workflow to deploy Fabric CICD to Exercise 3 workspace"
```

2. Push the changes to GitHub:

```bash
git push origin main
```

3. Verify the workflow file appears in your repository under `.github/workflows`.

---

## Part G — Run and Validate the Workflow

1. Open your repository on GitHub.
2. Click the **Actions** tab.
3. Select the workflow named `Fabric CICD Deploy` or your custom name.
4. Click **Run workflow**.
5. Confirm the workflow run begins.

### Monitor the workflow

- Check the log for the Python setup and dependency installation steps
- Verify the authentication step completes successfully
- Confirm the deploy step runs with the correct parameter file
- Look for any errors related to `workspaceId` or Fabric CICD deployment

### Verify deployment

- Open Microsoft Fabric and confirm the workspace created in Exercise 3 still exists
- Validate that the CICD deployment targeted that workspace and completed without errors
- If the deployment created artifacts, check the Fabric workspace or repository output to confirm success

---

## Part H — Troubleshooting

**Authentication fails**
- Confirm `FABRIC_CLIENT_ID`, `FABRIC_CLIENT_SECRET`, and `FABRIC_TENANT_ID` are present and correct in GitHub secrets
- Make sure the SPN credentials are valid for the Fabric environment
- Review `auth_spn_secret_AzDo.py` to verify it reads credentials from environment variables

**Wrong workspace ID**
- Verify the `workspaceId` in the parameter file matches the workspace created in Exercise 3
- If you used `FABRIC_WORKSPACE_ID`, confirm the secret value is correct

**Workflow cannot find the parameter file**
- Confirm the parameter file path in the workflow matches the repo file name
- Use `ls` or `dir` locally to validate the filename

**Deployment command is missing or incorrect**
- Inspect the repository for the actual deploy command or script name
- If the repo contains `deploy.py`, `run_deploy.sh`, or a Fabric CLI deployment command, use that instead of the placeholder command

---

## Useful Links

- GitHub Actions `workflow_dispatch`: https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#workflow_dispatch
- GitHub Actions secrets: https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions
- Fabric CLI docs: https://microsoft.github.io/fabric-cli/
- Fabric CLI sign-in (service principal): https://microsoft.github.io/fabric-cli/#sign-in
- Example workflow: https://github.com/chantifiedlens/GH-fabric-cicd-demo/blob/main/.github/workflows/fabric-cicd-demo-variables.yml
- Example auth script: https://github.com/chantifiedlens/GH-fabric-cicd-demo/blob/main/auth_spn_secret_AzDo.py
- Fabric workspace creation: https://learn.microsoft.com/en-us/fabric/get-started/create-workspaces

---

## Lab Checklist

- [ ] Cloned the repository from Exercise 3
- [ ] Confirmed the repository contains the provided Fabric CICD parameter file
- [ ] Updated `workspaceId` in the parameter file to the Exercise 3 workspace ID
- [ ] Verified or added the required GitHub secrets
- [ ] Created `.github/workflows/fabric-cicd-deploy.yml`
- [ ] Customized the workflow to use the repository's deploy command
- [ ] Committed and pushed the workflow and parameter updates
- [ ] Ran the workflow using `workflow_dispatch`
- [ ] Verified the deployment targeted the correct workspace
- [ ] Checked workflow logs for success and resolved any errors
