# Version Control Rules

This document outlines the rules and best practices for using Git version control in all projects. Adherence to these guidelines ensures a clean, understandable, and maintainable project history.

## 1. Core Principles
- GIT IS MANDATORY: Git is the sole version control system to be used for all projects. It is expected to be present and utilized in all development workflows.

## 2. Branching Strategy
- PROTECT `main`/`master`: Direct commits to the `main` (or `master`) branch are strictly prohibited. This branch must always reflect a stable, production-ready, or near-production-ready state.
- FEATURE BRANCHES: All new features, bug fixes, experiments, or any changes to the codebase must be developed in separate auxiliary branches.
  - BRANCH NAMING CONVENTION: Use a consistent and descriptive naming convention for branches to clearly indicate their purpose. Suggestions include:
    - `feature/<feature-name>` (e.g., `feature/user-authentication`)
    - `fix/<issue-id>-<short-description>` (e.g., `fix/42-login-button-error`)
    - `chore/<task-description>` (e.g., `chore/update-ci-pipeline`)
    - `docs/<document-name>` (e.g., `docs/update-readme`)
- AVOID LONG-LIVED BRANCHES: Keep feature branches short-lived. Integrate them back into the main development line (e.g., `develop` or `main` or `master`) as soon as they are complete and stable to minimize merge conflicts and integration complexities.

## 3. Committing Changes
- FREQUENT AND ATOMIC COMMITS: Commit your changes frequently. Each commit should represent a single, logical, and atomic unit of work. This makes it easier to understand changes, review code, and roll back if necessary. Avoid lumping unrelated changes into a single commit.
- COMMIT MESSAGE STANDARDS:
  - CONVENTIONAL COMMITS: All commit messages MUST adhere to the Conventional Commits specification (e.g., `feat: implement user profile page`, `fix: correct calculation error in payment module`, `docs: add API endpoint documentation`, `refactor: simplify logging mechanism`). This standard improves history readability and enables automated changelog generation.
  - SUBJECT LINE LENGTH: The subject line of the commit message MUST NOT exceed 52 characters. This ensures readability in various Git tools and on platforms like GitHub.
  - MESSAGE BODY: If a commit requires more explanation than the subject line allows, provide a detailed description in the commit message body. Separate the subject from the body with a blank line. Explain *what* the commit does and *why*, not *how*.
- WORK IN PROGRESS (WIP): Avoid pushing WIP commits to shared branches unless explicitly for early feedback or collaboration, and clearly mark them as such (e.g., using a `WIP:` prefix in the commit message, though these should ideally be squashed before final merge).

## 4. Merging Changes
- PREFER REBASE WORKFLOW: When integrating changes from a feature branch into `main` (or `master`/`develop`), the preferred method is to rebase the feature branch onto the latest target branch. This creates a linear and cleaner project history.
  - WORKFLOW:
    1. Ensure your local `main`/`master` (or target branch) is up-to-date: `git checkout main && git pull origin main`
    2. Switch to your feature branch: `git checkout <feature-branch>`
    3. Rebase your feature branch: `git rebase main`
    4. Resolve any conflicts that arise during the rebase (as per 'Resolve Conflicts Locally' best practice). Test thoroughly after conflict resolution.
    5. Once rebased and tested, the feature branch can be merged into `main` (often via a Pull Request). A fast-forward merge should be possible.

## 5. General Best Practices
- `GIT PULL --REBASE`: When updating your local copy of a branch that has remote changes, prefer `git pull --rebase` over a simple `git pull` (which defaults to `git merge`). This avoids unnecessary merge commits in your local history.
- RESOLVE CONFLICTS LOCALLY: Always resolve merge or rebase conflicts on your local machine. Test thoroughly after resolving conflicts before pushing the changes.
- DO NOT FORCE PUSH TO SHARED BRANCHES: Avoid using `git push --force` or `git push --force-with-lease` on shared branches (like `main`, `master`, `develop`). Force pushing rewrites history and can cause significant problems for collaborators. Force pushing to your *own* feature branches (that no one else is using or has based work upon) can be acceptable to clean up your commit history *before* creating a Pull Request.
- KEEP REPOSITORY CLEAN: Regularly prune stale local and remote branches that have been merged and are no longer needed.
  - Delete a merged local branch: `git branch -d <local-branch-name>`
- STASH FOR CONTEXT SWITCHING: Use `git stash` to temporarily save uncommitted changes when you need to switch context (e.g., to work on an urgent bug fix on a different branch).
