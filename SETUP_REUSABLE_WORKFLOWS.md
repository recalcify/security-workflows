# Setup Reusable Workflows - Automatic Security Scanning

This guide shows you how to add automated security scanning to ANY repository using reusable workflows.

## Quick Start (3 Steps)

### Step 1: Create the Caller Workflow
Create a new file in your repository at `.github/workflows/security.yml`:

```yaml
name: Security Checks
on:
  push:
    branches: [ main, develop, master ]
  pull_request:
    branches: [ main, develop, master ]
  schedule:
    - cron: '0 0 * * 0'

jobs:
  security:
    uses: recalcify/security-workflows/.github/workflows/security-master.yml@main
    secrets: inherit
```

### Step 2: Push the File
```bash
git add .github/workflows/security.yml
git commit -m "Add automated security scanning"
git push
```

### Step 3: Watch it Run
Go to your repository's "Actions" tab and watch the security checks run automatically!

---

## Advanced Options

### Use Individual Workflows

If you prefer to run specific security checks, use them individually:

**Code Security Scanning Only:**
```yaml
jobs:
  code-security:
    uses: recalcify/security-workflows/.github/workflows/code-security.yml@main
    secrets: inherit
```

**Dependency Checking Only:**
```yaml
jobs:
  dependencies:
    uses: recalcify/security-workflows/.github/workflows/dependency-check.yml@main
    secrets: inherit
```

**SAST Analysis Only:**
```yaml
jobs:
  sast:
    uses: recalcify/security-workflows/.github/workflows/sast-analysis.yml@main
    secrets: inherit
```

**Secret Scanning Only:**
```yaml
jobs:
  secrets:
    uses: recalcify/security-workflows/.github/workflows/secret-scanning.yml@main
    secrets: inherit
```

---

## How It Works

### Reusable Workflows
This repository provides "reusable workflows" that can be called from any repository. Here's what happens:

