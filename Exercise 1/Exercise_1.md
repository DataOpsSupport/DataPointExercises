# Create fine-grained PAT in GitHub

A fine-grained personal access token (PAT) is the recommended way to authorize Microsoft Fabric to access a private GitHub repository.

1. Sign in to GitHub and open `Settings` > `Developer settings` > `Personal access tokens` > `Fine-grained tokens`.
2. Click `Generate new token` and choose the repository or organization scope you need.
3. Select the minimum required permissions:
   - `Repository contents: Read and write`
   - `Metadata: Read`
   - `Workflows: Read` (if Fabric will interact with GitHub Actions)
4. Set an expiration date to limit exposure, then generate the token.
5. Copy the PAT value immediately and save it securely; you will not be able to see it again.

> Tip: Use a separate fine-grained PAT for Fabric integration so it can be revoked independently of your personal GitHub access.

# Create new workspace in Fabric

To create a new workspace in Microsoft Fabric:

1. Sign in to the Microsoft Fabric portal.
2. In the Fabric landing page, select `Workspaces` from the left navigation.
3. Click `New workspace`.
4. Enter a workspace name that reflects the your account, such as `Fabric01Dev`.
5. Choose the appropriate tenant, region, and capacity settings for your needs.
6. Optionally configure a description and assign user roles or access permissions.
7. Create the workspace.

Once the workspace exists, verify the workspace home page loads and that you can access the notebook, dataflows, or reports experience from the new workspace.

# Connect via Git integration

After creating the Fabric workspace, configure Git integration to connect to your GitHub repository:

1. Open the new Fabric workspace and choose `Settings` or `Manage workspace`.
2. Find the `Git integration` section.
3. Select `GitHub` as the provider.
4. Enter the repository URL and branch you want to connect.
5. Paste the fine-grained PAT you created in GitHub when prompted for authentication.
6. Confirm repository access and save the connection.
7. Validate the integration by creating or opening a file in Fabric and checking that Git metadata appears (branch, commit history, sync status).

> Note: If your repository is private, ensure the PAT has the correct repository permissions and that the GitHub repo is reachable from the Fabric tenant.
