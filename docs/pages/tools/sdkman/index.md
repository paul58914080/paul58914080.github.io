# SDKMAN

## Install

```
curl -s "https://get.sdkman.io" | bash
```

Follow the on-screen instructions to wrap up the installation. 

```
source "$HOME/.sdkman/bin/sdkman-init.sh"
```

Current running sdkman version

```
sdk version
```
## Usage

### Install

Install a package with the following command:

```
sdk install [package] [version]
```

`[version]` is optional. If not provided, the latest version will be installed.


Remove a package

```
sdk uninstall [package] [version]
```

### List 

List all available packages

```
sdk list
```

List all installed packages

```
sdk current
```

List all available versions of a package

```
sdk list [package]
```

### Use

Use a specific version of a package

```
sdk use [package] [version]
```

### Default

Set a default version of a package

```
sdk default [package] [version]
```

### Current

Display the current version of a package

```
sdk current [package]
```

### Env

You want to switch between different versions of a package in different project. This can be achieved through an **`.sdkmanrc`** file in the base directory of your project. This file can be generated automatically by issuing the following command: 

```
sdk env init
```

After checking out a new project, you may be missing some SDKs specified in the project's .sdkmanrc file. To install these missing SDKs, just type:

```
sdk env install
```

When leaving a project, you may want to reset the SDKs to their default version. This can be achieved by entering:

```
sdk env clear
```

### Upgrade

To see what is outdated for all Candidates:

```
sdk upgrade [package]
```

`[package]` is optional. If not provided, all candidates to be upgraded will be listed.

### Update

To update the candidates new version or being added or removed, run:

```
sdk update
```

### Home

To open the home location of a package

```
sdk home [package]
```

## References

- [SDKMAN! Documentation](https://sdkman.io/)