# Exercise 5: Add GitHub Deployment Environments with Required Approvals

## Objective

In this exercise, you will:
- Make your GitHub repository public (required for deployment approvals on free plans)
- Create a deployment environment in GitHub Actions
- Configure required reviewers to approve deployments
- Update the workflow from Exercise 4 to use the environment
- Test the environment approval workflow
- Convert the repository back to private

**Prerequisites:**
- Completed Exercise 4 and have a GitHub Actions workflow deployed
- Repository owner or admin access to the GitHub repository
- At least one collaborator or organization member who can review deployments

**Important Note:**
Deployment environments with required reviewers are available in public repositories for all GitHub plans (Free, Pro, Team). Private repositories require GitHub Pro, GitHub Team, or GitHub Enterprise.

---

## Part A — Make the Repository Public

Environments with deployment protection rules require the repository to be public on GitHub Free plans. If you are on GitHub Pro, GitHub Team, or GitHub Enterprise, you can use private repositories, but the steps below still apply.

### Step 1: Navigate to Repository Settings

1. Go to your GitHub repository in a web browser.
2. Click the **Settings** tab at the top of the repository.
3. If you don't see the Settings tab, click the **...** (More) dropdown menu and select **Settings**.

### Step 2: Change Repository Visibility

1. In the left sidebar, click **General** (if not already selected).
2. Scroll down to the **Danger Zone** section at the bottom of the page.
3. Click the **Change visibility** button.

