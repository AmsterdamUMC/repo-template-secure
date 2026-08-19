# Research Project Template (Secure & Reproducible)

This repository template helps Amsterdam UMC researchers:

- Organize research code and analysis
- Prevent accidental upload of sensitive data
- Make work reproducible and understandable
- Comply with GDPR and institutional data protection requirements

**This template is specifically designed for healthcare research with patient data, research datasets, and sensitive information.**

---

## Why Use This Template?

Research code often lives alongside sensitive data - patient records, experimental results, confidential information. It's easy to accidentally commit a CSV file or hardcode a password. One `git push` later, and that data is on GitHub servers, potentially forever.

This template prevents those accidents with automated checks that run before you commit, before you push, and again on GitHub's servers when you push or open a pull request - four safety nets working together. See [Security System Overview](#security-system-overview) below for the summary, or [Security Workflows](docs/security-workflows.md) for exactly how each layer works.

---

## Quick Start

### Step 1: Create Your Repository

1. Go to https://github.com/amsterdamumc/repo-template-secure
2. Click **"Use this template"** → "Create a new repository"
3. Choose **Private** visibility (always start private!)
4. Give it a meaningful name: `project-name-analysis`

### Step 2: Clone and Install Hooks (Required)

```bash
git clone https://github.com/amsterdamumc/your-project.git
cd your-project

pip install pre-commit
pre-commit install
pre-commit install --hook-type pre-push
```

**Every contributor must do this.** Without hooks installed, you have no local protection and rely entirely on GitHub Actions - which only runs after code has already left your machine.

### Step 3: Verify It Works

```bash
# This should fail
echo "test data" > test.csv
git add test.csv
git commit -m "test"
# Expected: ❌ Commit blocked by pre-commit hook

# Clean up
rm test.csv
git reset
```

If the commit succeeds, **the hooks are not installed correctly** - repeat Step 2.

### Step 4: Customize for Your Project

1. **Replace README.md** - copy [`README-template.md`](README-template.md) over this file and fill in your project details
2. **Replace CITATION.cff entirely** - it ships with placeholder details, not real ones. Swap in your own name(s), title, affiliation, and ORCID; don't just add to it
3. **Customize `.gitignore`** if needed - add project-specific patterns, but don't remove the security patterns
4. **Document your data** in `/data/README.md` - where it comes from, expected structure - without actual paths or credentials

### Step 5: Start Working

```bash
data/                    # Git-ignored - put data here
├── raw/patients.csv
└── processed/cleaned.xlsx

scripts/                 # Tracked by Git - put code here
├── 01_import.R
├── 02_clean.R
└── 03_analyze.R

git add scripts/
git commit -m "Add data import script"
git push
```

---

## Security System Overview

This template includes **four layers of protection** to prevent accidental data leaks:

| Layer               | When It Runs        | What It Checks                              | Can Be Bypassed?         |
| ------------------- | ------------------- | -------------------------------------------- | ------------------------ |
| **`.gitignore`**    | Before `git add`    | Blocks tracking of sensitive file types     | Yes (with `-f` flag)     |
| **Pre-commit hook** | Before `git commit` | Forbidden files, PII, secrets, formatting   | Yes (with `--no-verify`) |
| **Pre-push hook**   | Before `git push`   | Final check before code leaves your machine | Yes (with `--no-verify`) |
| **GitHub Actions**  | After `git push`    | Server-side validation, blocks PRs          | **No**                   |

**No single layer is perfect** - together they catch most mistakes. Always review what you're committing.

For exactly how each layer works technically (config, detection logic, troubleshooting), see **[Security Workflows](docs/security-workflows.md)**. For exactly what's blocked and why, see **[SECURITY.md → Forbidden Content](SECURITY.md#forbidden-content)** - that's the authoritative, up-to-date list, so this README doesn't duplicate it.

---

## Project Structure

| File/Folder               | Purpose                                                        |
| -------------------------- | --------------------------------------------------------------- |
| `.github/workflows/`      | Automated security checks on every push                         |
| `.pre-commit-config.yaml` | Local security hooks configuration                              |
| `.gitignore`              | Prevents tracking sensitive file types                          |
| `gitleaks.toml`           | Secrets detection configuration                                 |
| `CODEOWNERS`              | Defines code reviewers                                          |
| `CITATION.cff`            | **Placeholder** citation info - replace before anyone cites you |
| `data/`                   | **Local-only** folder for data files (blocked by Git)           |
| `scripts/`                | Your analysis code - this DOES get committed                    |
| `results/`                | Non-sensitive outputs (aggregated stats, figures) - commit only if data-free |
| `docs/`                   | Guides: security workflows, data handling, FAQ                  |
| `README-template.md`      | Starter template for your project's README                      |
| `SECURITY.md`             | Detailed security policy, forbidden content, incident response  |
| `CONTRIBUTING.md`         | Guidelines for contributors                                     |
| `CODE_OF_CONDUCT.md`      | Community standards                                             |

