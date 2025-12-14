# Maven

## Install

Install **Maven** using [SDKMAN](../sdkman/index.md) with the following command:

```
sdk install maven
```

## Wrapper

The Maven Wrapper is an excellent tool to ensure that all developers use the same version of Maven. It is a set of files that you can commit to your project, and it will download the correct version of Maven when you run it.

To create the Maven Wrapper, run the following command:

```
mvn -N wrapper:wrapper
```

We can also specify the version of Maven to use:

```
mvn -N wrapper:wrapper -Dmaven=3.8.1
```

> **Note**: The option `-N` means non-recursive, and it tells Maven to execute the command only in the current project and not in the modules.

## Usage

### Build

To build a project, run the following command:

```shell
mvnw clean install
```

To skip the tests, run the following command:

```shell
mvnw clean install -DskipTests
```
### Test

To run test in a project, run the following command:

```shell
mvnw test
```

### Debug

To debug a Maven build, run the following command:

```shell
mvnw clean install -X
```

### Verify

To verify mvn package is valid and meets quality criteria of a project, run the following command:

```shell
mvnw clean verify
```

### Package

To package a project, run the following command:

```shell
mvnw clean package
```

### Deploy

To deploy a project, run the following command:

```shell
mvnw clean deploy
```

### Release

To release a project, run the following command:

```shell
mvnw release:prepare
mvnw release:perform
```

### Upgrade wrapper

To upgrade the Maven Wrapper, run the following command:

```shell
./mvnw -N wrapper:wrapper -Dmaven=3.8.7
```

## Update dependencies

To display the new versions of the dependencies in a project, run the following command:

```
mvnw versions:display-dependency-updates
```

To update the dependencies in a project, run the following command:

```shell
mvnw versions:use-latest-versions
mvnw versions:update-properties # Update the properties
```

To display the new versions of the plugins in a project, run the following command:

```
mvnw versions:display-plugin-updates
```

## Tips & tricks

### Skip tests

To skip the tests during the build, run the following command:

```shell
mvnw clean install -DskipTests
```

### Reduce verbosity

To reduce the verbosity of the Maven output, run the following command:

```shell
mvnw clean install -q
```

Sometimes you may notice output like this:

```
Downloading: http://.../artifactory/repo/com/codahale/metrics/metrics-core/3.0.1/metrics-core-3.0.1.jar
4/2122 KB   
8/2122 KB   
12/2122 KB   
16/2122 KB   
18/2122 KB   
18/2122 KB   4/480 KB   
18/2122 KB   8/480 KB   
18/2122 KB   12/480 KB   
18/2122 KB   16/480 KB   
18/2122 KB   16/480 KB   4/1181 KB   
18/2122 KB   16/480 KB   8/1181 KB   
18/2122 KB   16/480 KB   12/1181 KB
```

To reduce the verbosity of the download progress, run the following command:

```shell
mvnw clean install -ntp
```

```shell
mvnw clean install --no-transfer-progress
```