![Change visibility button location](https://docs.github.com/assets/cb-28260/mw-1440/images/help/repository/repo-settings-danger-zone.webp)

### Step 3: Select Public

1. A dialog will appear asking you to confirm the change.
2. Select **Public** from the options.
3. Read the warning message carefully. It will indicate:
   - The repository will be visible to everyone on GitHub
   - Anyone can fork the repository
   - Search engines may index the repository
4. Type the repository name to confirm.
5. Click **I understand, change repository visibility** (or similar button text).

### Step 4: Verify the Change

1. Return to the repository main page.
2. Confirm that the repository is now labeled as **Public** (you should see this near the repository name at the top).

**Reference:** [GitHub Docs - Setting repository visibility](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/setting-repository-visibility)

---

## Part B — Create a Deployment Environment

### Step 1: Navigate to Environments Settings

1. In your GitHub repository, go to **Settings**.
2. In the left sidebar, scroll down and click **Environments**.
3. You should see a section labeled **Environments** with an option to create a new environment.

### Step 2: Create a New Environment

1. Click the **New environment** button.
2. Enter a name for the environment. For example:
   - `test-workspace` (matches the deployment target from Exercise 4)
   - `staging`
   - `approval-test`
   
   Environment names are not case sensitive and must be unique within the repository.

3. Click **Configure environment**.

### Step 3: Configure Required Reviewers

1. On the environment configuration page, scroll down to find **Deployment protection rules**.
2. Check the box for **Required reviewers**.
3. In the **Required reviewers** field, start typing a GitHub username or team name.
4. You can select up to 6 people or teams, for now only select yourself. Only **one** of the required reviewers needs to approve the job for it to proceed.

**Example:** You could add:
   - Your own username
   - A team member's username
   - An organization team (if part of an organization)

### Step 4: Configure Additional Protection Rules (Optional)

You can also configure other protection rules for this environment:

#### Wait Timer
- Check **Wait timer** to add a delay before workflow jobs can proceed.
- Enter the number of minutes to wait (e.g., 5 minutes).
- This is useful for staged deployments or notifications.

#### Deployment Branches
- Scroll to **Deployment branches and tags**.
- Choose which branches can deploy to this environment:
  - **All branches** - Any branch can trigger deployments (useful for testing)
  - **Protected branches** - Only protected branches can deploy
  - **Selected branches and tags** - Specify exact branches or tag patterns

For this exercise, you can leave it as **All branches** or select **main** if you want to be more restrictive.

### Step 5: Save the Environment Configuration

1. Scroll to the top or bottom of the page and click **Save protection rules**.
2. You should see a confirmation that the environment was created or updated.

**Reference:** [GitHub Docs - Managing Environments for Deployment](https://docs.github.com/en/actions/how-tos/deploy/configure-and-manage-deployments/manage-environments#creating-an-environment)

---

## Part C — Update the Workflow to Use the Environment

Now you need to update the workflow from Exercise 4 to reference the new environment so that deployments require approval.

### Step 1: Edit the Workflow File

1. In your repository, navigate to `.github/workflows/fabric-cicd-deploy.yml` (or the name you used in Exercise 4).
2. Click the edit (pencil) icon to edit the file in the GitHub web interface, or edit it locally and push the changes.

### Step 2: Add Environment to the Deploy Job

In the `jobs:` section, add an `environment:` field to the `deploy` job. Your workflow should look similar to this:

```yaml
name: Fabric CICD Deploy

on:
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: test-workspace    # <-- Add this line with your environment name

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
          python auth_spn_secret_AzDo.py
          echo "Replace this with the repository's Fabric CICD deploy command"
```

The key addition is the `environment: test-workspace` line (or whatever you named your environment).

### Step 3: Commit the Changes

1. If editing in the GitHub web interface, scroll down and click **Commit changes**.
2. Add a commit message such as: `Add deployment environment with required approvals`
3. Choose to commit to the main branch (or your working branch) and click **Commit changes**.

If editing locally, commit and push the changes:

```bash
git add .github/workflows/fabric-cicd-deploy.yml
git commit -m "Add deployment environment with required approvals"
git push origin main
```

---

## Part D — Test the Environment Approval Workflow

### Step 1: Trigger the Workflow

1. In your GitHub repository, click the **Actions** tab.
2. On the left, select the workflow **Fabric CICD Deploy** (or your workflow name).
3. Click the **Run workflow** button.
4. Select the branch to run from (typically `main`) and click **Run workflow**.

### Step 2: Observe the Workflow Run

1. The workflow should start running. The initial steps (checkout, setup, etc.) should proceed normally.
2. When the job reaches the **deploy** job, it should **pause** and wait for approval.
3. You should see a status like:
   - `Waiting for approval`
   - `Deployment protection rules require approval`
   - Or similar status message

### Step 3: Approve the Deployment

1. On the workflow run page, you should see a section for **Deployment approvals** or **Required approvals**.
2. Click **Review deployments** or the approval button.
3. Select the environment (e.g., `test-workspace`).
4. You can leave a comment explaining the approval (optional).
5. Click **Approve and deploy**.

### Step 4: Verify Deployment Proceeds

1. After approval, the workflow job should continue executing.
2. The deployment steps should run normally.
3. When complete, you should see a green checkmark and a status like `Completed` or `Success`.

### Step 5: Check the Deployment Log

1. Click on the workflow run name to see the full logs.
2. Verify that all steps executed successfully.
3. Check the **Deployments** tab in your repository (if available) to see the deployment record.

**Reference:** [GitHub Docs - Deploying with GitHub Actions](https://docs.github.com/en/actions/concepts/use-cases/deploying-with-github-actions#using-environments)

---

## Part E — Convert Repository Back to Private (If Needed)

If you want to make the repository private again after testing, follow these steps. **Note:** If you are on GitHub Free, private repositories cannot use deployment environments with required reviewers. If you are on GitHub Pro or higher, you can proceed.

### Step 1: Navigate to Repository Settings

1. Go to your GitHub repository.
2. Click **Settings**.
3. In the left sidebar, click **General**.

### Step 2: Change Visibility Back to Private

1. Scroll down to the **Danger Zone** section.
2. Click **Change visibility**.
3. Select **Private**.
4. Read the warning message. It will indicate:
   - The repository will no longer be visible to the public
   - Existing environments and protection rules may be affected (depending on your plan)
   - Some features may become unavailable
5. Type the repository name to confirm.
6. Click **I understand, change repository visibility**.

### Step 3: Verify the Change

1. Return to the repository main page.
2. Confirm that the repository is now labeled as **Private**.

### Step 4: Test Deployment Approvals After Converting to Private (Optional)

**Important:** 
- On **GitHub Free**, private repositories cannot use deployment environments with required reviewers. The environment and protection rules will be ignored.
- On **GitHub Pro, GitHub Team, or GitHub Enterprise**, deployment environments with required reviewers remain available in private repositories.

To verify the environment is still active (if on a paid plan):
1. Return to **Settings → Environments**.
2. Confirm the environment is still listed.
3. Optionally trigger the workflow again to verify approvals still work.

**Reference:** [GitHub Docs - Setting repository visibility](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/setting-repository-visibility)

---

## Part F — Key Points and Best Practices

### Repository Visibility Requirements

- **GitHub Free Plan:**
  - Deployment environments with required reviewers: **Public repositories only**
  - Private repositories: Environments and protection rules are available but will be ignored
  - If you convert a public repo to private, protection rules become inactive
  - If you convert it back to public, rules become active again

- **GitHub Pro, Team, Enterprise:**
  - Deployment environments with required reviewers: **Available for both public and private repositories**

### Required Reviewers Best Practices

1. **Avoid Self-Approval:** Always enable "Prevent self-review" to prevent users from approving their own deployments.

2. **Team-Based Reviews:** For organization repositories, assign entire teams as reviewers rather than individuals for better coverage.

3. **Multiple Reviewers:** While only one approver is required to proceed, configuring multiple reviewers ensures better oversight.

4. **Meaningful Reviews:** Reviewers should check:
   - The deployment parameters
   - The workspace/environment being targeted
   - Any recent code changes that might affect the deployment

### Environment Secrets vs. Repository Secrets

- **Environment secrets:** Only available to jobs that use that environment. Can override repository secrets.
- **Repository secrets:** Available to all workflows in the repository.

For sensitive credentials that should only be used with specific environments, store them as environment secrets instead of repository secrets.

### Deployment Branches Restrictions

Configure deployment branch restrictions to ensure only specific branches (typically `main` or `master`) can deploy to production or critical environments:

1. Go to **Settings → Environments → [Your Environment]**
2. Under **Deployment branches and tags**, select **Selected branches and tags**
3. Add a rule like `main` or `release/*`
4. Click **Add rule**

This prevents accidental deployments from feature branches.

### Additional Deployment Protection Rules

GitHub also offers additional protection rules:

- **Wait Timer:** Add a delay before deployments proceed (useful for notifications or staged rollouts)
- **Custom Deployment Protection Rules:** Via GitHub Apps for custom workflows
- **Bypass Option:** "Allow administrators to bypass configured protection rules" (disable for stricter security)

Reference: [GitHub Docs - Deployments and Environments](https://docs.github.com/en/actions/reference/deployments-and-environments)

---

## Troubleshooting

### Problem: Workflow Does Not Wait for Approval

**Solutions:**
1. Verify the environment name in the workflow matches exactly (case-insensitive, but names must match)
2. Check that the environment exists in **Settings → Environments**
3. Confirm that required reviewers are configured in the environment settings
4. Verify the repository is public (if on GitHub Free)
5. Check that your GitHub plan supports deployment environments for your repository type

### Problem: Cannot Find "Review Deployments" Button

**Solutions:**
1. The button appears only on the workflow run page when a job is waiting for approval
2. Ensure you have the required permissions (reviewer must be the triggering user or a designated reviewer)
3. Check that you are assigned as a required reviewer in the environment settings

### Problem: Deployment Approvals Work in Public, But Not After Converting to Private

**Solution:**
This is expected on GitHub Free plans. To use private repositories with deployment approvals, upgrade to GitHub Pro, GitHub Team, or GitHub Enterprise.

### Problem: Changes to Environment Settings Are Not Taking Effect

**Solution:**
1. Refresh your browser page
2. Wait a few seconds for GitHub to sync the changes (usually instant)
3. Try triggering a new workflow run after confirming settings are saved

---

## Summary

In this exercise, you have:

1. ✅ Made your GitHub repository public (required for GitHub Free with deployment approvals)
2. ✅ Created a deployment environment named `test-workspace`
3. ✅ Configured required reviewers for the environment
4. ✅ Updated your Exercise 4 workflow to use the environment
5. ✅ Tested the deployment approval workflow
6. ✅ Optionally converted the repository back to private
7. ✅ Learned best practices for secure deployments

Your Fabric CICD deployment workflow now requires manual approval before deploying to the test workspace, adding an important layer of control to your deployment pipeline.

---

## Helpful Links

- [GitHub Actions: Deploying with Environments](https://docs.github.com/en/actions/concepts/use-cases/deploying-with-github-actions#using-environments)
- [Managing Environments for Deployment](https://docs.github.com/en/actions/how-tos/deploy/configure-and-manage-deployments/manage-environments)
- [Deployments and Environments Reference](https://docs.github.com/en/actions/reference/deployments-and-environments)
- [Setting Repository Visibility](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/setting-repository-visibility)
- [GitHub Plans Comparison](https://github.com/pricing)
- [GitHub Actions Security Best Practices](https://docs.github.com/en/actions/security-guides)

---

## Next Steps

After completing this exercise:

1. **Test with Multiple Reviewers:** Add a team or multiple people as required reviewers and test the workflow with different approvers.
2. **Add Wait Timer:** Configure a wait timer for additional delay before deployments proceed.
3. **Restrict Deployment Branches:** Limit deployments to the main branch only.
4. **Create Additional Environments:** Create staging and production environments with different approval requirements.
5. **Explore Environment Secrets:** Store sensitive credentials as environment-specific secrets.
6. **Monitor Deployments:** Use the Deployments tab in your repository to track all deployment history and approvals.
