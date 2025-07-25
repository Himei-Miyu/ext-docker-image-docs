<!--

********************************************************************************

WARNING:

    DO NOT EDIT "composer/README.md"

    IT IS AUTO-GENERATED

    (from the other files in "composer/" combined with a set of templates)

********************************************************************************

-->

# Quick reference

-	**Maintained by**:  
	[Rob Bast](https://github.com/alcohol), with [contributions](https://github.com/composer/docker/graphs/contributors) from the community.

-	**Where to get help**:  
	[the Docker Community Slack](https://dockr.ly/comm-slack), [Server Fault](https://serverfault.com/help/on-topic), [Unix & Linux](https://unix.stackexchange.com/help/on-topic), or [Stack Overflow](https://stackoverflow.com/help/on-topic)

# Supported tags and respective `Dockerfile` links

-	[`2.8.10`, `2.8`, `2`, `latest`](https://github.com/composer/docker/blob/1992e740d9584be61ea3130e18193276cf989cf5/latest/Dockerfile)

-	[`2.2.25`, `2.2`](https://github.com/composer/docker/blob/a037fe423a4fef8030b2a8c3131da0934a6295dd/2.2/Dockerfile)

-	[`1.10.27`, `1.10`, `1`](https://github.com/composer/docker/blob/a037fe423a4fef8030b2a8c3131da0934a6295dd/1.10/Dockerfile)

# Quick reference (cont.)

-	**Where to file issues**:  
	[https://github.com/composer/docker/issues](https://github.com/composer/docker/issues?q=)

-	**Supported architectures**: ([more info](https://github.com/docker-library/official-images#architectures-other-than-amd64))  
	[`amd64`](https://hub.docker.com/r/amd64/composer/), [`arm32v6`](https://hub.docker.com/r/arm32v6/composer/), [`arm32v7`](https://hub.docker.com/r/arm32v7/composer/), [`arm64v8`](https://hub.docker.com/r/arm64v8/composer/), [`i386`](https://hub.docker.com/r/i386/composer/), [`ppc64le`](https://hub.docker.com/r/ppc64le/composer/), [`riscv64`](https://hub.docker.com/r/riscv64/composer/), [`s390x`](https://hub.docker.com/r/s390x/composer/)

-	**Published image artifact details**:  
	[repo-info repo's `repos/composer/` directory](https://github.com/docker-library/repo-info/blob/master/repos/composer) ([history](https://github.com/docker-library/repo-info/commits/master/repos/composer))  
	(image metadata, transfer size, etc)

-	**Image updates**:  
	[official-images repo's `library/composer` label](https://github.com/docker-library/official-images/issues?q=label%3Alibrary%2Fcomposer)  
	[official-images repo's `library/composer` file](https://github.com/docker-library/official-images/blob/master/library/composer) ([history](https://github.com/docker-library/official-images/commits/master/library/composer))

-	**Source of this description**:  
	[docs repo's `composer/` directory](https://github.com/docker-library/docs/tree/master/composer) ([history](https://github.com/docker-library/docs/commits/master/composer))

# What is Composer?

Composer is a tool for dependency management in PHP, written in PHP. It allows you to declare the libraries your project depends on and it will manage (install/update) them for you.

You can read more about Composer in our [official documentation](https://getcomposer.org/doc/).

![logo](https://raw.githubusercontent.com/docker-library/docs/58f7363e6cfa78f8cd54af16eab51c63c1232002/composer/logo.png)

# How to use this image

### Basic usage

```console
$ docker run --rm --interactive --tty \
  --volume $PWD:/app \
  composer <command>
```

### Persist cache / global configuration

You can bind mount the Composer home directory from your host to the container to enable a persistent cache or share global configuration:

```console
$ docker run --rm --interactive --tty \
  --volume $PWD:/app \
  --volume ${COMPOSER_HOME:-$HOME/.composer}:/tmp \
  composer <command>
```

**Note:** this relies on the fact that the `COMPOSER_HOME` value is set to `/tmp` in the image by default.

Or if your environment follows the XDG specification:

```console
$ docker run --rm --interactive --tty \
  --env COMPOSER_HOME \
  --env COMPOSER_CACHE_DIR \
  --volume ${COMPOSER_HOME:-$HOME/.config/composer}:$COMPOSER_HOME \
  --volume ${COMPOSER_CACHE_DIR:-$HOME/.cache/composer}:$COMPOSER_CACHE_DIR \
  --volume $PWD:/app \
  composer <command>
```

### Filesystem permissions

By default, Composer runs as root inside the container. This can lead to permission issues on your host filesystem. You can work around this by running the container with a different user:

```console
$ docker run --rm --interactive --tty \
  --volume $PWD:/app \
  --user $(id -u):$(id -g) \
  composer <command>
```

See: https://docs.docker.com/engine/reference/run/#user for details.

> Note: Docker for Mac behaves differently and this tip might not apply to Docker for Mac users.

### Private repositories / SSH agent

When you need to access private repositories, you will either need to share your configured credentials, or mount your `ssh-agent` socket inside the running container:

```console
$ eval $(ssh-agent); \
  docker run --rm --interactive --tty \
  --volume $PWD:/app \
  --volume $SSH_AUTH_SOCK:/ssh-auth.sock \
  --env SSH_AUTH_SOCK=/ssh-auth.sock \
  composer <command>
```

**Note:** On OSX this requires Docker For Mac v2.2.0.0 or later, see [docker/for-mac#410](https://github.com/docker/for-mac/issues/410).

When combining the use of private repositories with running Composer as another user, you can run into non-existent user errors (thrown by ssh). To work around this, bind mount the host passwd and group files (read-only) into the container:

```console
$ eval $(ssh-agent); \
  docker run --rm --interactive --tty \
  --volume $PWD:/app \
  --volume $SSH_AUTH_SOCK:/ssh-auth.sock \
  --volume /etc/passwd:/etc/passwd:ro \
  --volume /etc/group:/etc/group:ro \
  --env SSH_AUTH_SOCK=/ssh-auth.sock \
  --user $(id -u):$(id -g) \
  composer <command>
```

# Troubleshooting

### PHP version & extensions

Our image is aimed at quickly running Composer without the need for having a PHP runtime installed on your host. You should not rely on the PHP version in our container. We do not provide a Composer image for each supported PHP version because we do not want to encourage using Composer as a base image or a production image.

We try to deliver an image that is as lean as possible, built for running Composer only. Sometimes dependencies or Composer [scripts](https://getcomposer.org/doc/articles/scripts.md) require the availability of certain PHP extensions.

Suggestions:

-	(optimal) create your own build image and [install](https://getcomposer.org/doc/faqs/how-to-install-composer-programmatically.md) Composer inside it.

	**Note:** Docker 17.05 introduced [multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build/), simplifying this enormously:

	```dockerfile
	COPY --from=composer /usr/bin/composer /usr/bin/composer
	```

-	(alternatively) specify the target [platform](https://getcomposer.org/doc/06-config.md#platform) / extension(s) in your `composer.json`:

	```json
	{
	  "config": {
	    "platform": {
	      "php": "MAJOR.MINOR.PATCH",
	      "ext-something": "MAJOR.MINOR.PATCH"
	    }
	  }
	}
	```

-	(discouraged) pass the [`--ignore-platform-reqs`](https://getcomposer.org/doc/03-cli.md#install-i) and / or `--no-scripts` flags to `install` or `update`:

	```console
	$ docker run --rm --interactive --tty \
	  --volume $PWD:/app \
	  composer install --ignore-platform-reqs --no-scripts
	```

# License

View [license information](https://github.com/composer/composer/blob/master/LICENSE) for the software contained in this image.

As with all Docker images, these likely also contain other software which may be under other licenses (such as Bash, etc from the base distribution, along with any direct or indirect dependencies of the primary software being contained).

Some additional license information which was able to be auto-detected might be found in [the `repo-info` repository's `composer/` directory](https://github.com/docker-library/repo-info/tree/master/repos/composer).

As for any pre-built image usage, it is the image user's responsibility to ensure that any use of this image complies with any relevant licenses for all software contained within.
