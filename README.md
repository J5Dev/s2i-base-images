# Ubuntu S2I Base Docker Image

This repository contains a Dockerfile that serves as the base image with all essential libraries and tools needed for OpenShift language images.

## Installation and Usage

This image is available on DockerHub. To pull the latest image (18.04), run:

```
docker pull j5dev/s2i-base-ubuntu18.04
```

To build the base image for 18.04 from scratch, run:

```
cd s2i-base-ubuntu/18.04
make build
```

## Test

This repository includes the S2I test framework, which launches a simple test to make sure the image builds and runs properly.

```
cd s2i-base-ubuntu/18.04
make test
```
