# Filler Repository

[![Version](https://img.shields.io/docker/v/fnndsc/pl-publish-test?sort=semver)](https://hub.docker.com/r/fnndsc/pl-publish-test)
[![MIT License](https://img.shields.io/github/license/fnndsc/pl-publish-test)](https://github.com/FNNDSC/pl-publish-test/blob/main/LICENSE)
[![ci](https://github.com/FNNDSC/pl-publish-test/actions/workflows/ci.yml/badge.svg)](https://github.com/FNNDSC/pl-publish-test/actions/workflows/ci.yml)

`pl-publish-test` is a dummy  [_ChRIS_](https://chrisproject.org/) plugin to test and showcase the process of publishing plugins to the ChRIS store.

## Publishing to ChRIS Store
1. Create a new repository in the FNNDSC organization Github
1. Use the python-chrisapp-template
1. Clone your repository `git clone https://github.com/FNNDSC/pl-publish-test.git`
1. Delete line 44 and uncomment line 45 in `pl-publish-test/.github/workflows/ci.yml` [Optional] To enable testing, delete line 22: `if: false  # delete this line to enable automatic testing`
1. `git tag v1.0.0`
1. `git push origin v1.0.0`
1. Monitor the workflow at https://github.com/FNNDSC/pl-publish-test/actions 
1. Once the build is completed, view it at https://app.chrisproject.org/catalog

## Installation

`pl-publish-test` is a _[ChRIS](https://chrisproject.org/) plugin_, meaning it can
run from either within _ChRIS_ or the command-line.

## Local Usage

To get started with local command-line usage, use [Apptainer](https://apptainer.org/)
(a.k.a. Singularity) to run `pl-publish-test` as a container:

```shell
apptainer exec docker://fnndsc/pl-publish-test commandname [--args values...] input/ output/
```

To print its available options, run:

```shell
apptainer exec docker://fnndsc/pl-publish-test commandname --help
```

## Examples

`commandname` requires two positional arguments: a directory containing
input data, and a directory where to create output data.
First, create the input directory and move input data into it.

```shell
mkdir incoming/ outgoing/
mv some.dat other.dat incoming/
apptainer exec docker://fnndsc/pl-publish-test:latest commandname [--args] incoming/ outgoing/
```

## Development

Instructions for developers.

### Building

Build a local container image:

```shell
docker build -t localhost/fnndsc/pl-publish-test .
```

### Running

Mount the source code `commandname.py` into a container to try out changes without rebuild.

```shell
docker run --rm -it --userns=host -u $(id -u):$(id -g) \
    -v $PWD/commandname.py:/usr/local/lib/python3.12/site-packages/commandname.py:ro \
    -v $PWD/in:/incoming:ro -v $PWD/out:/outgoing:rw -w /outgoing \
    localhost/fnndsc/pl-publish-test commandname /incoming /outgoing
```

### Testing

Run unit tests using `pytest`.
It's recommended to rebuild the image to ensure that sources are up-to-date.
Use the option `--build-arg extras_require=dev` to install extra dependencies for testing.

```shell
docker build -t localhost/fnndsc/pl-publish-test:dev --build-arg extras_require=dev .
docker run --rm -it localhost/fnndsc/pl-publish-test:dev pytest
```

## Release

Steps for release can be automated by [Github Actions](.github/workflows/ci.yml).
This section is about how to do those steps manually.

### Increase Version Number

Increase the version number in `setup.py` and commit this file.

### Push Container Image

Build and push an image tagged by the version. For example, for version `1.2.3`:

```
docker build -t docker.io/fnndsc/pl-publish-test:1.2.3 .
docker push docker.io/fnndsc/pl-publish-test:1.2.3
```

### Get JSON Representation

Run [`chris_plugin_info`](https://github.com/FNNDSC/chris_plugin#usage)
to produce a JSON description of this plugin, which can be uploaded to _ChRIS_.

```shell
docker run --rm docker.io/fnndsc/pl-publish-test:1.2.3 chris_plugin_info -d docker.io/fnndsc/pl-publish-test:1.2.3 > chris_plugin_info.json
```

Intructions on how to upload the plugin to _ChRIS_ can be found here:
https://chrisproject.org/docs/tutorials/upload_plugin

# Test
