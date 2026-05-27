# Save your Power BI file in PBIP format

Power BI Project (PBIP) format saves your report as a folder of plain text files instead of a single binary `.pbix` file. This makes it possible to track changes with Git.

1. Open your report in Power BI Desktop.
2. Go to `File` > `Save as`.
3. In the `Save as type` dropdown, select `Power BI Project (.pbip)`.
4. Choose a folder on your computer and save. Power BI Desktop creates a folder structure with your report and semantic model as separate subfolders.

> Note: PBIP format is a preview feature. If you do not see it in the Save dialog, enable it under `File` > `Options and settings` > `Options` > `Preview features` > `Store reports using enhanced metadata format`.

# Open the folder in VS Code and create a local Git repo

1. Open Visual Studio Code.
2. Select `File` > `Open Folder` and navigate to the folder that contains your `.pbip` file and the two subfolders (`Contoso10k.Report` and `Contoso10k.SemanticModel`).
3. Go to the source control menu in VSCode 
4. Click on Initialize Repository
5. Add a commit message like ("initial commit")
6. Stage all the changes with `+`
7. Click Commit

> Tip: Check the Git panel on the left sidebar in VS Code (the branch icon) to see staged and unstaged changes visually after each step.

> Note on terminal vs. VS Code UI: For all commit and stage operations in these exercises you can use either the **VS Code Source Control panel** (recommended) or the **integrated terminal**. Both options are shown throughout this guide — use whichever feels more comfortable.

# Exercise 1 — Add a new measure and commit

In this exercise you create a new DAX measure in the semantic model and commit the change.

1. In Power BI Desktop, open the `Data` view or the `Model` view.
2. Select the `Sales` table in the fields pane.
3. Click `New measure` in the ribbon.
4. Enter the following DAX expression:
   ```dax
   Avg Age = AVERAGE ( Customer[Age] )
   ```
5. Save: `Ctrl+S`.
6. Stage and commit the change:

   **VS Code UI:** Go to the Source Control panel, stage the changed file with `+`, enter the message `Added Avg Age measure`, and click `Commit`.

   **Terminal:**
   ```bash
   git add .
   git commit -m "Added Avg Age measure"
   ```

# Exercise 2 — Add a new report page and commit

In this exercise you add a new page to the report and build a visual on it.

1. In Power BI Desktop, click the `+` button at the bottom of the report to add a new page.
2. Add a bar chart or similar visual showing margin broken down by brand.
3. Save: `Ctrl+S`.
4. Stage and commit:

   **VS Code UI:** Go to the Source Control panel, stage the changed files, enter the message `Added brand by margin visual on new page`, and click `Commit`.

   **Terminal:**
   ```bash
   git add .
   git commit -m "Added brand by margin visual on new page"
   ```

# Exercise 3 — Format a visual and commit the change

In this exercise you make formatting changes to an existing visual and commit them.

1. In Power BI Desktop, navigate to `Demo` page that contains the sales table visual.
2. Select the table visual, then open the `Format` pane.
3. Under `General` first select `Effects` and then `Background` turn it on, set a background color for the visual.
4. Go to the `Margin` page and select the bar chart that shows margin by brand.
5. In the `Format` pane, enable `Data labels`.
6. Save the file: `Ctrl+S`. Power BI Desktop writes the changes to the PBIP folder files automatically.
7. Switch to VS Code and stage and commit the change:

   **VS Code UI:** Go to the Source Control panel, review the changed files, click `+` to stage them, enter the message `Background color to table and added data label to brand by margin`, and click `Commit`.

   **Terminal:**
   ```bash
   git add .
   git commit -m "Background color to table and added data label to brand by margin"
   ```

# Exercise 4 — Add a new measure and commit

1. In Power BI Desktop, open the `Data` view or the `Model` view.
2. Select the `Sales` table in the fields pane.
3. Click `New measure` in the ribbon.
4. Enter the following DAX expression:
   ```dax
   Double Margin = [Margin] * 2
   ```
5. Set the format string to match the existing margin measures (currency, Hungarian Ft format).
6. Save: `Ctrl+S`.
7. Stage and commit the change:

   **VS Code UI:** Go to the Source Control panel, stage the changed file with `+`, enter the message `Add Double margin Measure`, and click `Commit`.

   **Terminal:**
   ```bash
   git add .
   git commit -m "Add Double margin Measure"
   ```

# Exercise 5 — Revert the last commit

Git lets you undo a commit cleanly without deleting history. Here you revert the measure you just added as you realize that the measure doesnt make too much sense.

**VS Code UI:** Right click on the last commit and select `Revert...` and ok

1. Open the integrated terminal in VS Code (`` Ctrl+` ``) and run:
   ```bash
   git revert HEAD
   ```
2. Git opens a commit message editor. The default message (`Revert "Add Double margin Measure"`) is fine — save and close the editor (`:wq` in vim, or close the tab in VS Code).
3. Verify that the `Double Margin` measure is gone from `Sales.tmdl` and check the history:
   ```bash
   git log --oneline
   ```
   You should see both the original commit and the new revert commit in the history.

> Note: `git revert` creates a new commit that undoes the changes. The original commit stays in the history. This is the safe way to undo changes that have already been shared with others.

