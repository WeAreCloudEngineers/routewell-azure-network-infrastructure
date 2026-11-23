```markdown
# Group 1 — Official Git & GitHub Contributing Guide

This document defines the mandatory Git and GitHub workflow for all 22 contributors in **Group 1 (RouteWell — Multi-Tier Azure VNet Architecture)**. Following this workflow prevents merge conflicts, protects shared branches, and keeps our commit history clean and auditable.

> **Repository:** `https://github.com/WeAreCloudEngineers/routewell-azure-network.git`  
>


## 1. Branch Hierarchy & Ownership

We use a **4-tier branching model** so all four sub-groups can work in parallel without overwriting each other or flooding `develop` with conflicting Pull Requests.

| Branch Tier | Naming Pattern | Branched From | Merges Into | Owner / Reviewer |
| :--- | :--- | :--- | :--- | :--- |
| **Production / Stable** | `main` | — | — | Group Lead (`@JzwylFSE`) |
| **Integration** | `develop` | `main` | `main` | Group Lead (`@JzwylFSE`) |
| **Team Deliverable** | `docs/phase-0/<section>` or `feature/<task>` | `develop` | `develop` | Sub-Group Team Lead |
| **Member Sub-Branch** | `task/<name>-<subtask>` | Team Deliverable | Team Deliverable | Individual Squad Member |

### Phase 0 Team Deliverable Branches & Leads
| Section | Deliverable | Team Lead | Branch Name | Target Folder |
| :--- | :--- | :--- | :--- | :--- |
| **2.1** | CIDR Planning & Subnet Allocation | Jah'swill *(Group Lead)* | `docs/phase-0/2.1-cidr-planning` | `docs/phase-0/cidr/` |
| **2.2** | NSG Rule Justification | Chimamkpa Oduh | `docs/phase-0/2.2-nsg-rules` | `docs/phase-0/nsg/` |
| **2.3** | Public Access Mechanism | Emmanuel Ijeomah | `docs/phase-0/2.3-public-access` | `docs/phase-0/public-access/` |
| **2.4** | Network Architecture Diagram | Yewande Biobaku | `docs/phase-0/2.4-architecture-diagram` | `docs/phase-0/diagram/` |

> **Golden Rule:** Never push directly to `main`, `develop`, or a Team Deliverable branch. All changes must go through a Pull Request and review.

---

## 2. First-Time Local Setup (All 22 Members)

Run these commands once when joining the repository.

```bash
# 1. Configure your Git identity (use your real name and GitHub email)
git config --global user.name "Your Full Name"
git config --global user.email "your-github-email@example.com"

# 2. Navigate to your workspace and clone the repository
git clone https://github.com/WeAreCloudEngineers/routewell-azure-network.git
cd routewell-azure-network

# 3. Fetch all remote branches and switch to develop
git fetch origin
git switch develop
git pull --ff-only origin develop

# 4. Verify your setup
git status
git branch -a

```

---

## 3. Team Lead Workflow: Initializing a Team Deliverable Branch

Before your squad begins working on a phase or feature, the **Team Lead** creates and publishes the shared Team Deliverable branch from the latest `develop` (skip if the Group Lead has already pre-created it).

```bash
# 1. Sync your local develop branch
git switch develop
git pull --ff-only origin develop

# 2. Create the Team Deliverable branch (Example: Section 2.2 NSG Rules)
git switch -c docs/phase-0/2.2-nsg-rules

# 3. Push the branch to GitHub so your squad members can branch off it
git push -u origin HEAD

```

---

## 4. Squad Member Workflow: Daily Contribution Flow

Once your Team Lead confirms your Team Deliverable branch is live on GitHub, follow this exact sequence for your assigned task.

### Step 1: Sync Your Team Branch & Create Your Sub-Branch

```bash
# 1. Fetch the latest branches from GitHub
git fetch origin

# 2. Switch to your Team Deliverable branch and pull the latest changes
git switch docs/phase-0/2.2-nsg-rules
git pull --ff-only origin docs/phase-0/2.2-nsg-rules

# 3. Create your personal sub-branch from the Team Deliverable branch
# Naming format: task/<your-first-name>-<short-task-name>
git switch -c task/victor-inbound-nsg-rules

```

### Step 2: Make Changes, Inspect, Commit & Push

```bash
# 1. Create or edit ONLY your assigned file(s) inside your team's folder
# (Once your team adds real files, the placeholder .gitkeep file can be removed)

# 2. Inspect what you changed before staging
git status
git diff

# 3. Stage ONLY your intended files (avoid blind 'git add .')
git add docs/phase-0/nsg/inbound-rules.md

# 4. Commit using Conventional Commits (see Section 8)
git commit -m "docs: add inbound NSG rules for web tier"

# 5. Push your sub-branch to GitHub
git push -u origin HEAD

```

### Step 3: Open a Pull Request (Squad Member → Team Lead)

