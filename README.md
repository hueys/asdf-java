# asdf-java

[![Update Java release data](https://github.com/hueys/asdf-java/actions/workflows/update-data.yaml/badge.svg)](https://github.com/hueys/asdf-java/actions/workflows/update-data.yaml)
[![asdf-java Tests](https://github.com/hueys/asdf-java/actions/workflows/tests.yml/badge.svg)](https://github.com/hueys/asdf-java/actions/workflows/tests.yml)

[Java](https://www.java.com/en/) plugin for the [asdf](https://github.com/asdf-vm/asdf) version manager.

## About this fork

This is a personal fork of [halcyon/asdf-java](https://github.com/halcyon/asdf-java), the original plugin. Changes from upstream:

- **Temurin only.** `asdf list all java` / `asdf install java` only offer [Eclipse Temurin](https://adoptium.net/temurin/) builds, instead of upstream's full multi-vendor list (adoptopenjdk, zulu, liberica, graalvm, corretto, etc.).
- **`asdf latest java` works.** Upstream has no `bin/latest-stable`, so `asdf latest java` / `asdf install java latest` (no query) fail outright on it — asdf core's fallback requires version strings to start with a digit, and every version this plugin lists is vendor-prefixed. This fork implements it.
- **CI hardened.** GitHub Actions workflows are scanned with [zizmor](https://docs.zizmor.sh/) and all third-party actions are pinned to commit SHAs; [Dependabot](https://docs.github.com/en/code-security/dependabot) keeps those pins up to date.

## Requirements
- [bash v5.0](https://www.gnu.org/software/bash/)
- [curl](https://curl.haxx.se/)
- [sha256sum](https://www.gnu.org/software/coreutils/) (only on Linux)
- [unzip](http://infozip.sourceforge.net/UnZip.html)
- [jq](https://jqlang.github.io/jq/) (only for updating the release data)

## Install

```
asdf plugin add java https://github.com/hueys/asdf-java.git
```

## Use

Check [asdf](https://asdf-vm.github.io/asdf/) for instructions on how to install & manage versions of Java.

_Note: this plugin only offers [Eclipse Temurin](https://adoptium.net/temurin/) builds._

## Install

List candidate JDKs:

```
asdf list all java
```

Install a candidate listed from the previous command like this:

```
asdf install java temurin-11.0.16+8
```

### Setting a version

Select an installed candidate for use like this:

```
asdf set -u java temurin-11.0.16+8
```
or just for the local directory
```
asdf set java temurin-11.0.16+8
```

### Latest

If you just want the latest of a major version (without worrying about the patch number), install it like this:

```
asdf install java latest:temurin-11
```

Without a query, `asdf install java latest` (and `asdf latest java`) resolves to the latest plain JDK build, excluding `jre`/`musl`/etc. variants.

Similarly, you can set the global/local version. The latest tag will resolve to the most recent version.

Running `asdf local java latest:temurin-11` will result in the following:

```shell
$ cat .tool-versions
java temurin-11.0.16+8
```

### Early Access builds

If you want to use Early Access builds (they allow you to prepare for the next version of the JDK but they are not stable releases), you can set `java_release_type` in `.asdfrc`, the supported values are:
- `ga`: You can only install GA (General Availability / stable) releases (default)
- `ea`: You can only install EA (Early Access / unstable) builds
- `all`: You can install both GA and EA builds

After you set the value (e.g.: `java_release_type=all`), you need to prune the cache of asdf-java so that asdf will refresh the available versions to install:
```shell
export ASDF_JAVA_CACHE_DIR="${TMPDIR:-/tmp}/asdf-java.cache/"
ls -al "$ASDF_JAVA_CACHE_DIR"
rm -rf "$ASDF_JAVA_CACHE_DIR"
```

After these steps, `asdf list-all java` should show you the type of builds you set and you can install them.

## JAVA_HOME
To set `JAVA_HOME` in your shell's initialization add the following:

`. ~/.asdf/plugins/java/set-java-home.bash`

For zsh shell, instead use:

`. ~/.asdf/plugins/java/set-java-home.zsh`

For fish shell, instead use:

`. ~/.asdf/plugins/java/set-java-home.fish`

For nushell shell, instead use:

`source ~/.asdf/plugins/java/set-java-home.nu`

For xonsh shell, instead use:

`source ~/.asdf/plugins/java/set-java-home.xsh`

## macOS

### `JAVA_HOME` integration

Some applications in macOS use `/usr/libexec/java_home` to set java home.

Setting `java_macos_integration_enable` to yes on `.asdfrc` file enables this integration.

```
java_macos_integration_enable=yes
```

_Note: Temurin packages offer this integration._

### Apple Silicon integration

If you have an Apple Silicon mac, then you can choose to run either an `arm64` JVM natively, or an `x86_64` JVM under Rosetta translation. If you run the command `arch`, it will print either `arm64` (which means you are running natively) or `x86_64` (which means you are running under Rosetta translation).

When you run `asdf list all java`, it lists only the VMs which are available for the architecture you are currently running under. To switch your terminal from native ARM to Rosetta use `arch -x86_64 /bin/zsh`.
