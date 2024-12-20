# Holos Runner

This runner image is for speeding up workflows.

## Manual publishing

Log into ghcr.io

```bash
gh auth token | docker login ghcr.io -u $(gh api user --jq .login) --password-stdin
```

Configure buildx if you haven't.  See OrbStack [Multi-platform builds].

```bash
# Create a parallel multi-platform builder
docker buildx create --name mybuilder --use
# Make "buildx" the default
docker buildx install
# Build for multiple platforms
docker build --platform linux/amd64,linux/arm64 .
```

Build and push the image, remove the tags you don't want to push to.  Takes
about 6 minutes on my M3 Max.

```bash
docker build --platform linux/amd64,linux/arm64 --push \
  -t quay.io/holos-run/runner:latest \
  -t ghcr.io/holos-run/runner:latest \
  .
```

[Multi-platform builds]: https://docs.orbstack.dev/docker/images#multiplatform
