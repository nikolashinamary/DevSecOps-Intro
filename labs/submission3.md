# Lab 3 Submission - Secure Git

## Task 1 - SSH Commit Signature Verification

### 1.1 Why commit signing matters

- Commit signing proves commit authorship and protects commit integrity.
- It reduces the risk of spoofed commits in shared repositories.
- It adds accountability and auditability to CI/CD and code-review workflows.
- It helps enforce trusted-source policies in DevSecOps pipelines.

### 1.2 SSH signing setup evidence

Local Git signing configuration:

```bash
git config --get user.signingkey
git config --get gpg.format
git config --get commit.gpgsign
```

Observed values:

```text
/Users/marianikolashina/.ssh/id_rsa.pub
ssh
true
```

### 1.3 Signed commit evidence

Existing signed commit in repository history:

- Commit: `9684b3c6625e32f3c63bb11e6f42f322bdb2ab89`
- Evidence from commit object:

```text
gpgsig -----BEGIN SSH SIGNATURE-----
```

GitHub verification evidence should be attached in screenshot placeholders below.

### 1.4 Analysis: why commit signing is critical in DevSecOps workflows

- It prevents attacker-controlled or impersonated commits from blending into trusted history.
- It strengthens supply-chain trust by allowing teams to require verified signatures before merge/deploy.
- It improves forensics by tying changes to a cryptographic identity, not only a mutable username/email.
- It supports policy automation (branch protection and verified-commit gates) for secure SDLC controls.

## Task 2 - Pre-commit Secret Scanning

### 2.1 Hook setup process and configuration

Implemented local hook:

- Path: `.git/hooks/pre-commit`
- Permissions: executable (`chmod +x .git/hooks/pre-commit`)

Hook behavior summary:

- Collects staged files (`git diff --cached --name-only --diff-filter=ACM`).
- Splits files into `lectures/*` and non-lectures groups.
- Runs TruffleHog on non-lectures files using Docker:
  - `trufflesecurity/trufflehog:latest`
- Runs Gitleaks on each staged file using Docker:
  - `zricethezav/gitleaks:latest`
- Blocks commit if secrets are found in non-lectures files.
- Allows commit if findings are only in `lectures/*` (documented educational exception).

### 2.2 Test results (blocked and successful paths)

Blocked-path test case:

- Stage a file containing a fake credential pattern (for example AWS-style key).
- Attempt commit; hook should fail with `COMMIT BLOCKED`.
- Remove/redact secret, restage, and re-run commit.

Current local hook sanity check output (no staged files):

```text
[pre-commit] scanning staged files for secrets...
[pre-commit] no staged files; skipping scans
```

Screenshots for blocked and successful test paths are listed in placeholders below.

### 2.3 Analysis: how automated secret scanning prevents incidents

- It shifts secret detection left to developer workstations, before secrets reach remote history.
- It reduces accidental credential exposure risk from copy/paste and test data artifacts.
- It standardizes security checks for all contributors through an enforceable local gate.
- Combined TruffleHog and Gitleaks coverage improves detection depth and lowers false negatives.

## Screenshot Placeholders

Add screenshots at these paths and keep links in this report:

1. `labs/screenshots/lab3-task1-verified-badge.png` - GitHub "Verified" badge for signed commit.
2. `labs/screenshots/lab3-task1-signing-config.png` - Terminal output showing signing config values.
3. `labs/screenshots/lab3-task2-hook-file-and-permissions.png` - `.git/hooks/pre-commit` content/permissions.
4. `labs/screenshots/lab3-task2-blocked-secret-test.png` - Commit blocked when fake secret is staged.
5. `labs/screenshots/lab3-task2-success-after-fix.png` - Commit succeeds after secret removal/redaction.

Optional inline placeholders:

![Task 1 Verified Badge Placeholder](screenshots/lab3-task1-verified-badge.png)
![Task 2 Blocked Secret Test Placeholder](screenshots/lab3-task2-blocked-secret-test.png)
![Task 2 Successful Commit Placeholder](screenshots/lab3-task2-success-after-fix.png)
