---
title: AWS ECS Task failure due to CannotPullContainerError
date: 2025-02-17 16:08:55
layout:     post
subtitle: "\" AWS ECS Task failure due to CannotPullContainerError \""
author:     "Nithin VR"
tags:
    - AWS
    - ECS
    - Docker
---

## Problem
Suddenly in our AWS ECS cluster tasks are failing due `CannotPullContainerError`. The error message was not very helpful. It only said `ref pull has been retried 1 time(s): number of layers and diffIDs don't  match: 3 != 15`.

## Debugging
1. Pulled the images locally and inspected the image and it has 15 layers as mentioned in the Error. But somehow only 3 is pulled. I am not sure where are we losing the image layers.
2. Verified there is no connectivity issue between our ECS cluster and our image repo.
3. Updated our docker build action `docker/build-push-action` in Github pipeline to `v6` from `v5`. No luck.

## Actual Issue
Underlying issue was ECS currently does not support mixed compression types within the same image manifest. Our base image recently start using `zstd` for docker layer compression and our build was still using old `gzip` . So the final image has both type of layers which was not supported by the AWS ECS. This was figured out by comparing the docker manifest of working docker image and breaking docker image.
We used `docker manifest inspect your-image:tag` to inspect and compare the docker images. Once we identified that 

This was our image layers:
```json
{
  "layers": [
    {
      "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
      // Base image layer
    },
    {
      "mediaType": "application/vnd.docker.image.rootfs.diff.tar.zstd",
      // Your new layer - which caused problems
    }
  ]
}
```
## Fix
To make sure we are also using the `zstd` for docker layer compression similar to our base image we updated our Github action to build docker. By passing outputs `outputs: type=image,oci-mediatypes=true,compression=zstd,compression-level=3,force-compression=true` it forced the docker build to use `zstd` instead of default `gzip`;
```yml
uses: docker/build-push-action@v6
with:
    context: .
    file: Dockerfile
    push: true
    tags: ${{ steps.release-tags.outputs.release-tags }}
    outputs: type=image,oci-mediatypes=true,compression=zstd,compression-level=3,force-compression=true
```
