Get Latest Release
==================

A simple Github action to get the latest release from another repository.

Configuration
=============

Example Repository - https://github.com/pozetroninc/github-action-get-latest-release

**owner**: The Github user or organization that owns the repository. e.g. pozetroninc
**repo**:  The repository name. e.g. github-action-get-latest-release

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
      - id: timeseries
        uses: pozetroninc/github-action-get-latest-release@master
        with:
            owner: RedisTimeSeries
            repo: RedisTimeSeries
      - uses: actions/checkout@v2
      - uses: docker/build-push-action@v1
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
          repository: pozetroninc/keydb-timeseries
          dockerfile: timeseries.dockerfile
          build_args: KEY_DB_VERSION=${{ steps.keydb.outputs.release }}, REDIS_TIME_SERIES_VERSION=${{ steps.timeseries.outputs.release }}
          tags: latest, ${{ steps.keydb.outputs.release }}_${{ steps.timeseries.outputs.release }}

```
