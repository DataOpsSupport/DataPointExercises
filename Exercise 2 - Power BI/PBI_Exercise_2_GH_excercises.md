# A note on terminal vs. VS Code UI

For all Git operations in these exercises — creating branches, staging files, and committing — you can use either the **VS Code Source Control panel** (recommended for now for new practitioners) or the **integrated terminal**. Both approaches are shown throughout this guide.

> Tip: The VS Code UI is a great way to see exactly what changed before you commit. The terminal is faster once you are comfortable with the commands. Use whichever feels right for you.

# Push your local repo to GitHub

1. Go to [github.com](https://github.com) and sign in.
2. Click the `+` icon in the top right corner and select `New repository`.
3. Give the repository a name (e.g. `contoso10k-dataops`).
4. Leave it **empty** — do not initialise with a README, `.gitignore`, or licence.
5. Click `Create repository`.
6. Copy the HTTPS URL of your new repository.
7. Add GitHub as the remote and push — pick your preferred method:

   **VS Code UI:** Open the Source Control panel, click the `...` menu, select `Remote` > `Add Remote`, paste the URL, then click `Publish Branch`.

   **Terminal:**
   ```bash
   git remote add origin https://github.com/YOUR-USERNAME/contoso10k-dataops.git
   git branch -M main
   git push -u origin main
   ```
8. Refresh your GitHub repository page and confirm the files are visible.

> Tip: If you are prompted for credentials, use your GitHub credentials.

# Exercise 5 — Add a new measure on a feature branch

As a general best practice, it is recommended to use feature branches whenever you are modifying your project. So far locally as initial practice, we only edited our main branch, but now we are going to follow this best practice and create a feature branch so we can make our changes separately from main, without the risk of breaking it.

1. Create and switch to a new branch called `add_new_measure`:

   **VS Code UI:** Click the branch name in the bottom-left status bar, select `Create new branch`, and type `add_new_measure`.

   **Terminal:**
   ```bash
   git checkout -b add_new_measure
   ```
2. In Power BI Desktop, select the `Sales` table and click `New measure`.
3. Enter the following DAX expression:
   ```dax
   Double Margin = [Margin] * 2
   ```
4. Format the measure consistently with the other margin measures.
5. Save: `Ctrl+S`.
6. Stage, commit, and push:

   **VS Code UI:** Go to the Source Control panel, stage the changed file with `+`, enter the message `Double margin measure added`, click `Commit`, then `Publish Branch`.

   **Terminal:**
   ```bash
   git add .
   git commit -m "Double margin measure added"
   git push -u origin add_new_measure
   ```

# Exercise 6 — Fix the Margin % measure on a second feature branch

1. Switch back to `main` and create a new branch called `modify_measures`:

   **VS Code UI:** Click the branch name in the status bar, select `main`. Then click the branch name again, select `Create new branch`, and type `modify_measures`.

   **Terminal:**
   ```bash
   git checkout main
   git checkout -b modify_measures
   ```
2. In Power BI Desktop, open the `Data` or `Model` view and select the `Margin %` measure in the `Sales` table.
3. Update the DAX expression to:
   ```dax
   DIVIDE ( [Margin], [Sales Amount] ) * 0.98
   ```
4. Save: `Ctrl+S`.
5. Stage, commit, and push:

   **VS Code UI:** Go to the Source Control panel, stage the changed file, enter the message `Corrected margin % calculation`, click `Commit`, then `Publish Branch`.

   **Terminal:**
   ```bash
   git add .
   git commit -m "Corrected margin % calculation"
   git push -u origin modify_measures
   ```
6. On GitHub, open a pull request from `modify_measures` into `main` and merge it.
7. Then open a pull request from `add_new_measure` into `main` and merge it.
8. Pull the updated `main` branch back locally:

   **VS Code UI:** Click the branch name in the status bar, select `main`, then click `Sync Changes`.

   **Terminal:**
   ```bash
   git checkout main
   git pull
   ```


# Exercise 7 — Solve a merge conflict (1/2)

In this exercise you deliberately create a merge conflict by having two branches change the same file in different ways. The conflicting file is `pages.json`, which stores the active report page.

**First branch — switch to page 1:**

1. Switch to the `modify_measures` branch:

   **VS Code UI:** Click the branch name in the status bar and select `modify_measures`.

   **Terminal:**
   ```bash
   git checkout modify_measures
   ```
2. In Power BI Desktop, navigate to the **Page 1**.
3. Save: `Ctrl+S`.
4. Stage, commit, and push:

   **VS Code UI:** Go to the Source Control panel, stage the changed file, enter the message `switched to page 1`, click `Commit`, then `Sync Changes`.

   **Terminal:**
   ```bash
   git add .
   git commit -m "switched to page 1"
   git push
   ```
5. Switch to `main` and merge/use a PR to merge the `modify_measures` branch — this will succeed with no conflict:

   **VS Code UI:** Click the branch name, select `main`. Then open the `...` menu in the Source Control panel, select `Branch` > `Merge Branch`, and choose `modify_measures`.

   **Terminal:**
   ```bash
   git checkout main
   git merge modify_measures
   ```

**Second branch — switch to the Margin page:**

6. Switch to the `add_new_measure` branch:

   **VS Code UI:** Click the branch name and select `add_new_measure`.

   **Terminal:**
   ```bash
   git checkout add_new_measure
   ```
7. In Power BI Desktop, navigate to the **Margin report page**.
8. Save: `Ctrl+S`.
9. Stage and commit:

   **VS Code UI:** Go to the Source Control panel, stage the changed file, enter the message `switched to Margin page`, and click `Commit`.

   **Terminal:**
   ```bash
   git add .
   git commit -m "switched to Margin page"
   git push
   ```

# Exercise 8 — Solve a merge conflict (2/2)

Now try to merge the second branch into `main`. Because both branches edited `pages.json` differently, Git cannot merge automatically.

1. Switch to `main` and attempt the merge:

   **VS Code UI:** Click the branch name, select `main`. Then open `...` > `Branch` > `Merge Branch` > `add_new_measure`.

   **Terminal:**
   ```bash
   git checkout main
   git merge add_new_measure
   ```
2. Git will report a conflict. In VS Code you will see `pages.json` marked with a conflict indicator in the Source Control panel. Open the file — you will see both versions highlighted.
3. Resolve the conflict by choosing one of the following options in VS Code:
   - **Accept Current Change** — keeps the page that was set by `modify_measures` (page 1)
   - **Accept Incoming Change** — keeps the page that was set by `add_new_measure` (Margin page)
   - **Manual** — edit the file directly to set whichever value you want, then delete the conflict markers
4. Save the file after resolving.
5. Stage and commit the resolved file:

   **VS Code UI:** Go to the Source Control panel, stage `pages.json`, and click `Commit` (VS Code pre-fills a merge commit message).

   **Terminal:**
   ```bash
   git add .
   git commit
   ```
6. Open the report in Power BI Desktop and verify it opens on the page you chose.

> Note: A merge conflict does not mean something broke — it just means Git found two competing changes to the same line and needs you to decide which one wins.
