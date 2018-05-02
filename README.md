# InspIRCd

[![Build Status](https://travis-ci.org/inspircd/inspircd-docker.svg?branch=master)](https://travis-ci.org/inspircd/inspircd-docker)

InspIRCd is a modular Internet Relay Chat (IRC) server written in C++ for Linux, BSD, Windows and Mac OS X systems which was created from scratch to be stable, modern and lightweight.

InspIRCd is one of only a few IRC servers to provide a tunable number of features through the use of an advanced but well-documented module system. By keeping core functionality to a minimum we hope to increase the stability, security, and speed of InspIRCd while also making it customizable to the needs of many different users.

# IMPORTANT NOTE

This branch is for a testing/preview image of InspIRCd 3.0

It doesn't provide a usable config by default. It is not made for use in production.

Running it always requires a bindmount or volume at `/inspircd/conf` which holds your configs.

Example:

```console
docker build -t inspircd/inspircd-docker:alpha .

docker run --name inspircd-testing -p 6697:6697 -v "$PWD/conf:/inspircd/conf" inspircd/inspircd-docker:alpha
```


## TLS Configuration

### Using self-generated certificates

This container image generates a self-signed TLS certificate on start-up as long as none exists. To use this container with TLS enabled:

```console
$ docker run --name inspircd -p 6667:6667 -p 6697:6697 inspircd/inspircd-docker
```

You can customize the self-signed TLS certificate using the following environment variables:

|Available variables      |Default value                   |Description                                   |
|-------------------------|--------------------------------|----------------------------------------------|
|`INSP_TLS_CN`            |`irc.example.com`               |Common name of the certificate                |
|`INSP_TLS_MAIL`          |`nomail@example.com`            |Mail address represented in the certificate   |
|`INSP_TLS_UNIT`          |`Server Admins`                 |Unit responsible for the service              |
|`INSP_TLS_ORG`           |`Example IRC Network`           |Organisation name                             |
|`INSP_TLS_LOC`           |`Example City`                  |City name                                     |
|`INSP_TLS_STATE`         |`Example State`                 |State name                                    |
|`INSP_TLS_COUNTRY`       |`XZ`                            |Country Code by [ISO 3166-1 ](https://en.wikipedia.org/wiki/ISO_3166-1)|
|`INSP_TLS_DURATION`      |`365`                           |Duration until the certificate expires        |


This will generate a self-signed certificate for `irc.example.org` instead of `irc.example.com`:

```console
$ docker run --name inspircd -p 6667:6667 -p 6697:6697 -e "INSP_TLS_CN=irc.example.org" inspircd/inspircd-docker
```

### Using secrets

We provide the ability to use `secrets` with this image to place a certificate to your nodes.

**Docker version 1.13 is required and [secrets are only supported in swarm mode](https://docs.docker.com/engine/swarm/secrets/)**

```console
docker secret create irc.key /path/to/your/ircd.key
docker secret create inspircd.crt /path/to/your/ircd.crt

docker service create --name inspircd --secret source=irc.key,target=inspircd.key,mode=0400 --secret inspircd.crt inspircd/inspircd-docker
```

Notice the syntax `--secret source=irc.key,target=inspircd.key` allows you to name a secret in a way you like.

Currently used secrets:

* `inspircd.key`
* `inspircd.crt`

# Build extras

To build extra modules you can use the `--build-arg` statement.

Available build arguments:

|Argument            |Description                                                              |
|--------------------|-------------------------------------------------------------------------|
|`VERSION`           |Version of InspIRCd. Uses `-b`-parameter from `git clone`                |
|`CONFIGUREARGS`     |Additional Parameters. Used to enable core extras like `m_geoip.cpp`     |
|`EXTRASMODULES`     |Additional Modules from [inspircd-extras](https://github.com/inspircd/inspircd-extras/tree/master/2.0) repository like `m_geoipban`|
|`BUILD_DEPENDENCIES`|Additional packages which are only needed during compilation             |
|`RUN_DEPENDENCIES`  |Additional packages which are needed to run InspIRCd                     |

```console
docker build --build-arg "BUILD_DEPENDENCIES=geoip-dev pcre-dev" --build-arg "RUN_DEPENDENCIES=geoip pcre" --build-arg "CONFIGUREARGS=--enable-extras=m_geoip.cpp --enable-extras=m_regex_pcre.cpp"  --build-arg "EXTRASMODULES=m_geoipban" inspircd-docker
```

## Building additional modules

In case you want to develop InspIRCd modules, it is useful to run InspIRCd with modules which neither exist in core modules nor in extras.

You can put the sources these modules in the modules directory of this repository. They are automatically copied to the modules directory of InspIRCd.

It also allows you to overwrite modules.

Make sure you install all needed dependencies using `ADDPACKAGES`.


# Updates and updating

To update your setup simply pull the newest image version from docker hub and run it.

```console
docker pull inspircd/inspircd-docker
```

We automatically build our images weekly to include the current state of modern libraries.

Considering to update your docker setup regularly.

# License

View [license information](https://github.com/inspircd/inspircd) for the software contained in this image.

# Supported Docker versions

This image is officially supported on Docker version 17.06.0-CE.

Support for older versions (down to 1.12) is provided on a best-effort basis.

Please see [the Docker installation documentation](https://docs.docker.com/installation/) for details on how to upgrade your Docker daemon.

# User Feedback

## Issues

If you have any problems with or questions about this image, please contact us through a [GitHub issue](https://github.com/inspircd/inspircd-docker/issues).

You can also reach many of the project maintainers via the `#inspircd` IRC channel on [Chatspike](https://chatspike.net).

## Contributing

You are invited to contribute new features, fixes, or updates, large or small; we are always thrilled to receive pull requests and do our best to process them as fast as we can.
