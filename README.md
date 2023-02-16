Get Latest Release
==================

A simple Github action to get the latest release from another repository. No authentication required.

Configuration
=============

Example Repository - https://github.com/pozetroninc/github-action-get-latest-release

**Inputs**

Name | Description | Example
--- | --- | ---
owner | The Github user or organization that owns the repository |  pozetroninc
repo | The repository name | github-action-get-latest-release

**or**
Name | Description | Example
--- | --- | ---
repository | The repository name in full | pozetroninc/github-action-get-latest-release

**Additional Inputs (Optional)**
Name | Description | Example
--- | --- | ---
excludes | Exclude draft or pre-release versions. | "prerelease, draft"
token | GitHub token or personal access token | `${{ secrets.GITHUB_TOKEN }}` or `${{ secrets.PERSONAL_ACCESS_TOKEN }}`

Using the `GITHUB_TOKEN` will avoid the action [failing due to hitting API rate limits](https://github.com/pozetroninc/github-action-get-latest-release/issues/24) from the IP address of the GitHub runner your action is running on. Using a `PERSONAL_ACCESS_TOKEN` is required to get the release information from a private repo. You can read about [how to create a personal access token here](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) and how to [add this as a repository secret here](https://docs.github.com/en/github/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets).

**Outputs**

Name | Description | Example
--- | --- | ---
release | The latest release version tag | v0.3.0
id | The latest release version id | 12345
description | The latest release description body | This is an example release

Usage Example
=============
``` yaml
name: Build Docker Images
on: [push, repository_dispatch]

jobs:
  build:
    name: RedisTimeSeries
    runs-on: ubuntu-latest
    steps:
      - id: keydb
        uses: pozetroninc/github-action-get-latest-release@master
        with:
          owner: JohnSully
          repo: KeyDB
          excludes: prerelease, draft
      - id: timeseries
        uses: pozetroninc/github-action-get-latest-release@master
        with:
          repository: RedisTimeSeries/RedisTimeSeries
      - uses: actions/checkout@v3
      - uses: docker/build-push-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
          repository: pozetroninc/keydb-timeseries
          dockerfile: timeseries.dockerfile
          build_args: KEY_DB_VERSION=${{ steps.keydb.outputs.release }}, REDIS_TIME_SERIES_VERSION=${{ steps.timeseries.outputs.release }}
          tags: latest, ${{ steps.keydb.outputs.release }}_${{ steps.timeseries.outputs.release }}

```

To use the current repo:
``` yaml
with:
  repository: ${{ github.repository }}
```

To use authentication token:
``` yaml
with:
  token: ${{ secrets.GITHUB_TOKEN }}
```
