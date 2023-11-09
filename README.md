# sbom.sh-create
SBOM.sh container analysis and scan using Grype provided as a GitHub Action
# SBOM.sh GitHub Action

This GitHub Action integrates with [sbom.sh](https://sbom.sh/) to generate and upload Software Bill of Materials (SBOM) for your projects. Utilizing the `codenotary/sbom.sh` container image, this action supports various open-source SBOM tools such as Trivy, Grype, and Syft.

## Features

- Generate SBOMs for filesystems and container images.
- Support for multiple scanning tools (Trivy, Grype, Syft).
- Upload SBOMs directly to sbom.sh and obtain a shareable URL.
- Optional vulnerability scanning and SBOM score calculation at sbom.sh.

## Workflow Dispatch Options

This action can be triggered manually using the `workflow_dispatch` event. You can provide the following inputs:

- `scan_type`: The type of scan to perform, such as `trivyfs`, `trivyimage`, `grypefs`, `grypeimage`, `syftfs`, or `syftimage`.
- `target`: The target for the scan, which is applicable for image scans.

When you navigate to the Actions tab of your GitHub repository, you will see an option to "Run workflow". You can select the branch you want to run the workflow on and provide the required inputs.


## Usage

To use this action, add the following workflow to your `.github/workflows` directory:

```yaml
name: "Generate and Upload SBOM"

on: [push, pull_request, workflow_dispatch]

jobs:
  generate_sbom:
    runs-on: ubuntu-latest
    name: "SBOM Generation"
    steps:
      # ... workflow steps here
```

Customize your workflow by specifying the scanning tool and target using the `workflow_dispatch` event.

### Workflow example

```yaml
name: "Generate and Upload SBOM"

on: [push, pull_request, workflow_dispatch]

jobs:
  generate_sbom:
    runs-on: ubuntu-latest
    name: "SBOM Generation"
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v2
      
      - name: Generate SBOM
        id: sbom_generation
        run: |
          RESPONSE=$(docker run --rm -v ${{ github.workspace }}:/app codenotary/sbom.sh:latest grypefs)
          echo "SBOM_RESPONSE=$RESPONSE" >> $GITHUB_ENV
      
      - name: Extract ShareUrl
        run: |
          SHARE_URL=$(echo $SBOM_RESPONSE | jq -r '.ShareUrl')
          echo "SBOM_SHARE_URL=$SHARE_URL" >> $GITHUB_ENV
      
      - name: Output SBOM URL
        run: echo "The SBOM can be found at $SBOM_SHARE_URL"    
      
      - name: Comment on Pull Request
        if: github.event_name == 'pull_request'
        uses: actions/github-script@v3
        with:
          github-token: ${{secrets.GITHUB_TOKEN}}
          script: |
            github.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: "Generated SBOM is available at: ${{ env.SBOM_SHARE_URL }}"
            })
```

## Inputs
- `scan_type`: Type of scan (trivyfs, trivyimage, grypefs, grypeimage, syftfs, syftimage).
- `target`: Scan target (applicable for image scans).

## About sbom.sh

sbom.sh is a service that allows you to generate a valid Software Bill-of-Materials (SBOM) in CycloneDX or SPDX format. It provides a centralized platform to store SBOMs and facilitates better tracking and analysis of your software components.

For detailed documentation on the sbom.sh service and GitHub Action, visit https://sbom.sh or https://hub.docker.com/repository/docker/codenotary/sbom.sh/general.

## License
This GitHub Action and corresponding scripts are released under the Apache2 License. See LICENSE for details.

## Contributing
Contributions are welcome!