1. You create `.github/workflows/security.yml` in your repository
2. 2. This file references the `security-master.yml` from this repo using `uses:`
   3. 3. When you push or create a PR, GitHub runs your workflow
      4. 4. Your workflow calls our security-master workflow
         5. 5. The master workflow orchestrates all 4 security checks in parallel
            6. 6. Results appear in your GitHub Security tab
              
               7. ### The Workflow Chain
               8. ```
                  Your Repo's security.yml
                      ↓
                  recalcify/security-workflows/security-master.yml
                      ├─→ code-security.yml
                      ├─→ dependency-check.yml
                      ├─→ sast-analysis.yml
                      └─→ secret-scanning.yml
                  ```

                  ---

                  ## Enterprise Setup (Organization-Wide)

                  ### For GitHub Organizations

                  1. **Create an organization workflow repository** (optional):
                  2.    - Create a private repo: `your-org/.github-workflows`
                        -    - Copy workflows there
                             -    - Use: `uses: your-org/.github-workflows/.github/workflows/security-master.yml@main`
                              
                                  - 2. **Use this repo directly** (simplest):
                                    3.    - All repos reference `recalcify/security-workflows`
                                          -    - Centralized updates
                                               -    - No maintenance needed
                                                
                                                    - 3. **Automatic Scanning for All Repos**:
                                                      4.    - Create a GitHub App in your org
                                                            -    - Use `organization-wide workflow repository` feature (GitHub Enterprise)
                                                                 -    - Automatically applies security scanning to all repos
                                                                  
                                                                      - ---

                                                                      ## Customization

                                                                      ### Add Custom Security Rules

                                                                      Create `.github/workflows/custom-security.yml` in your repo:

                                                                      ```yaml
                                                                      name: Custom Security Checks
                                                                      on:
                                                                        push:
                                                                          branches: [ main ]
                                                                        pull_request:
                                                                          branches: [ main ]

                                                                      jobs:
                                                                        custom-checks:
                                                                          runs-on: ubuntu-latest
                                                                          steps:
                                                                            - uses: actions/checkout@v4
                                                                            - name: Your custom security check
                                                                              run: |
                                                                                # Add your custom security checks here
                                                                      ```

                                                                      Then reference both your custom workflow AND the security-master:

                                                                      ```yaml
                                                                      # .github/workflows/security.yml
                                                                      jobs:
                                                                        security-master:
                                                                          uses: recalcify/security-workflows/.github/workflows/security-master.yml@main
                                                                          secrets: inherit

                                                                        custom:
                                                                          needs: security-master
                                                                          uses: ./.github/workflows/custom-security.yml
                                                                      ```

                                                                      ### Exclude Specific Checks

                                                                      Create your own `security.yml` calling only the workflows you need:

                                                                      ```yaml
                                                                      name: Custom Security
                                                                      on:
                                                                        push:
                                                                          branches: [ main ]

                                                                      jobs:
                                                                        code-security:
                                                                          uses: recalcify/security-workflows/.github/workflows/code-security.yml@main
                                                                          secrets: inherit

                                                                        sast:
                                                                          uses: recalcify/security-workflows/.github/workflows/sast-analysis.yml@main
                                                                          secrets: inherit

                                                                        # Note: dependency-check and secret-scanning skipped
                                                                      ```

                                                                      ---

                                                                      ## Viewing Results

                                                                      ### GitHub Security Tab
                                                                      All security findings appear in your repository's **Security** tab:
                                                                      1. Go to your repository
                                                                      2. 2. Click **"Security"** tab
                                                                         3. 3. View **Code scanning**, **Dependabot**, **Secret scanning** results
                                                                           
                                                                            4. ### Workflow Logs
                                                                            5. Monitor individual runs:
                                                                            6. 1. Go to **Actions** tab
                                                                               2. 2. Click the workflow run
                                                                                  3. 3. View detailed logs and any failures
                                                                                    
                                                                                     4. ### Branch Protection
                                                                                     5. Require security checks to pass before merging:
                                                                                     6. 1. Go to **Settings** → **Branches**
                                                                                        2. 2. Select your branch (e.g., `main`)
                                                                                           3. 3. Enable **"Require status checks to pass"**
                                                                                              4. 4. Select the security check jobs
                                                                                                 5. 5. Enable **"Dismiss stale pull request approvals"**
                                                                                                   
                                                                                                    6. ---
                                                                                                   
                                                                                                    7. ## Troubleshooting
                                                                                                   
                                                                                                    8. ### Workflows Not Running?
                                                                                                    9. - ✅ Check **Actions** tab is enabled in Settings
                                                                                                       - - ✅ Verify `.github/workflows/security.yml` is in `main` branch
                                                                                                         - - ✅ Check workflow syntax: `github.com/actions/starter-workflows`
                                                                                                          
                                                                                                           - ### Too Many Failures?
                                                                                                           - - Adjust tool sensitivity in the individual workflow files
                                                                                                             - - Some tools may have false positives
                                                                                                               - - Create `.github/workflows/overrides.yml` to customize
                                                                                                                
                                                                                                                 - ### Permission Denied Errors?
                                                                                                                 - - Workflows use `secrets: inherit` to pass GitHub token
                                                                                                                   - - Check your repo doesn't have strict action restrictions
                                                                                                                     - - Go to **Settings** → **Actions** → **Workflow permissions**
                                                                                                                       - - Set to "Read and write permissions"
                                                                                                                        
                                                                                                                         - ### Workflows Too Slow?
                                                                                                                         - - Remove unnecessary jobs in your copy of `security.yml`
                                                                                                                           - - Or use specific workflows instead of `security-master.yml`
                                                                                                                             - - Jobs run in parallel, so total time = longest job
                                                                                                                              
                                                                                                                               - ---
                                                                                                                               
                                                                                                                               ## For Multiple Repositories
                                                                                                                               
                                                                                                                               ### Batch Setup Script
                                                                                                                               
                                                                                                                               Create a script to add workflows to all your repos:
                                                                                                                               
                                                                                                                               ```bash
                                                                                                                               #!/bin/bash
                                                                                                                               REPOS=("repo1" "repo2" "repo3")

                                                                                                                               for repo in "${REPOS[@]}"; do
                                                                                                                                 cd ~/$repo
                                                                                                                                 mkdir -p .github/workflows
                                                                                                                                 cat > .github/workflows/security.yml << 'EOF'
                                                                                                                               name: Security Checks
                                                                                                                               on:
                                                                                                                                 push:
                                                                                                                                   branches: [ main, develop ]
                                                                                                                                 pull_request:
                                                                                                                                   branches: [ main, develop ]
                                                                                                                                 schedule:
                                                                                                                                   - cron: '0 0 * * 0'

                                                                                                                               jobs:
                                                                                                                                 security:
                                                                                                                                   uses: recalcify/security-workflows/.github/workflows/security-master.yml@main
                                                                                                                                   secrets: inherit
                                                                                                                               EOF
                                                                                                                                 git add .github/workflows/security.yml
                                                                                                                                 git commit -m "Add automated security scanning"
                                                                                                                                 git push
                                                                                                                               done
                                                                                                                               ```
                                                                                                                               
                                                                                                                               ---
                                                                                                                               
                                                                                                                               ## About Permissions
                                                                                                                               
                                                                                                                               All workflows use **least-privilege permissions**:
                                                                                                                               - `contents: read` - Only reads your code
                                                                                                                               - - `security-events: write` - Only writes security findings
                                                                                                                                 - - Never modifies your code or settings
                                                                                                                                  
                                                                                                                                   - The `secrets: inherit` passes GitHub's automatic `GITHUB_TOKEN` only.
                                                                                                                                  
                                                                                                                                   - ---
                                                                                                                                   
                                                                                                                                   ## Support & Updates
                                                                                                                                   
                                                                                                                                   - 📖 **Documentation**: See main `README.md`
                                                                                                                                   - - 🐛 **Issues**: Report problems in `security-workflows` repo
                                                                                                                                     - - 🔄 **Updates**: Automatically get latest tools (always uses `@main`)
                                                                                                                                       - - 🤝 **Contributing**: Contributions welcome!
                                                                                                                                        
                                                                                                                                         - ---
                                                                                                                                         
                                                                                                                                         ## Security Notes
                                                                                                                                         
                                                                                                                                         These workflows will detect:
                                                                                                                                         - ✅ Script injection attempts
                                                                                                                                         - - ✅ Known vulnerable dependencies
                                                                                                                                           - - ✅ Secrets and credentials
                                                                                                                                             - - ✅ Code quality issues
                                                                                                                                               - - ✅ Common security patterns
                                                                                                                                                
                                                                                                                                                 - But they should be part of a comprehensive security strategy including:
                                                                                                                                                 - - Code reviews
                                                                                                                                                   - - Dependency management
                                                                                                                                                     - - Access controls
                                                                                                                                                       - - Incident response plans
                                                                                                                                                         - - Regular security audits
