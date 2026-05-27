# GitHub Actions exercise — Automated Best Practice Analysis

In this exercise you add an automated workflow to your repository that runs the Tabular Editor Best Practice Analyzer (BPA) on every pull request targeting `main`. If any measure or column violates the rules, the workflow fails and blocks the merge.

# Step 1 — Add the BPA rules file

1. In VS Code, right-click in the Explorer panel and select `New Folder`. Name it `bpa_scripts`.
2. Copy or drag the `BPA_Rules.json` file provided by the presenter into the `bpa_scripts` folder.
3. Make sure you are on the `main` branch, then commit the new folder:

   **VS Code UI:** Go to the Source Control panel, stage the new file with `+`, enter the message `Add BPA rules`, and click `Commit`. Then click `Sync Changes` to push.

   **Terminal:**
   ```bash
   git checkout main
   git add bpa_scripts/
   git commit -m "Add BPA rules"
   git push
   ```

# Step 2 — Create the GitHub Actions workflow

1. Go to your repository on GitHub.
2. Click the `Actions` tab.
3. Click `set up a workflow yourself` at the top of the page. This opens an in-browser editor and creates the file at `.github/workflows/main.yml`.
4. Delete the placeholder content in the editor and paste in the workflow `.yml` file provided by the presenter.
5. Click `Commit changes`, enter a commit message (e.g. `Add BPA workflow`), and commit directly to `main`.

> Note: GitHub Actions will automatically pick up any `.yml` file placed in `.github/workflows/`. The workflow will trigger on every pull request targeting `main`.

# Step 3 — Create a branch and introduce a BPA violation

Now you will test whether the workflow catches a real violation. Create a branch and add a measure that deliberately breaks a BPA rule by using `/` instead of `DIVIDE`.

1. In VS Code, create a new branch from `main`:

   **VS Code UI:** Click the branch name in the bottom-left status bar, select `Create new branch`, and type a name such as `test-bpa`.

   **Terminal:**
   ```bash
   git checkout main
   git pull
   git checkout -b test-bpa
   ```
2. In Power BI Desktop, select the `Sales` table and click `New measure`.
3. Enter the following DAX expression:
   ```dax
   Revenue per Unit Sold = [Sales Amount] / [Total Quantity]
   ```
4. Save: `Ctrl+S`.
5. Stage, commit, and push:

   **VS Code UI:** Go to the Source Control panel, stage the changed file, enter the commit message below, click `Commit`, then `Publish Branch`.

   **Terminal:**
   ```bash
   git add .
   git commit -m "Adding new measure not following DAX best practices"
   git push -u origin test-bpa
   ```

# Step 4 — Open a pull request and check the result

1. Go to your repository on GitHub. You should see a banner prompting you to open a pull request for the branch you just pushed — click `Compare & pull request`.
2. Open the pull request into `main`.
3. Scroll down to the checks section at the bottom of the PR. The GitHub Actions workflow will start running automatically.
4. Wait for the check to complete. It should **fail**, with the BPA flagging `[Revenue per Unit Sold]` for using `/` instead of `DIVIDE`.
5. Click on `Details` next to the failed check to see the full workflow log and the BPA output.

> Note: This is exactly the gate that protects your `main` branch in production — a developer cannot merge code that violates your agreed DAX standards without first fixing the violation or explicitly overriding the rule.
