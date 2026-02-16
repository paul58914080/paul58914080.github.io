# JDK (Java Development Kit)

## Install

Install **Java** using [SDKMAN](../sdkman/index.md) with the following command:

```shell
sdk install java 21.0.2-tem
```

## Usage

### Version listing

To list all available Java versions, use:
```shell
sdk list java
```

To list all installed and local Java versions, use:
```shell
sdk list java | awk 'NR<=5 || tolower($0) ~ /installed|local only/'
```

!!!info

    The above command lists the first 5 lines of the output from `sdk list java` and then filters the remaining lines to show only those that contain "installed" or "local only", ignoring case. This way, you can see the available versions as well as the ones you have installed or are available locally.

### Version switching

To use a specific Java version, use:
```shell
sdk use java 21.0.2-tem
```

To set a specific Java version as the default, use:
```shell
sdk default java 21.0.2-tem
```
## Configuration

### Certificates

To list the certificates in the keystore, use the following command:

```shell
keytool -list -keystore [keystore] -storepass [storepass]
```

To add a certificate to the keystore, use the following command:

```shell
keytool -import -alias [alias] -keystore [keystore] \ 
        -storepass [storepass] -file [certificate]
```

