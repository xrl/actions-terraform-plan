# GitHub Actions: Terraform PR

GitHub Action for adding `terraform plan` output as a PR comment.


## Usage

You must sent `on: pullrequest` or else the GitHub workflow will not have all the metadata required for posting back.

```yaml
on: pullrequest
[[[ SNIP ]]]
      - uses: tchupp/actions-terraform-pr@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          apply-branch: "main"
```

### Assumptions

The most common cause for issues involves a mismatch in expectations.  
Please read below to make sure your setup aligns with the assumptions made by this action.

#### Setup

This action expects that the repo has been checked out and terraform has been set up with the correct version.  
The following snippet from an example job, [which can be found below](#simple-setup).

```yaml
...
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v2
        with:
          terraform_version: ~1.0.0
          cli_config_credentials_token: ${{ secrets.TF_TOKEN }}
...
```

### Inputs

Assumes all the TF is in the root repo, for now.

## Examples

### Simple Setup

This setup assumes you have your terraform files at the top level of your repo, and you only have a single workspace:
```bash
$ tree
.
├── main.tf
└── versions.tf

0 directories, 2 files
```

In your `.github/workflows/terraform.yml` file you would have:
```yaml
on:
  pull_request:
    branches:
      - main
    paths:
      - '**.tf'
      - '.github/workflows/terraform.yml'

jobs:
  terraform:
    name: "Terraform"
    runs-on: ubuntu-latest
    env:
      TF_WORKSPACE: "default"
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v2
        with:
          terraform_version: ~1.0.0
          cli_config_credentials_token: ${{ secrets.TF_TOKEN }}

      - uses: tchupp/actions-terraform-pr@v1
        with:
          apply-branch: "main"
```

### Forked from tchupp/actions-terraform-pr

I couldn't get the tcupp version to work, turns out I didn't set the `on: pull_request` in my workflow and a simple branch push wasn't enough.

But if you need more features go check out: https://github.com/tchupp/actions-terraform-pr

For now, this forked action will stand as a dead simple alternative.