1. Go to the repository on GitHub → **Pull requests** → **New pull request**.
2. **CRITICAL:** Set the **Base branch** to your **Team Deliverable branch** (e.g., `docs/phase-0/2.2-nsg-rules`) — **NOT** `develop` and **NOT** `main`.
3. Set the **Compare branch** to your personal sub-branch (e.g., `task/victor-inbound-nsg-rules`).
4. Fill out the Pull Request template completely (summary, changes, checklist).
5. Request a review from your **Sub-Group Team Lead**.

---

## 5. Team Lead Workflow: Reviewing & Consolidating into `develop`

### Step 1: Review and Merge Squad Member PRs

1. Inspect each squad member's PR for technical accuracy, formatting, scope, and zero secrets.
2. Request changes if anything is incomplete or incorrect.
3. Once approved, click **Create a merge commit** to merge the member's `task/*` branch into your Team Deliverable branch.

### Step 2: Consolidate & Verify Locally

After all squad members' PRs are merged into your Team Deliverable branch, pull everything locally to do a final integration check:

```bash
git switch docs/phase-0/2.2-nsg-rules
git pull --ff-only origin docs/phase-0/2.2-nsg-rules

```

Verify that all files are cleanly formatted, consistent, and complete.

### Step 3: Open the Consolidated PR (`Team Branch` → `develop`)

1. Go to GitHub → **Pull requests** → **New pull request**.
2. Set **Base branch** to `develop`.
3. Set **Compare branch** to your Team Deliverable branch (e.g., `docs/phase-0/2.2-nsg-rules`).
4. Fill out the PR template and assign **Group Lead (`@JzwylFSE`)** as the reviewer.
5. Wait for Group Lead approval and merge into `develop`.

---

## 6. Group Lead Workflow: Promoting `develop` to `main`

Once all four sub-group deliverables for a milestone are merged into `develop` and verified:

1. Open a Release Pull Request on GitHub:
* **Base branch:** `main`
* **Compare branch:** `develop`


2. Verify all Phase 0 docs, scripts, validation outputs, and screenshots are complete with zero secrets.
3. Merge using **Create a merge commit**.
4. Sync local `main`:
```bash
git switch main
git pull --ff-only origin main

```



---

## 7. Keeping Branches Synced & Post-Merge Cleanup

### If Your Team Branch Updates While You Are Still Working

Pull the latest changes from your Team Deliverable branch into your personal `task/*` branch to avoid merge conflicts later:

```bash
# While on your personal task/* branch:
git fetch origin
git merge origin/docs/phase-0/2.2-nsg-rules

# If Git reports a merge conflict:
# 1. Open the conflicting file(s) in VS Code and resolve the markers
# 2. Stage, commit, and push the resolution:
git add <resolved-file>
git commit -m "chore: resolve merge conflicts with team branch"
git push

```

*(If a merge goes wrong and you want to cancel it immediately, run `git merge --abort`. Never use `git push --force` on shared branches.)*

### After Your Pull Request Is Merged (Cleanup)

Clean up your local branches so your workspace stays tidy:

```bash
# 1. Switch back to your base branch (Team Branch for Members, develop for Leads)
git switch develop
git pull --ff-only origin develop

# 2. Delete your merged local branch safely
git branch -d task/victor-inbound-nsg-rules

# 3. Remove stale references to deleted remote branches
git fetch --prune origin

```

---

## 8. Non-Negotiable Standards

### Conventional Commit Messages

Every commit must start with a clear prefix describing the type of change. Vague messages like `"update"`, `"fixed"`, or `"done"` will be rejected.

| Prefix | When to Use | Example |
| --- | --- | --- |
| `docs:` | Documentation, Phase 0 markdown, README, diagrams | `docs: calculate web tier subnet usable IPs` |
| `feat:` | New Azure CLI scripts, configs, or infrastructure features | `feat: add VNet and subnet provisioning script` |
| `fix:` | Bug fixes, broken script commands, or incorrect NSG rules | `fix: correct priority conflict on outbound NSG rule` |
| `refactor:` | Restructuring scripts or docs without changing behavior | `refactor: modularize resource group variables` |
| `test:` | Connectivity tests, validation scripts, or test logs | `test: add curl and ping verification outputs` |
| `chore:` | Repo maintenance, `.gitignore`, templates, `.gitkeep` cleanup | `chore: remove placeholder gitkeep in cidr folder` |

### Merge Strategy

* Always use **Create a merge commit** on GitHub. Squash merging and rebase merging are disabled to preserve full branch ancestry.

### Security & Azure Safety

* **Zero Secrets in Git:** Never commit `.env` files, Azure credentials, subscription keys, passwords, or SSH private keys (`*.pem`, `*.key`, `id_rsa`, `id_ed25519`). Use `.env.example` for placeholder variable names only.
* **Controlled Deployments:** A merged Pull Request does **not** automatically authorize running provisioning or teardown scripts against the shared Azure subscription. Only designated maintainers may execute live Azure deployments.

```

```