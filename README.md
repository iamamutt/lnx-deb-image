# lnx-deb-image

<!-- 
#> git tag -a v1.0.0 -m "GitHub Actions Initial Workflow"
#> git push origin v1.0.0

#> git tag -d v1.0.0
#> git push --delete origin v1.0.0
-->

Testing simultaneous docker hub and ghcr workflows.

## Docker compose 

`docker-compose up --detach`


## Manual Docker build

Build using the command below. Make sure to change the platform to whatever you need, e.g., `linux/arm64`, `linux/amd64`, etc...

```bash
cd build/debian
docker build \
    --file=Dockerfile \
    --platform=linux/arm64 \
    --output=type=docker \
    --tag=lnx-deb-image:v0.0.0a \
    --build-arg IMAGE_CREATED=2021-11-11T11:11:11Z \
    --build-arg IMAGE_VERSION=v0.0.0a \
    --build-arg USER_NAME=joseph \
    --build-arg USER_GROUP=docker \
    .
```
