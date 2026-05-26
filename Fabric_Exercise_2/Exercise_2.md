# Exercise 2: Local clone of fabric-cicd-local and running three CI/CD methods

## Objective

In this exercise you will:
- Clone the upstream repository https://github.com/DataOpsSupport/fabric-cicd-local locally
- Create a new Microsoft Fabric workspace named `fabric-cicd-local-FabricUser-xx` (replace `xx` with your allocated number)
- Create a GitHub repository to hold your lab code (named `fabric-cicd-local-FabricUser-xx`) and push the code there
- Use provided service principal credentials to authenticate against Azure
- Execute three CI/CD approaches (local script, GitHub Actions, and Azure Pipelines/other CI) as described below

**Prerequisites**
- A [GitHub account](https://github.com/signup)
- [Git installed](https://git-scm.com/downloads)
- [Azure CLI installed](https://learn.microsoft.com/cli/azure/install-azure-cli) and basic familiarity with it
- Service principal details (CLIENT_ID, TENANT_ID, CLIENT_SECRET) — these will be provided for the lab
- Optional: [Azure DevOps account](https://azure.microsoft.com/en-us/services/devops/) if you will test Azure Pipelines
- A code editor (VS Code recommended: https://code.visualstudio.com/)

---

## Important note about the upstream repository
I attempted to fetch the README from `https://github.com/DataOpsSupport/fabric-cicd-local` but it was not accessible (the repo might be private). The steps below describe the general, fully-detailed approach you can follow. If your upstream README contains different commands or scripts, follow those exact commands instead — the steps below are meant to be comprehensive and adapt easily.

---

## Part A — Create your Fabric workspace and GitHub repository

1. Create a new Microsoft Fabric workspace (name it exactly):
  - Navigate to [https://app.powerbi.com](https://app.powerbi.com) and sign in
  - In the left navigation, click **Workspaces** → **New workspace**
  - **Name:** enter `fabric-cicd-local-FabricUser-xx` (replace `xx` with your allocated number)
  - **Capacity:** select an available Fabric capacity (trial or assigned)
  - **Description:** optional, e.g., `Fabric CI/CD lab workspace`
  - Click **Save** and verify the workspace appears in your workspace list

  For more, see: https://learn.microsoft.com/en-us/fabric/get-started/create-workspaces

2. (Optional) Create a new GitHub repository if you want to push your local copy to GitHub:
  - Recommended repository name: `fabric-cicd-local-FabricUser-xx` (only needed for remote hosting)
  - Description: `Local copy of fabric-cicd-local for Fabric CI/CD lab`
  - Visibility: Private or Public as instructed by the lab
  - Note: you do NOT need to rename or move your local clone; pushing to GitHub is optional and can be done later if required

3. Clone the upstream repository locally:

```bash
# clone upstream (may require access)
git clone https://github.com/DataOpsSupport/fabric-cicd-local.git
cd fabric-cicd-local
```

4. If the upstream repo is private or inaccessible, ask the instructor to provide a zip or alternate URL; alternatively initialize a local folder and copy provided starter files into it (pushing to GitHub is optional per lab instructions):

```bash
# if upstream inaccessible
mkdir fabric-cicd-local
cd fabric-cicd-local
git init
# copy starter files provided by instructor into this folder
git add .
git commit -m "Initial commit - lab starter"
# Optionally, add your GitHub repository as a remote and push when instructed by the lab
# git remote add origin https://github.com/<YourUsername>/fabric-cicd-local-FabricUser-xx.git
# git push -u origin main
```

---

## Part B — Configure Azure authentication (service principal)

You will be provided with a service principal (SP) for the lab. The SP will have an Azure AD application and credentials (client id, tenant id, client secret) and RBAC permissions to deploy resources.

1. Save the provided SP values securely. Example variables you will receive:
   - AZURE_CLIENT_ID
   - AZURE_TENANT_ID
   - AZURE_CLIENT_SECRET

2. Locally (example PowerShell) set environment variables and log in using the SP:

```powershell
$env:AZURE_CLIENT_ID = "<CLIENT_ID>"
$env:AZURE_TENANT_ID = "<TENANT_ID>"
$env:AZURE_CLIENT_SECRET = "<CLIENT_SECRET>"

az login --service-principal -u $env:AZURE_CLIENT_ID -p $env:AZURE_CLIENT_SECRET --tenant $env:AZURE_TENANT_ID --allow-no-subscriptions
```

Linux/macOS bash example:

```bash
export AZURE_CLIENT_ID="<CLIENT_ID>"
export AZURE_TENANT_ID="<TENANT_ID>"
export AZURE_CLIENT_SECRET="<CLIENT_SECRET>"

az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET --tenant $AZURE_TENANT_ID --allow-no-subscriptions
```

3. Verify access:

```bash
az account show
az group list --output table
```

If those commands succeed, the SP credentials are valid and have required permissions.

---

## Part C — Run the three local auth scripts

This exercise focuses on running the three local authentication examples provided in the repository:

1. `1-auth_local_hard_coded.py` — credentials are hard-coded in the script (use only for demo; not secure)
2. `2-auth_local_variables.py` — reads credentials from environment variables
3. `3-auth_local_config.py` — reads credentials from a configuration file (recommended pattern)

Before running the scripts:

- Ensure Python 3.8+ is installed: https://www.python.org/downloads/
- Create and activate a virtual environment (recommended):

```bash
python -m venv .venv
source .venv/bin/activate    # macOS/Linux
.\.venv\Scripts\Activate   # Windows PowerShell
pip install --upgrade pip
```

- Install requirements if `requirements.txt` exists:

```bash
pip install -r requirements.txt
```

Run each script locally and observe output/logs. IMPORTANT: do not set environment variables or other credentials unless the upstream README or the script's internal comments instruct you to — each script contains its own usage instructions and expected inputs.

1. Open each Python script in an editor and read the top-of-file comments — follow the exact instructions present in the script (the scripts may request a config file, environment variables, or inline credentials).

2. Then run:

```bash
python 1-auth_local_hard_coded.py
python 2-auth_local_variables.py
python 3-auth_local_config.py
```

Notes on the scripts:
- `1-auth_local_hard_coded.py` — open the file to see where credentials are set; replace placeholder values if the instructor provided hard-coded credentials (not recommended). This is useful to demonstrate how the SDK authenticates but avoid committing such files.
- `2-auth_local_variables.py` — demonstrates reading from environment variables. Ensure you exported the AZURE_* variables before running.
- `3-auth_local_config.py` — reads a local config file (e.g., `auth_config.json` or `config.yaml`). Create the config file from the provided example or template and include the SP values there. Example `auth_config.json`:

```json
{
  "client_id": "<CLIENT_ID>",
  "tenant_id": "<TENANT_ID>",
  "client_secret": "<CLIENT_SECRET>"
}
```

Security reminder: never commit secrets to source control. Use local files ignored by git (add to `.gitignore`) or environment variables.

Verifying authentication:
- Successful authentication in the scripts should allow calling the Azure REST/SDK endpoint used by the repo (e.g., list resource groups). You can also manually verify with the Azure CLI:

```bash
az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET --tenant $AZURE_TENANT_ID --allow-no-subscriptions
az account show
```

Troubleshooting:
- If a script raises authentication errors, check that the SP credentials are correct and the SP has sufficient RBAC roles (Contributor or specific permissions) on the target subscription/resource group. See: https://learn.microsoft.com/azure/role-based-access-control/overview
- If Python dependencies fail, confirm `requirements.txt` and Python version.
- If config file is not found, ensure the script's expected filename/path matches your file.

References for local execution:
- Python virtual environments: https://docs.python.org/3/library/venv.html
- Azure CLI and service principal login: https://learn.microsoft.com/cli/azure/authenticate-azure-cli
- Azure service principal creation and RBAC: https://learn.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal
- Secrets best practices: https://learn.microsoft.com/azure/security/develop/secret-management

---

## Part D — Verification, cleanup and tips

Verification
- Check deployed resources in the Azure Portal or with `az` commands.
- Inspect script and CLI output for failures and stack traces.

Cleanup
- Use any provided `destroy`/`teardown` scripts in the repository.
- Or delete resource groups used by the lab:

```bash
az group delete --name <resource-group-name> --yes --no-wait
```

Security & best practices
- Do not commit service principal secrets to Git. Use secure secret storage (environment variables, OS-level secret stores, or a password manager).
- Use least-privilege RBAC for the service principal.
- Rotate the service principal secret if it is suspected compromised.

Troubleshooting
- Authentication errors: confirm SP values and that SP has Contributor (or required) role on the subscription/resource group.
- Network errors: ensure your machine can reach Azure endpoints (or that any runners/agents have network access).
- Script errors: run scripts locally to reproduce and debug before automating.

---

## Useful links
- Upstream repo (attempted): https://github.com/DataOpsSupport/fabric-cicd-local
- Git: https://git-scm.com/
- GitHub: https://docs.github.com/
- Azure CLI: https://learn.microsoft.com/cli/azure/
- Azure service principal: https://learn.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal
- Python venv: https://docs.python.org/3/library/venv.html
- Azure role-based access control (RBAC): https://learn.microsoft.com/azure/role-based-access-control/overview

---

## Lab checklist
- [ ] Created Fabric workspace `fabric-cicd-local-FabricUser-xx`
- [ ] Created GitHub repository `fabric-cicd-local-FabricUser-xx`
- [ ] Set and validated service principal credentials
- [ ] Successfully ran the auth examples locally:
  - `1-auth_local_hard_coded.py`
  - `2-auth_local_variables.py`
  - `3-auth_local_config.py`
- [ ] Verified resources (using `az` or the scripts) and cleaned up
