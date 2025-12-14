# Node

## Install

Install **Node.js via nvm** using [Homebrew](../../mac/homebrew/index.md) with the following command:

```shell
brew install nvm
```

`nvm` allows you to quickly install and use different versions of node via the command line.

## Usage

### Install
Latest version of Node.js can be installed using the following command:

```
nvm install node
```

To install a specific version of Node.js, use the following command:

```
nvm install [version]
```

### Uninstall

To uninstall a specific version of Node.js, use the following command:

```
nvm uninstall [version]
```

### List

You can list available versions using the following command:

```
nvm ls-remote
```

You can list installed versions using the following command:

```
nvm ls
```

### Use
You can switch between versions using the following command:

```
nvm use [version]
```


### Which
You can get the path to the installed version using the following command:

```
nvm which [version]
```

> **Note:** You can use argument `--lts` to install or uninstall or use or list the latest LTS version of Node.js.