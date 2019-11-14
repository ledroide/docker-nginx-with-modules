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

Nginx_version parameter relates to both :
* version downloaded from (https://nginx.org/download/)
* tag for perl variant of base image from (https://hub.docker.com/_/nginx?tab=tags)
It is usually the same value, but it is better to check before.

```bash
export NGINXVERSION=1.17.5
```

## flavors

Flavors are a way to group a set of modules to generate a custom nginx image.
Flavors can be added by editing the `flavors.json` file and listing the module
URLs.

To build a flavor you can use the provided Makefile:

```bash
make image flavor=proxy nginx_version=${NGINXVERSION}
```

To build a flavor, `jq` is required, cf. [download section of jq](https://stedolan.github.io/jq/download/)

## default image

Default image repo/name is *dockerregistry.questel.fr/systeam/nginx-$(flavor)* (depending on which flavor is chosen.)

## default tag

Default tag is made of nginx version, git tag and last commit, using `git describe --tag`.
It can be overwritten at _make_ step with *image_tag* variable.

## variant with proxy logs

This variant logs in file **/var/log/nginx/proxy.log** whereas regular image logs to _stdout_ and _stderr_

First build a regular image as seen below, or use an existing image already built. Then build the proxy log variant.
```bash
export GITTAG=$(git describe --tag)    #  should be the same as regular image
export BASETAG=${NGINXVERSION}-${GITTAG}
docker build -t dockerregistry.questel.fr/systeam/nginx-proxy:${BASETAG}-log --build-arg base_tag=${BASETAG} -f Dockerfile-with-logs .
```

## docker push
```bash
docker push dockerregistry.questel.fr/systeam/nginx-proxy:${BASETAG}
docker push dockerregistry.questel.fr/systeam/nginx-proxy:${BASETAG}-log
```

## links
* [original tsuru/docker-nginx-with-modules repo](https://github.com/tsuru/docker-nginx-with-modules.git)
* [nginx logging guide](https://docs.nginx.com/nginx/admin-guide/monitoring/logging/)
