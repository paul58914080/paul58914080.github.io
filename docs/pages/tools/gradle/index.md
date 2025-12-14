# Gradle

## Install

Install **Gradle** using [SDKMAN](../sdkman/index.md) with the following command:

```
sdk install gradle
```

## Wrapper

The gradle wrapper is an excellent tool to ensure that all developers use the same version of Gradle. It is a set of files that you can commit to your project, and it will download the correct version of Gradle when you run it.

To create a Gradle wrapper, use the following command:

```shell
gradle wrapper
```

This will create the following files:

```shell
gradlew # Unix shell script
gradlew.bat # Windows batch file
gradle/wrapper/gradle-wrapper.jar # Gradle wrapper JAR
gradle/wrapper/gradle-wrapper.properties # Gradle wrapper properties
```

## Usage

### Build

To build a project, run the following command:

```
./gradlew build
```

To skip the tests, run the following command:

```
./gradlew build -x test
```

### Test

To run tests in a project, run the following command:

```
./gradlew test
```

### Debug

To debug a Gradle build, run the following command:

```
./gradlew build --debug
```

### Verify

To verify a project, run the following command:

```
./gradlew check
```

### Dependencies

To list the dependencies of a project, run the following command:

```
./gradlew dependencies
```

### Tasks

To list the tasks of a project, run the following command:

```
./gradlew tasks
```

### Clean

To clean a project, run the following command:

```
./gradlew clean
```

### Publish

To publish a project, run the following command:

```
./gradlew publish
```

### Release

To release a project, run the following command:

```
./gradlew release
```

### Upgrade wrapper

To upgrade the Gradle wrapper, run the following command:

```
./gradlew wrapper --gradle-version [version]
```

