# Example 1: Secure PATH Handling
# Filename: .github/workflows/secure-path.yml
```
name: Secure Path Handling Demo
on:
  issues:
    types: [opened]

jobs:
  process-issue:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
        
      - name: Process issue
        run: |
          # SECURE: Using predefined paths rather than user input
          echo "PATH=$PATH:./vendor/bin" >> $GITHUB_ENV
          echo "Processing issue with custom path"
          run-tool
```

# Example 2: Secure Environment Variable Handling
# Filename: .github/workflows/secure-envvar.yml
```
name: Secure Environment Variable Demo
on:
  pull_request:
    types: [opened]

jobs:
  process-pr:
    runs-on: ubuntu-latest
    steps:
      - name: Set env vars
        run: |
          # SECURE: Validate and sanitize user input
          PR_TITLE="${{ github.event.pull_request.title }}"
          # Remove special characters and validate
          SANITIZED_OPTIONS=$(echo "$PR_TITLE" | tr -cd '[:alnum:]-_ ')
          echo "CONFIG_OPTIONS=$SANITIZED_OPTIONS" >> $GITHUB_ENV
          
      - name: Use variables
        run: |
          echo "Using config options: $CONFIG_OPTIONS"
          ./configure $CONFIG_OPTIONS
```

# Example 3: Secure Script Execution
# Filename: .github/workflows/secure-code-execution.yml
```
name: Secure Code Execution Demo
on:
  issues:
    types: [opened]

jobs:
  run-script:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
        
      # SECURE: Never executing user-provided content directly
      - name: Process user input safely
        run: |
          echo "Processing issue body"
          # Store user input in a file for processing
          echo "${{ github.event.issue.body }}" > issue_body.txt
          # Use a trusted script to process the input
          ./safe_processor.sh issue_body.txt
```
# Example 4: Secure Artifact Handling
# Filename: .github/workflows/secure-artifact.yml
```
name: Secure Artifact Handling Demo
on:
  workflow_dispatch:
    inputs:
      artifact_name:
        description: 'Name of artifact to download'
        required: true
        type: choice
        options:
          - build-artifact
          - test-results
          - documentation

jobs:
  download-and-use:
    runs-on: ubuntu-latest
    steps:
      - name: Verify artifact name
        id: verify
        run: |
          # SECURE: Validate against allowed list
          ALLOWED_ARTIFACTS=("build-artifact" "test-results" "documentation")
          REQUESTED="${{ github.event.inputs.artifact_name }}"
          VALID=false
          
          for allowed in "${ALLOWED_ARTIFACTS[@]}"; do
            if [ "$allowed" = "$REQUESTED" ]; then
              VALID=true
              break
            fi
          done
          
          if [ "$VALID" = "true" ]; then
            echo "::set-output name=artifact_name::$REQUESTED"
          else
            echo "Invalid artifact requested"
            exit 1
          fi
      
      - name: Download artifact
        uses: actions/download-artifact@v2
        with:
          name: ${{ steps.verify.outputs.artifact_name }}
          path: ./downloaded
          
      - name: Verify artifact integrity
        run: |
          cd ./downloaded
          # SECURE: Verify checksum before executing
          sha256sum -c checksum.txt
          if [ $? -eq 0 ]; then
            chmod +x ./script.sh
            ./script.sh
          else
            echo "Artifact verification failed!"
            exit 1
          fi
```
# Example 5: Secure PR Handling
# Filename: .github/workflows/secure-pr-handling.yml
```
name: Secure PR Handling Demo
on:
  pull_request_target:
    types: [opened, synchronize]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      # SECURE: First checkout the base code (not the PR)
      - name: Checkout base code
        uses: actions/checkout@v3
      
      # SECURE: Only perform limited operations with user content
      - name: Process PR metadata
        run: |
          echo "Processing PR #${{ github.event.pull_request.number }}"
          echo "Title: ${{ github.event.pull_request.title }}"
          # Store PR information in a file for processing
          echo "${{ toJSON(github.event.pull_request) }}" > pr_metadata.json
          # Process using trusted code from the base branch
          ./scripts/validate_pr.sh pr_metadata.json
          
      # Only checkout PR code in a separate job with limited permissions
      # if initial validation passes
```
