# Release a maven project

## GPG

### Commands

Install `gpg` with `homebrew`

```bash
brew install gpg
```

Generate key

```bash
gpg --full-gen-key
```

To list the generated keys

```bash
gpg --list-keys
```

!!! tip "Tip: Finding your GPG Key ID"

     To find your GPG **Key ID**, run:
     
     ```bash
     gpg --list-keys
     ```
     
     Example output:
     
     ```
     /home/mylocaluser/.gnupg/pubring.kbx
     ------------------------------------
     pub   rsa3072 2021-06-23 [SC] [expires: 2023-06-23]
           CA925CD6C9E8D064FF05B4728190C4130ABA0F98
     uid           [ultimate] Central Repo Test <central@example.com>
     sub   rsa3072 2021-06-23 [E] [expires: 2023-06-23]
     ```
     
     The long hexadecimal value: **CA925CD6C9E8D064FF05B4728190C4130ABA0F98** is your **GPG Key ID** (fingerprint) used in signing configuration.

Push your public key to the server

```bash
gpg --keyserver keyserver.ubuntu.com --send-keys <KEY-ID>
```

or

```bash
gpg --keyserver keys.openpgp.org send-keys <KEY-ID>
```

Generate key ring that will be used to signin our artifacts

Export private key in the form of a string

```bash
gpg --armor --export-secret-keys <KEY-ID> | pbcopy
```

```bash
echo “<the copied key from above>” | gpg —dearmor > ~/my_secring.gpg
```

Your GPG public key (exported as an ASCII-armored string)

```bash
gpg --armor --export <KEY-ID> > public.key
```

Your GPG private key (exported as an ASCII-armored string)

```bash
gpg --armor --export-secret-keys <KEY-ID> > private.key
```

## Maven Central Deployment

### Setup

1. [Register to Publish Via the Central Portal](https://central.sonatype.org/register/central-portal/#register-to-publish-via-the-central-portal)
2. [Register or choose namespace](https://central.sonatype.org/register/central-portal/#register-to-publish-via-the-central-portal)

### Configure `pom.xml`

Add the following configuration to your `pom.xml` file:

```xml title="pom.xml"

<profiles>
  <profile>
    <id>makeRelease</id>
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-source-plugin</artifactId>
          <version>${maven-source-plugin.version}</version>
          <executions>
            <execution>
              <id>attach-sources</id>
              <goals>
                <goal>jar-no-fork</goal>
              </goals>
            </execution>
          </executions>
        </plugin>

        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-javadoc-plugin</artifactId>
          <version>${maven-javadoc-plugin.version}</version>
          <configuration>
            <encoding>UTF-8</encoding>
          </configuration>
          <executions>
            <execution>
              <id>attach-javadoc</id>
              <goals>
                <goal>jar</goal>
              </goals>
            </execution>
          </executions>
        </plugin>

        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-gpg-plugin</artifactId>
          <version>${maven-gpg-plugin.version}</version>
          <executions>
            <execution>
              <id>sign-artifacts</id>
              <phase>verify</phase>
              <goals>
                <goal>sign</goal>
              </goals>
            </execution>
          </executions>
          <configuration>
            <passphrase>${env.GPG_PASSPHRASE}</passphrase>
            <gpgArguments>
              <arg>--pinentry-mode</arg>
              <arg>loopback</arg>
            </gpgArguments>
          </configuration>
        </plugin>

        <!-- ✨ NEW Sonatype Central Publishing Plugin -->
        <plugin>
          <groupId>org.sonatype.central</groupId>
          <artifactId>central-publishing-maven-plugin</artifactId>
          <version>${central-publishing-maven-plugin.version}</version>
          <extensions>true</extensions>
          <configuration>
            <publishingServerId>central</publishingServerId>
            <autoPublish>true</autoPublish>
            <waitUntil>published</waitUntil>
          </configuration>
        </plugin>

      </plugins>
    </build>
  </profile>
</profiles>
```

### Github workflow for release

Pre-requisites:

- The following secrets are configured in your GitHub repository:
  - `CENTRAL_USERNAME` - Your Sonatype OSSRH username.
  - `CENTRAL_PASSWORD` - Your Sonatype OSSRH password.
  - `GPG_PRIVATE_KEY` - Your GPG private key (exported as an ASCII-armored string).
  - `GPG_PASSPHRASE` - The passphrase for your GPG key.

The pipeline will be triggered on every new release published in the GitHub repository. The version
will be extracted from the tag name (assuming the tag follows the pattern `vX.Y.Z`). The idea is to
build the project using Maven, sign the artifacts with GPG, and deploy them to the Maven Central
Repository.

```yaml title=".github/workflows/release.yml"
name: Publish package to Maven Central Repository

on:
  release:
    types: [ published ]

jobs:
  publish:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout sources
        uses: actions/checkout@v6
        with:
          fetch-depth: 0

      - name: Extract version from tag
        id: version
        run: |
          raw_tag_name="${GITHUB_REF_NAME}"
          # Extract last x.y.z pattern
          version=$(echo "$raw_tag_name" | grep -oE '[0-9]+\.[0-9]+\.[0-9]+$')
          echo "VERSION=$version" >> $GITHUB_ENV

      - name: Set up JDK 25 with GPG
        uses: actions/setup-java@v5
        with:
          distribution: temurin
          java-version: 25
          gpg-private-key: ${{ secrets.GPG_PRIVATE_KEY }}
          gpg-passphrase: MAVEN_GPG_PASSPHRASE
          server-id: central
          server-username: CENTRAL_USERNAME
          server-password: CENTRAL_PASSWORD

      - name: Build & Deploy to Staging
        env:
          CENTRAL_USERNAME: ${{ secrets.CENTRAL_USERNAME }}
          CENTRAL_PASSWORD: ${{ secrets.CENTRAL_PASSWORD }}
          MAVEN_GPG_PASSPHRASE: ${{ secrets.GPG_PASSPHRASE }}
        run: |
          # Build and deploy artifacts (signed)
          echo "🚀 Building release version: $VERSION"
          mvn -B clean deploy -P makeRelease -DskipTests -Drevision="$VERSION" -ntp
```

### Reference

- [https://central.sonatype.org/publish/requirements/gpg/](https://central.sonatype.org/publish/requirements/gpg/)
- [Ultimate Guide on Publishing KMP Library on a New Sonatype Central Platform](https://www.youtube.com/watch?v=NPUehp4KpSs)
- [deepaksorthiya/java-maven-github-and-mavencentral-deploy](https://github.com/deepaksorthiya/java-maven-github-and-mavencentral-deploy/tree/main)
- [A Practical Example with OSSRH and the Central Repository (OSS)](https://www.youtube.com/watch?v=N8_2-hpTnFA&t=2s)
- [Subscribe to - Sonatype youtube channel](https://www.youtube.com/c/Sonatypeinc/videos)
