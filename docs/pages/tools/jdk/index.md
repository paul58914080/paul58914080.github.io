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

## Version switching

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

