# Exercise 3: Create a GitHub Actions Workflow to Provision a Fabric Workspace

## Objective

In this exercise, you will:
- Clone the GitHub repository you created in Exercise 1
- Store service principal credentials as GitHub secrets
- Create a GitHub Actions workflow file that provisions a new Microsoft Fabric workspace
- Configure the workflow to automate workspace creation and permission assignment using Fabric CLI
- Test and validate the workflow in your own GitHub repository

**Prerequisites:**
- Completed Exercise 1: GitHub repository (`GH-Git-integration-source` forked into your account)
- GitHub account with access to the repository from Exercise 1
- Service principal details (CLIENT_ID, TENANT_ID, CLIENT_SECRET) — these will be provided for the lab
- [Git installed](https://git-scm.com/downloads) on your local machine
- [Fabric CLI installed](https://microsoft.github.io/fabric-cli/) (Python 3.8+ required)
- A code editor (VS Code recommended: https://code.visualstudio.com/)
- Basic familiarity with GitHub Actions and YAML syntax

---

## Part A — Clone Your Repository Locally

1. Open a terminal or PowerShell on your local machine.

2. Clone the repository you created in Exercise 1:

```bash
# Replace <YourUsername> with your actual GitHub username
git clone https://github.com/<YourUsername>/GH-Git-integration-source.git
cd GH-Git-integration-source
```

3. Verify the repository structure:
   - Confirm you have the `/workspace` folder
   - Check for any existing `.github/workflows` directory (if it doesn't exist, you will create it)

```bash
ls -la                    # macOS/Linux
dir                       # Windows PowerShell
```

---

## Part B — Add Service Principal Credentials as GitHub Secrets

To run the workflow securely without hardcoding credentials, you will store the service principal details as GitHub repository secrets.

### Step 1: Navigate to Repository Settings

1. Go to your GitHub repository: `https://github.com/<YourUsername>/GH-Git-integration-source`
2. Click the **Settings** tab (top-right area of the repository page)
3. In the left sidebar, expand **Secrets and variables** and select **Actions**

### Step 2: Create Secrets

You will create three secrets for the service principal credentials. Click **New repository secret** for each:

1. **Secret Name:** `FABRIC_CLIENT_ID`
   - **Value:** Paste the CLIENT_ID provided by the lab instructor

2. **Secret Name:** `FABRIC_CLIENT_SECRET`
   - **Value:** Paste the CLIENT_SECRET provided by the lab instructor

3. **Secret Name:** `FABRIC_TENANT_ID`
   - **Value:** Paste the TENANT_ID provided by the lab instructor

After creating all three secrets, you should see them listed in the **Repository secrets** section (values will be masked).

**Security Note:** GitHub secrets are encrypted and never exposed in workflow logs. For more information, see [GitHub's secrets documentation](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions).

---

## Part C — Create the GitHub Actions Workflow

### Step 1: Create the Workflow Directory

In your local repository, create the required directory structure if it doesn't exist:

```bash
# macOS/Linux
mkdir -p .github/workflows

# Windows PowerShell
New-Item -ItemType Directory -Path ".github\workflows" -Force
```

### Step 2: Create the Workflow File

Create a new file named `create-fabric-workspace.yml` in the `.github/workflows` directory:

**File location:** `.github/workflows/create-fabric-workspace.yml`

### Step 3: Define the Workflow

Copy the following workflow definition into `create-fabric-workspace.yml`:

```yaml
name: Create Fabric Workspace
on:
  workflow_dispatch:

jobs:
  create_workspace:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.10'
      
      - name: Install Fabric CLI
        run: |
          pip install fabric-cli
      
      - name: Fabric CLI Login with Service Principal
        run: |
          fabric login --client-id '${{ secrets.FABRIC_CLIENT_ID }}' \
                       --client-secret '${{ secrets.FABRIC_CLIENT_SECRET }}' \
                       --tenant-id '${{ secrets.FABRIC_TENANT_ID }}'
      
      - name: Create Fabric Workspace
        run: |
          fab workspace create 'WS-fabricUser-xx-test'
        env:
          FABRIC_USER_ID: ${{ secrets.FABRIC_CLIENT_ID }}
      
      - name: Set Workspace Permissions
        run: |
          fab acl set ws1.Workspace -I 7598817c-573e-41c2-8359-094c935e307f -R contributor
      
      - name: Verify Workspace Creation
        run: |
          fab workspace get 'WS-fabricUser-xx-test'
```

### Step 4: Customize the Workspace Name

In the workflow file, replace the placeholder in the **Create Fabric Workspace** step:

- Change `WS-fabricUser-xx-test` to your assigned number (e.g., `WS-fabricUser-01-test`)
- Also update the same naming convention in the **Verify Workspace Creation** step if needed

**Workflow Name Options:**
The workflow name is defined at the top of the file (`name: Create Fabric Workspace`). You can customize it with your user number:

Examples:
- `Create Fabric Workspace - FabricUser-01`
- `Provision Workspace - User 01`
- `Fabric CI/CD - Create Workspace - 01`

---

## Part D — Push Workflow to GitHub and Test

### Step 1: Stage and Commit the Workflow

In your local terminal, add the workflow file to Git:

```bash
# Add the workflow file
git add .github/workflows/create-fabric-workspace.yml

# Commit with a descriptive message
git commit -m "Add GitHub Actions workflow to provision Fabric workspace"
```

### Step 2: Push to GitHub

Push the changes to your GitHub repository:

```bash
# Push to the remote repository
git push origin main
```

Verify the push was successful by checking your GitHub repository page — you should see the workflow file listed in the `.github/workflows` directory.

### Step 3: Test the Workflow

1. Navigate to your GitHub repository: `https://github.com/<YourUsername>/GH-Git-integration-source`
2. Click the **Actions** tab (top navigation)
3. In the left sidebar, you should see **Create Fabric Workspace** (or your custom workflow name)
4. Click the workflow name
5. Click the **Run workflow** button (green button on the right)
6. A dialog will appear. Click **Run workflow** to confirm

### Step 4: Monitor Workflow Execution

1. Wait for the workflow to start (it may take a few seconds)
2. Click the workflow run to see detailed logs
3. Watch each step execute:
   - "Checkout code" — clones the repository
   - "Set up Python" — installs Python 3.10
   - "Install Fabric CLI" — downloads and installs fabric-cli
   - "Fabric CLI Login with Service Principal" — authenticates using your secrets
   - "Create Fabric Workspace" — provisions the new workspace
   - "Set Workspace Permissions" — assigns the specified permission to the workspace
   - "Verify Workspace Creation" — confirms the workspace exists

4. If all steps complete with a green checkmark, the workflow succeeded ✓

### Step 5: Verify Workspace in Fabric

1. Go to [Microsoft Fabric](https://app.powerbi.com) and sign in
2. Navigate to **Workspaces** in the left navigation pane
3. Look for your newly created workspace: `WS-fabricUser-xx-test` (where `xx` is your number)
4. Confirm the workspace appears in your list and is accessible

---

## Part E — Troubleshooting and Common Issues

**Workflow fails with "authentication failed"**
- Verify that the three secrets (`FABRIC_CLIENT_ID`, `FABRIC_CLIENT_SECRET`, `FABRIC_TENANT_ID`) are correctly entered in GitHub Settings → Secrets and variables → Actions
- Ensure the service principal credentials provided by the instructor are valid and not expired
- Check that the service principal has the required permissions (typically Contributor role) on the Fabric capacity or subscription

**"Fabric CLI not found" error**
- The step "Install Fabric CLI" may take longer on first run. Ensure Python 3.10 is properly installed
- If using a self-hosted runner, verify Python 3.8+ is available on the runner

**Workspace creation fails**
- Ensure the workspace name `WS-fabricUser-xx-test` is unique (no duplicate workspaces with the same name)
- Verify the service principal has permissions to create workspaces in the Fabric capacity
- Check Fabric workspace limits — you may have reached your quota

**Permission assignment fails**
- The object ID `7598817c-573e-41c2-8359-094c935e307f` is a placeholder. Contact the lab instructor if you need to assign permissions to a specific user or group
- Verify the workspace reference `ws1.Workspace` matches your workspace naming convention

**Workflow never starts**
- Ensure you pushed the workflow file to the repository (check the Actions tab — the workflow should be listed)
- GitHub may take a few seconds to recognize a new workflow file
- Refresh the GitHub Actions page if needed

For more help, see:
- [GitHub Actions documentation](https://docs.github.com/en/actions)
- [Fabric CLI documentation](https://microsoft.github.io/fabric-cli/)
- [Fabric workspace creation guide](https://learn.microsoft.com/en-us/fabric/get-started/create-workspaces)

---

## Part F — Useful Links and References

**Workflow and CI/CD:**
- [GitHub Actions documentation](https://docs.github.com/en/actions)
- [GitHub Actions: Using secrets](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions)
- [GitHub Actions: workflow_dispatch trigger](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#workflow_dispatch)
- [YAML syntax for GitHub Actions](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)

**Fabric and CLI:**
- [Fabric CLI documentation](https://microsoft.github.io/fabric-cli/)
- [Fabric CLI sign-in (service principal)](https://microsoft.github.io/fabric-cli/#sign-in)
- [Fabric workspace creation](https://learn.microsoft.com/en-us/fabric/get-started/create-workspaces)
- [Fabric ACL (Access Control List) management](https://microsoft.github.io/fabric-cli/#workspaces-management)
- [Fabric authentication and authorization](https://learn.microsoft.com/en-us/fabric/admin/role-based-access-control)

**Service Principal and Security:**
- [Azure service principal creation](https://learn.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)
- [Azure RBAC for Fabric](https://learn.microsoft.com/en-us/fabric/admin/role-based-access-control)
- [Git and GitHub basics](https://docs.github.com/en/get-started)

---

## Lab Checklist

Use this checklist to verify you have completed all steps:

- [ ] Cloned the GitHub repository from Exercise 1 locally
- [ ] Created three GitHub secrets (FABRIC_CLIENT_ID, FABRIC_CLIENT_SECRET, FABRIC_TENANT_ID)
- [ ] Created the `.github/workflows` directory structure
- [ ] Created the `create-fabric-workspace.yml` workflow file with correct content
- [ ] Updated the workspace name from the placeholder to your assigned number (e.g., WS-fabricUser-01-test)
- [ ] Committed and pushed the workflow file to GitHub
- [ ] Executed the workflow via the Actions tab with "Run workflow"
- [ ] Monitored the workflow execution and confirmed all steps completed successfully
- [ ] Verified the new workspace appears in Microsoft Fabric
- [ ] (Optional) Reviewed the workflow logs for any warnings or errors
- [ ] (Optional) Tested workflow re-runs to confirm reproducibility
