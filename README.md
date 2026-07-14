[![StepSecurity Maintained Action](https://raw.githubusercontent.com/step-security/maintained-actions-assets/main/assets/maintained-action-banner.png)](https://docs.stepsecurity.io/actions/stepsecurity-maintained-actions)

# GitHub Actions job_id parser

GitHub Action to get the current workflow run's job_id

This GitHub Action depends on the GitHub REST API Version: 2022-11-28.

https://docs.github.com/ja/rest/actions/workflow-jobs?apiVersion=2022-11-28

## Inputs

### `github_token`

**Optional** auth token to use with GitHub API

By default `github.token` context value (same as `secrets.GITHUB_TOKEN`) is used. You can use your own `PAT` if prefered.
Token requires `actions: read` scope.

### `job_name`

**Required** `jobs.<job-id>.name` of tartget workflow jobs

If no name specified, use `jobs.<job-id>` instead. Note that `<job-id>` != job_id.  
If you are using this actions with matrix workflow, check the example section.


```yaml
jobs:
  my_first_job:         # this is jobs.<job-id>
    name: My first job  # this is jobs.<job-id>.name
  my_second_job:        # this is jobs.<job-id>
    name: My second job # this is jobs.<job-id>.name
```

* `jobs.<job-id>`: https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_id
* `jobs.<job-id>.name`: https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idname

### `repository`

**Optional**: target GitHub repository

default: `github.repository` context value

### `run_id`

run_id of target workflow run

default: `${GITHUB_RUN_ID}`

###  `per_page`

Results per page (max 100) of target workflow run

default: 30

https://docs.github.com/en/rest/actions/workflow-jobs?apiVersion=2022-11-28#list-jobs-for-a-workflow-run-attempt--parameters

__If there are more than 30 jobs for the target workflow, set this parameter.__

## Outputs

### `job_id`

job_id of target workflow jobs

### `html_url`

html_url of target workflow jobs

## Permissions

The job using this GitHub Actions must have the `actions:read` permission.

## Example usage

### Simple example 1

```yaml
name: CI
on:
  push:
permissions:
  actions: read
  contents: read # required by actions/checkout
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout action
        uses: actions/checkout@v7
      - name: Some Scripts
        run: echo "do something here"
      - name: Get Current Job Log URL
        uses: step-security/gha-jobid-action@v0
        id: jobs
        with:
          job_name: "build" # input job.<job-id>
          #job_name: "${{ github.job }}"  # if job.<job-id>.name is not specified, this works too
      - name: Output Current Job Log URL
        run: echo ${{ steps.jobs.outputs.html_url }}
```

### Simple example 2

```yaml
name: CI
on:
  push:
permissions:
  actions: read
  contents: read # required by actions/checkout
jobs:
  build:
    name: Build and Test
    runs-on: ubuntu-latest
    steps:
      - name: Checkout action
        uses: actions/checkout@v7
      - name: Some Scripts
        run: echo "do something here"
      - name: Get Current Job Log URL
        uses: step-security/gha-jobid-action@v0
        id: jobs
        with:
          job_name: "Build and Test" # input job.<job-id>.name here. job.<job-id> won't works.
      - name: Output Current Job Log URL
        run: echo ${{ steps.jobs.outputs.html_url }}
```

### Matrix example

```yaml
name: CI
on:
  push:
permissions:
  actions: read
  contents: read # required by actions/checkout
jobs:
  build:
    strategy:
      matrix:
        distro: [alpha, beta, gamma]
    runs-on: ubuntu-latest
    steps:
      - name: Checkout action
        uses: actions/checkout@v7
      - name: Some Scripts
        run: echo "do something here ${{ matrix.distro }}"
      - name: Get Current Job Log URL
        uses: step-security/gha-jobid-action@v0
        id: jobs
        with:
          job_name: "build (${{ matrix.distro }})" # input job.<job-id>.name and matrix here.
          per_page: 50 # input matrix size here if it is larger than 30
      - name: Output Current Job Log URL
        run: echo ${{ steps.jobs.outputs.html_url }}
```

## License

Copyright (c) 2020-2024 Daisuke Sato
Copyright (c) 2026 StepSecurity.

This repository is licensed under the MIT License, see [LICENSE](./LICENSE).  
Unless attributed otherwise, everything in this repository is licensed under the MIT license.

