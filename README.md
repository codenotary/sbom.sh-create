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

To use this action, add the following to your `.github/workflows` directory in a file like `sbom-analysis.yml`:

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
        uses: actions/sbom-to-sbom-sh@main
        with:
          scan_type: 'grypefs' # Or other supported types like 'trivyfs', 'syftfs', etc.
          target: '' # If needed for the scan type, specify the target here.

      - name: Output SBOM URL
        run: echo "The SBOM can be found at ${{ steps.sbom_generation.outputs.sbom_url }}"
      
      # Additional steps like commenting on a PR can be added here
      
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