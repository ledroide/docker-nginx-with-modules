# Build custom docker image with additional modules

This project contains a Dockerfile that allows you to create a custom docker
image with any number of additional dynamic modules.

## Building

To build a new docker image it's only necessary to provide the `modules` build
argument with a comma separated list of git repository URLs to be included in
the image. Example:

```
git clone https://github.com/tsuru/docker-nginx-with-modules.git
cd docker-nginx-with-modules
docker build --build-arg modules=https://github.com/vozlt/nginx-module-vts.git:v0.1.17,https://github.com/openresty/echo-nginx-module.git .
```

## nginx version

nginx_version parameter relates to both :
* version downloaded from https://nginx.org/download/
* tag for base image from

## Flavors

Flavors are a way to group a set of modules to generate a custom nginx image.
Flavors can be added by editing the `flavors.json` file and listing the module
URLs.

To build a flavor you can use the provided Makefile:

```bash
make image flavor=proxy nginx_version=1.17.5
```

To build a flavor, `jq` is required, cf. [download section of jq](https://stedolan.github.io/jq/download/)

## variant with proxy logs

This variant logs in file **/var/log/nginx/proxy.log** whereas regular image logs to _stdout_ and _stderr_

First build a regular image as seen below, or use an existing image already built. Then build the proxy log variant.
```bash
BASETAG=1.17.5-0.2-3-gc5935d3 ; docker build -t dockerregistry.questel.fr/systeam/nginx-proxy:${BASETAG}-log --build-arg base_tag=${BASETAG} -f Dockerfile-with-logs .
```

