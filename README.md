# Security Workflows

A comprehensive collection of GitHub Actions workflows designed to detect and prevent script injection, malicious code, secrets, and other security vulnerabilities in your codebase.

## Overview

This repository contains four powerful security scanning workflows that work together to provide defense-in-depth security testing:

### 1. Code Security Scanning (code-security.yml)

Detects common coding vulnerabilities and malicious patterns.

**Tools:**
- **Trivy** - Container and filesystem vulnerability scanner
- - **git-secrets** - Prevents secrets from being committed
  - - **Custom pattern detection** - Detects dangerous functions like eval(), exec(), raw_input()
   
    - **Triggers:** Push and Pull Request events
   
    - ### 2. Dependency and Supply Chain Check (dependency-check.yml)
   
    - Identifies known vulnerabilities in project dependencies.
   
    - **Tools:**
    - - **OWASP Dependency-Check** - Detects known vulnerabilities in dependencies
      - - **npm audit** - Checks npm package vulnerabilities
        - - **Python Safety** - Checks Python package vulnerabilities
          - - **Package integrity verification** - Ensures packages haven't been tampered with
           
            - **Triggers:** On dependency file changes, weekly schedule
           
            - ### 3. SAST and Code Analysis (sast-analysis.yml)
           
            - Static Application Security Testing using multiple analysis engines.
           
            - **Tools:**
            - - **GitHub CodeQL** - Deep code analysis across multiple languages (JavaScript, Python, Java)
              - - **Semgrep** - Pattern-based code scanning (OWASP Top 10, CWE Top 25)
                - - **Bandit** - Python security issue scanner
                  - - **ESLint Security Plugin** - JavaScript security rules
                   
                    - **Triggers:** Push and Pull Request events
                   
                    - ### 4. Secret Scanning and Credential Detection (secret-scanning.yml)
                   
                    - Prevents credentials and secrets from being exposed in the codebase.
                   
                    - **Tools:**
                    - - **GitLeaks** - Scans for leaked secrets, API keys, and credentials
                      - - **TruffleHog** - Deep secret scanning across git history
                        - - **detect-secrets** - Baseline-based secret detection
                         
                          - **Triggers:** Push and Pull Request events
                         
                          - ## Getting Started
                         
                          - ### Prerequisites
                         
                          - - GitHub repository
                            - - GitHub Actions enabled
                              - - (Optional) Create GitHub Secrets for API keys if using custom tools
                               
                                - ### Installation
                               
                                - 1. Copy the workflow files from `.github/workflows/` directory to your repository's `.github/workflows/` directory
                                  2. 2. Create a `.secrets.baseline` file in your repo root (for detect-secrets):
                                     3.    ```bash
                                              detect-secrets scan --baseline .secrets.baseline
                                              git add .secrets.baseline
                                              ```
                                           3. Commit and push to enable the workflows
                                       
                                           4. ### Configuration
                                       
                                           5. Most workflows run automatically on push and pull request events. Customize triggers by editing the `on:` section in each workflow file:
                                       
                                           6. ```yaml
                                              on:
                                                push:
                                                  branches: [ main, develop ]
                                                pull_request:
                                                  branches: [ main, develop ]
                                                schedule:
                                                  - cron: '0 0 * * 0'  # Weekly on Sunday
                                              ```

                                              ## Workflow Details

                                              ### Permissions

                                              Each workflow uses least-privilege permissions:
                                              - `contents: read` - Read repository content
                                              - - `security-events: write` - Write security findings (SARIF)
                                               
                                                - ### Output and Reporting
                                               
                                                - - All SARIF reports are uploaded to GitHub Security tab
                                                  - - Failed checks block PRs if branch protection is enabled
                                                    - - Check runs provide detailed violation information
                                                     
                                                      - ## Best Practices
                                                     
                                                      - 1. **Enable branch protection** - Require status checks to pass before merging
                                                        2. 2. **Review findings** - Check GitHub Security tab regularly
                                                           3. 3. **Fix issues promptly** - Address vulnerabilities before deployment
                                                              4. 4. **Keep tools updated** - Workflows use latest action versions
                                                                 5. 5. **Set up alerts** - Enable repository security alerts
                                                                   
                                                                    6. ## Common Issues and Solutions
                                                                   
                                                                    7. ### Issue: Workflow fails immediately
                                                                    8. **Solution:** Ensure Actions are enabled in repository settings
                                                                   
                                                                    9. ### Issue: No scan results
                                                                    10. **Solution:** Check the workflow run logs for specific error messages
                                                                   
                                                                    11. ### Issue: False positives
                                                                    12. **Solution:** Adjust tool configurations in the workflow files
                                                                   
                                                                    13. ## Extending the Workflows
                                                                   
                                                                    14. To add custom security checks:
                                                                   
                                                                    15. ```yaml
                                                                        - name: Custom security check
                                                                          run: |
                                                                            # Your custom script here
                                                                        ```

                                                                        ## Supported Languages

                                                                        - JavaScript/TypeScript
                                                                        - - Python
                                                                          - - Java
                                                                            - - Go
                                                                              - - And more through CodeQL and Semgrep
                                                                               
                                                                                - ## Resources
                                                                               
                                                                                - - [GitHub Actions Documentation](https://docs.github.com/en/actions)
                                                                                  - - [CodeQL Documentation](https://codeql.github.com/)
                                                                                    - - [Semgrep Documentation](https://semgrep.dev/docs/)
                                                                                      - - [OWASP Top 10](https://owasp.org/www-project-top-ten/)
                                                                                       
                                                                                        - ## License
                                                                                       
                                                                                        - MIT License - see LICENSE file for details
                                                                                       
                                                                                        - ## Contributing
                                                                                       
                                                                                        - Feel free to submit issues and enhancement requests!
                                                                                       
                                                                                        - ## Support
                                                                                       
                                                                                        - For issues or questions, please check GitHub Issues or create a new issue in this repository.
