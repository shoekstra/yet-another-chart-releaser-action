# `yacr` GitHub Action

A GitHub action to turn a GitHub project into a self-hosted Helm chart repo, using [stecky/yet-another-chart-releaser](https://github.com/stecky/yet-another-chart-releaser) CLI tool.

<h1 align="center">
  <a href="https://github.com/stecky/yet-another-chart-releaser"><img src="https://github.com/stecky/yet-another-chart-releaser-action/blob/main/.github/yacr_logo.png" alt="yacr - Chart Releaser"></a>
  <br>
</h1>

## Usage

### Pre-requisites

1. A GitHub repo containing a directory with your Helm charts (eg: `/charts`)
1. A GitHub branch called `gh-pages` to store the published charts. See `charts_repo_url` for alternatives
1. Create a workflow `.yml` file in your `.github/workflows` directory. An [example workflow](#example-workflow) is available below.
  For more information, reference the GitHub Help Documentation for [Creating a workflow file](https://help.github.com/en/articles/configuring-a-workflow#creating-a-workflow-file)

### Inputs

For more information on inputs, see the [API Documentation](https://developer.github.com/v3/repos/releases/#input)

- `version`: The chart-releaser version to use (default: v0.1.8)
- `config`: Optional config file for chart-releaser
- `charts_dir`: The charts directory
- `charts_repo_url`: The GitHub Pages URL to the charts repo (default: `https://<owner>.github.io/<project>`)
- `packages_with_index`: Save a copy of the package files to the GitHub pages branch and reference them in the index

### Example Workflow

Create a workflow (eg: `.github/workflows/release.yml`):

```yaml
name: Release Charts

on:
  push:
    branches:
      - main

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          fetch-depth: 0

      - name: Configure Git
        run: |
          git config user.name "$GITHUB_ACTOR"
          git config user.email "$GITHUB_ACTOR@users.noreply.github.com"

      - name: Install Helm
        uses: azure/setup-helm@v1
        with:
          version: v3.4.0

      - name: Run chart-releaser
        uses: stecky/yet-another-chart-releaser-action@v1
        env:
          YACR_TOKEN: "${{ secrets.GITHUB_TOKEN }}"
```

This uses [@stecky/yet-another-chart-releaser-action](https://www.github.com/stecky/yet-another-chart-releaser-action) to turn your GitHub project into a self-hosted Helm chart repo.
It does this – during every push to `main` – by checking each chart in your project, and whenever there's a new chart version, creates a corresponding [GitHub release](https://help.github.com/en/github/administering-a-repository/about-releases) named for the chart version, adds Helm chart artifacts to the release, and creates or updates an `index.yaml` file with metadata about those releases, which is then hosted on GitHub Pages

## Code of conduct

Participation in this project is governed by the [Code of Conduct](CODE_OF_CONDUCT.md).