---

## Working with Data

```
project/
├── data/           # Safe - blocked by .gitignore
│   ├── raw/
│   └── processed/
├── scripts/        # Commit your analysis code here
└── results/        # Commit figures and non-sensitive outputs
```

Don't put data files anywhere else - anything outside `/data/` risks getting committed. Document data sources in `/data/README.md` (without actual paths or credentials).

**Full guide, including sharing data with collaborators and handling edge cases: [Data Handling](docs/data-handling.md).**

---

## You Don't Need to Be an Expert

This template is designed for researchers, not software engineers. You don't need to understand how pre-commit works, or how the security scanning works under the hood.

**You just need to know:**

1. **Data goes in `/data/`** - that folder is safe, Git ignores it
2. **Code goes in `/scripts/`** - this gets tracked by Git
3. **If hooks block something** - read the error message, it will tell you what's wrong
4. **If you're unsure** - ask! See the [FAQ](docs/faq.md) or contact [b.vandervelde@amsterdamumc.nl](mailto:b.vandervelde@amsterdamumc.nl)

---

## What This Template Doesn't Do

This template helps with **GitHub security**. It doesn't handle:

- **Data storage** - you still need myDRE, network drives, etc.
- **Data sharing** - use SURF FileSender or institutional tools
- **Access control** - set repository permissions appropriately
- **Backup** - keep backups of your work separate from Git
- **Computing environment** - use your own workstation, cluster, or cloud

Think of this as **one layer** in your research data management strategy, focused specifically on preventing accidental data leaks via Git.

---

## Getting Started Checklist

- [ ] Created repository from template (private!)
- [ ] Cloned to local machine
- [ ] Installed pre-commit hooks (`pre-commit install` + `--hook-type pre-push`)
- [ ] Tested that hooks work (try committing a `.csv` file)
- [ ] Replaced README with [`README-template.md`](README-template.md)
- [ ] Replaced CITATION.cff placeholder details with your own
- [ ] Updated `.gitignore` for project-specific needs (if needed)
- [ ] Documented data in `/data/README.md`
- [ ] Added collaborators (Settings → Collaborators)
- [ ] Read [SECURITY.md](SECURITY.md) and [Data Handling](docs/data-handling.md)
- [ ] Know where your data is stored (myDRE, network drive, etc.)
- [ ] Know how to contact the security team if needed

---

## ⚠️ Accidentally Committed Sensitive Data?

**Do NOT panic, but act immediately:**

1. **Stop** - don't try to fix it yourself
2. **Report** - contact <b.vandervelde@amsterdamumc.nl> immediately, subject "SECURITY INCIDENT"
3. **Do NOT** open a public GitHub issue
4. **Include:** repository name, what was exposed, when committed

Git keeps full history - deleting a file doesn't remove it. History needs to be rewritten, which requires coordination.

**Full incident response procedure: [SECURITY.md → Incident Response](SECURITY.md#incident-response).**

---

## Before Making a Repository Public

Repositories should start **private by default**. Only make public after:

- [ ] Security scans pass (no red ❌ in GitHub Actions)
- [ ] Full Git history reviewed for sensitive data
- [ ] No credentials or API keys in code or history
- [ ] README, LICENSE, and documentation complete
- [ ] Approved by project lead and data steward

**Full checklist and process: [SECURITY.md → Repository Access Control](SECURITY.md#repository-access-control).**

---

## Documentation

- **[Data Handling](docs/data-handling.md)** - Where to put files, how to share data safely, common scenarios
- **[Security Workflows](docs/security-workflows.md)** - Technical deep dive: how each layer works, config, troubleshooting
- **[FAQ](docs/faq.md)** - Answers to common questions
- **[SECURITY.md](SECURITY.md)** - Complete security policy, forbidden content, incident response, access control
- **[CONTRIBUTING.md](CONTRIBUTING.md)** - How to contribute safely

**Related systems:**
- Security rules: https://github.com/amsterdamumc/org-security-workflows
- Telemetry: https://github.com/amsterdamumc/security-telemetry
- Organization-wide scanner: https://github.com/amsterdamumc/org-security-scanner

---

## Support

**Technical questions:** <b.vandervelde@amsterdamumc.nl>
**Data management:** Contact your data steward
**Security incidents:** [SECURITY.md](SECURITY.md) (mark URGENT)

---

## License

This template is provided under the MIT License. See [LICENSE](LICENSE) for details.

---

*This template is maintained by Amsterdam UMC Research Software Management to support responsible, open, and reproducible research at Amsterdam UMC.*