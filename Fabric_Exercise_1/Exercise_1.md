# Exercise 2: Connecting Microsoft Fabric to GitHub with Git Integration

## Objective
In this exercise, you will configure Microsoft Fabric Git integration to connect a Fabric workspace to a GitHub repository. You will learn how to import a repository, create authentication credentials, set up a Fabric workspace, and enable version control for your Fabric items.

**Prerequisites:**
- A [GitHub account](https://github.com/signup) (free or paid)
- Access to [Microsoft Fabric](https://www.microsoft.com/en-us/microsoft-fabric/business-analytics) with appropriate capacity
- A [Microsoft 365 account](https://www.microsoft.com/en-us/microsoft-365) or a work/school account
- An allocated user number (xx)
- Administrator or contributor permissions in your GitHub account
- Administrator or contributor permissions in the Fabric workspace you will create
- A web browser (Edge, Chrome, Firefox, or Safari) with cookies and pop-ups enabled for Power BI/Fabric
- Basic knowledge of Git concepts (branches, repositories, commits)

---

## Part 1: Import GitHub Repository to Your Account

Follow these steps to fork and import the source repository into your own GitHub account:

### Step 1: Navigate to the Source Repository
1. Go to [https://github.com/DataOpsSupport/GH-Git-integration-source](https://github.com/DataOpsSupport/GH-Git-integration-source) in your web browser
2. You should see the repository page with its content and README

### Step 2: Fork the Repository
1. Click the **Fork** button in the top-right corner of the repository page
2. On the "Create a new fork" dialog:
   - Verify the repository name is `GH-Git-integration-source`
   - Ensure **Copy the `main` branch only** is checked (to reduce cloning time)
   - Verify the owner is set to your account
   - Click **Create fork**
3. Wait for the fork to complete. You will be automatically redirected to your forked repository at `https://github.com/[YourUsername]/GH-Git-integration-source`

   **Note:** For more information on forking repositories, see [GitHub's fork documentation](https://docs.github.com/en/get-started/quickstart/fork-a-repo)

### Step 3: Verify the Fork
1. Confirm you are on your copy of the repository (URL should be `https://github.com/[YourUsername]/GH-Git-integration-source`)
2. Verify the repository shows "forked from DataOpsSupport/GH-Git-integration-source" under the repository name
3. Verify the `/workspace` folder exists in the repository (check in the repository file browser)
4. Note the repository URL for use in later steps

---

## Part 2: Create a Fine-Grained Personal Access Token

Microsoft Fabric requires a Personal Access Token (PAT) to authenticate with GitHub. Follow these steps to create a fine-grained PAT:

### Step 1: Navigate to GitHub Settings
1. Click your profile avatar in the top-right corner of GitHub
2. Select **Settings** from the dropdown menu

### Step 2: Access Personal Access Tokens
1. In the left sidebar, scroll down and click **Developer settings**
2. Click **Personal access tokens** in the left sidebar
3. Click the dropdown and select **Fine-grained tokens**

### Step 3: Create a New Token
1. Click **Generate new token** button
2. On the "New fine-grained personal access token" page, fill in the following:
   - **Token name:** Enter a descriptive name, such as `Fabric-Git-Integration`
   - **Expiration:** Select an appropriate expiration date (e.g., 90 days or 1 year)
   - **Description:** (Optional) Enter `Token for Microsoft Fabric Git integration`
   - **Resource owner:** Select your GitHub account

### Step 4: Set Repository Access
1. Under **Repository access**, select **Only select repositories**
2. Click **Select repositories** and choose your forked repository: `GH-Git-integration-source`

### Step 5: Configure Permissions
1. Under **Permissions**, expand **Repository permissions**
2. Set the following permissions to **Read and write**:
   - **Contents** (required for reading and writing code and Fabric items)
   - **Metadata** (required for repository metadata)
3. Optionally, you can also grant:
   - **Pull requests** - Read and write (if you plan to work with PRs)
   - **Workflows** - Read and write (if repository contains GitHub Actions)

   **Security Note:** Fine-grained PATs are more secure than classic PATs because they allow you to limit permissions to specific repositories and actions. See [GitHub's authentication documentation](https://docs.github.com/en/authentication) for more details

### Step 6: Generate and Save the Token
1. Click **Generate token** button at the bottom of the page
2. **Important:** Copy the generated token immediately and save it in a secure location (you will not be able to see it again)
3. Keep this token confidential - treat it like a password and never share it
4. Consider storing it in a secure password manager (e.g., [1Password](https://1password.com/), [Bitwarden](https://bitwarden.com/), or [LastPass](https://www.lastpass.com/))
5. Do not commit or push this token to any repository
6. If the token is compromised, immediately delete it from GitHub and generate a new one

---

## Part 3: Create a Microsoft Fabric Workspace

### Step 1: Access Microsoft Fabric
1. Navigate to [https://app.powerbi.com](https://app.powerbi.com)
2. Sign in with your Microsoft account if prompted (see [Microsoft account documentation](https://support.microsoft.com/en-us/account-billing))
3. Switch to Microsoft Fabric (you should see the Fabric icon in the left navigation, or you may need to enable it from the Power BI admin panel)

   **Licensing Note:** You must have a Microsoft Fabric capacity or a trial capacity to use Fabric. See [Microsoft Fabric licensing](https://learn.microsoft.com/en-us/fabric/enterprise/licenses) for more information

### Step 2: Create a New Workspace
1. In the left navigation pane, click **Workspaces**
2. Click **New workspace** or the **+ New** button
3. On the workspace creation dialog:
   - **Name:** Enter `WS-fabricUser-xx-dev` (replace `xx` with your allocated number, e.g., `WS-fabricUser-01-dev`)
   - **Description:** (Optional) Enter a description such as `Fabric workspace with Git integration`
   - **Capacity:** Ensure a Fabric capacity is selected (this is where your workspace resources will be billed)
   - Click **Save**

   For more information, see [Create a Fabric workspace](https://learn.microsoft.com/en-us/fabric/get-started/create-workspaces)

### Step 3: Verify Workspace Creation
1. Wait for the workspace to be created
2. You should be automatically taken to the new workspace
3. Verify the workspace name appears in the left navigation and at the top of the page

---

## Part 4: Configure Git Integration

### Step 1: Open Workspace Settings
1. From your newly created workspace, click the **Settings** icon (gear icon) in the top-right corner
2. Select **Workspace settings**

   **Note:** You must be a workspace administrator to configure Git integration. If you do not have administrator access, contact your workspace owner

### Step 2: Access Git Integration
1. In the Workspace settings panel, scroll down to find **Git integration** or **Version control**
2. Click on **Git integration** or **Configure Git**

### Step 3: Connect to GitHub Repository
1. Click **Connect** or **Setup Git integration**
2. On the Git integration setup dialog, provide the following information:
   - **GitHub organization/user:** Enter your GitHub username
   - **Repository name:** Enter `GH-Git-integration-source`
   - **Branch name:** Enter `main` (the source branch)
   - **Authentication:** 
     - Select **Personal Access Token** as the authentication method
     - Paste the fine-grained PAT you created in Part 2
   - Click **Next** or **Connect**

### Step 4: Configure Branch and Folder Settings
1. On the next screen, configure the following:
   - **Create a new branch:** Select **Yes** or **Create new branch**
   - **New branch name:** Enter `dev`
   - **Base branch:** Select `main` (the new `dev` branch will be based on `main`)
   - **Folder name:** Enter `/workspace` (this is the workspace root folder in your repository - must match the folder structure in your GitHub repository)
   - Click **Next** or **Continue**

   **Important:** The folder path you specify must exist in your GitHub repository. If it doesn't exist, the sync will fail

### Step 5: Sync from Git Repository
1. When the confirmation window appears, you will see options for syncing:
   - Select **Sync from Git repository**
   - This will pull the contents from your GitHub repository into the Fabric workspace
   - Click **Sync** or **Confirm**
2. Wait for the sync to complete. This may take a few moments depending on the repository size and the number of items being imported
3. You will see a progress indicator showing the sync status. Do not navigate away from this page during the sync

   **Note:** When syncing from Git, any existing items with the same name in your workspace will be overwritten

### Step 6: Verify Git Integration
1. After syncing completes, navigate back to your workspace
2. You should see items and content imported from the repository (the number and types of items depend on what's in the `/workspace` folder)
3. In the Workspace settings, confirm that Git integration is now enabled and shows:
   - Repository: `[YourUsername]/GH-Git-integration-source`
   - Branch: `dev`
   - Folder: `/workspace`
4. Note the repository URL and branch name for future Git operations

   For more details on Git integration, see [Microsoft Fabric Git integration documentation](https://learn.microsoft.com/en-us/fabric/cicd/git-integration)

---

## Part 5: Verification and Next Steps

### Verify Your Setup
1. Check that your Fabric workspace contains the imported items from the repository
2. Open Workspace settings again to confirm Git integration details are correctly configured
3. Your workspace is now connected to Git and version-controlled

### What You've Accomplished
- ✅ Imported the source GitHub repository to your account
- ✅ Created a fine-grained Personal Access Token for secure authentication
- ✅ Created a new Fabric workspace with the naming convention `WS-fabricUser-xx`
- ✅ Configured Git integration with your forked repository
- ✅ Created a `dev` branch as your working branch
- ✅ Synced Fabric items from the GitHub repository

### Important Notes About Git Integration
- **Conflicts:** If the same item is modified both in Fabric and in Git, you may encounter merge conflicts. Always sync before making changes to avoid conflicts
- **Workspace deployment:** Git integration enables you to deploy Fabric items from Git to different workspaces (development, testing, production)
- **Source control:** All changes are tracked in Git, providing a complete audit trail and version history
- **Rollback:** You can revert to previous versions by updating from a specific commit in Git
- **Sensitive data:** Never store credentials, secrets, or personal information in your repository

### Next Steps
- Make changes to Fabric items in your workspace
- Learn to [commit changes back to Git](https://learn.microsoft.com/en-us/fabric/cicd/git-integration/commit-to-git) using the Fabric Git integration
- Learn to [update from Git](https://learn.microsoft.com/en-us/fabric/cicd/git-integration/update-from-git) to pull changes from your repository
- Collaborate with team members through Git branching and pull requests (see [GitHub's collaboration documentation](https://docs.github.com/en/pull-requests))
- Use the Git history to track changes and manage versions

---

## Important Considerations

### Workspace Permissions
- **Administrators:** Can configure Git integration, manage workspace settings, and manage user access
- **Contributors:** Can create and edit items but may not be able to configure Git integration (check with your workspace admin)
- **Viewers:** Can only view items in the workspace

If you cannot access Git integration settings, verify you have administrator rights in the workspace.

### Fabric Capacity and Costs
- Fabric workspaces must be assigned to a Fabric capacity (paid or trial)
- All operations in Fabric consume capacity units, which may result in billing depending on your subscription
- See [Microsoft Fabric licensing and capacity](https://learn.microsoft.com/en-us/fabric/enterprise/licenses) for pricing details
- Trial capacities are subject to usage limits and may expire

### GitHub Organization Policies
- If you're part of a GitHub organization, verify you're allowed to create and fork repositories
- Some organizations may have branch protection rules or required approvals
- Check with your GitHub organization administrator if you encounter restrictions

### Browser Requirements
- Ensure cookies and JavaScript are enabled for Power BI (https://app.powerbi.com)
- Pop-ups should be allowed for seamless Git integration setup
- If using a proxy or VPN, ensure GitHub (github.com) is accessible

---

**Git Integration Connection Failed**
- Verify your Personal Access Token is still valid and hasn't expired
- Ensure the token has the correct repository selected and has **Contents** and **Metadata** permissions
- Check that you have the correct repository name and GitHub username
- Verify you copied the entire token correctly (no missing characters)
- Confirm your firewall/network allows access to GitHub (check [GitHub IP addresses](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-githubs-ip-addresses) if using IP allowlists)
- Ensure the repository is accessible and not private/archived

**Sync Issues**
- Ensure the `/workspace` folder exists in your GitHub repository
- Verify that the `main` branch exists in your repository
- Check that the `dev` branch doesn't already exist (or use a different branch name)
- Verify that the files in the `/workspace` folder are in valid Fabric item format (`.pbixproj`, `.notebookproj`, etc.)
- Check for special characters in file names or folder names that might not be supported

**Authentication Errors**
- Generate a new Personal Access Token if the current one is not working (see [GitHub's token documentation](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token))
- Verify the token has not been revoked or expired from the GitHub settings page
- Clear browser cache and cookies, then try again
- Try in a different browser or private/incognito window

**Items Not Imported**
- Check the Fabric workspace log/activity to see if there were errors during sync
- Verify the items in the repository are in a supported Fabric format
- Ensure the folder structure matches what's specified in Git integration settings

**Resetting Git Integration**
- To disconnect Git integration: Go to Workspace settings > Git integration > Disconnect
- To reconnect: Click Git integration again and follow Part 4 steps
- Your workspace items will remain; disconnecting doesn't delete them

For more troubleshooting help, see [Microsoft Fabric Git integration troubleshooting](https://learn.microsoft.com/en-us/fabric/cicd/git-integration/troubleshoot-git-integration)

---

## References

### Microsoft Fabric Documentation
- [Microsoft Fabric Git integration documentation](https://learn.microsoft.com/en-us/fabric/cicd/git-integration)
- [Microsoft Fabric workspaces](https://learn.microsoft.com/en-us/fabric/get-started/workspaces)
- [Microsoft Fabric licensing](https://learn.microsoft.com/en-us/fabric/enterprise/licenses)
- [Fabric Git integration troubleshooting](https://learn.microsoft.com/en-us/fabric/cicd/git-integration/troubleshoot-git-integration)
- [Commit to Git from Fabric](https://learn.microsoft.com/en-us/fabric/cicd/git-integration/commit-to-git)
- [Update from Git in Fabric](https://learn.microsoft.com/en-us/fabric/cicd/git-integration/update-from-git)

### GitHub Documentation
- [GitHub Personal Access Tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
- [GitHub authentication](https://docs.github.com/en/authentication)
- [Forking a repository](https://docs.github.com/en/get-started/quickstart/fork-a-repo)
- [GitHub IP addresses](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-githubs-ip-addresses)
- [GitHub fine-grained personal access tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)

### Microsoft Account & Power BI
- [Microsoft account help](https://support.microsoft.com/en-us/account-billing)
- [Power BI documentation](https://docs.microsoft.com/en-us/power-bi/)
- [Getting started with Power BI](https://learn.microsoft.com/en-us/power-bi/fundamentals/power-bi-overview)

### Security Best Practices
- [GitHub security documentation](https://docs.github.com/en/code-security)
- [Protecting sensitive data in GitHub](https://docs.github.com/en/code-security/secret-scanning)
- [Git security best practices](https://git-scm.com/book/en/v2/Git-Tools-Signing-Your-Work)

### Additional Resources
- [Git tutorial and basics](https://git-scm.com/doc)
- [Understanding branches in Git](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell)
