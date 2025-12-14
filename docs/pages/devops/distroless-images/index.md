# Distroless Images

Distroless images are minimal docker images that contain only your application and its runtime dependencies. They do not contain os, package managers, shells, or other tools that are typically included in a Linux distribution. This makes distroless images more secure and lightweight than traditional images.

The name "**distroless**" comes from the fact that these images are "less" than a full Linux distribution. They are designed to be used as base images for containerized applications, providing a clean and secure environment for running your code.

There is a misconception that distroless images are not suitable for debugging or troubleshooting because they do not contain debugging tools or shells. However, you can still debug a distroless image using ephemeral containers in Kubernetes or other debugging techniques and this has been explained below.

Distroless images and slim images are similar in that they both aim to reduce the size of the container image by removing unnecessary components. However, slim images still contain an operating system and package manager, while distroless images do not.

## Building distroless image

Below are few examples of how you can build a distroless image for different types of applications:

=== "Java"

    ```Dockerfile title="Dockerfile"  linenums="1" 
    FROM gcr.io/distroless/java21-debian12

    WORKDIR /app
    
    COPY ../../java/bootstrap/target/bootstrap-exec.jar .
    
    CMD ["bootstrap-exec.jar"]
    ```
    ??? tip "Reference"
        You can check the above example in the [paul58914080 / distroless-tryout](https://github.com/paul58914080/distroless-tryout?tab=readme-ov-file#java)

=== "NGINX Static"

    ```Dockerfile title="Dockerfile"  linenums="1"
    FROM nginx:1.27.2 AS base
    
    # https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
    ARG TIME_ZONE
    
    RUN mkdir -p /opt/var/cache/nginx && \
    cp -a --parents /usr/lib/nginx /opt && \
    cp -a --parents /usr/share/nginx /opt && \
    cp -a --parents /var/log/nginx /opt && \
    cp -aL --parents /var/run /opt && \
    cp -a --parents /etc/nginx /opt && \
    cp -a --parents /etc/passwd /opt && \
    cp -a --parents /etc/group /opt && \
    cp -a --parents /usr/sbin/nginx /opt && \
    cp -a --parents /usr/sbin/nginx-debug /opt && \
    cp -a --parents /lib/$(uname -m)-linux-gnu/ld-* /opt && \
    cp -a --parents /lib/$(uname -m)-linux-gnu/libpcre* /opt && \
    cp -a --parents /lib/$(uname -m)-linux-gnu/libz.so.* /opt && \
    cp -a --parents /lib/$(uname -m)-linux-gnu/libc* /opt && \
    cp -a --parents /lib/$(uname -m)-linux-gnu/libdl* /opt && \
    cp -a --parents /lib/$(uname -m)-linux-gnu/libpthread* /opt && \
    cp -a --parents /lib/$(uname -m)-linux-gnu/libcrypt* /opt && \
    cp -a --parents /usr/lib/$(uname -m)-linux-gnu/libssl.so.* /opt && \
    cp -a --parents /usr/lib/$(uname -m)-linux-gnu/libcrypto.so.* /opt && \
    cp /usr/share/zoneinfo/${TIME_ZONE:-ROC} /opt/etc/localtime
    
    FROM gcr.io/distroless/base-debian12
    
    COPY --from=base /opt /
    COPY ../../index.html /usr/share/nginx/html
    
    EXPOSE 80 443
    
    ENTRYPOINT ["nginx", "-g", "daemon off;"]
    ```
    ??? tip "Reference"
        You can check the above example in the [paul58914080 / distroless-tryout](https://github.com/paul58914080/distroless-tryout?tab=readme-ov-file#java)

## Adding certificates to a distroless image

You can modify the above docker file to add certificates to the distroless image. Here's an example of how to add certificates to a distroless image:

=== "With builder image"

    ```Dockerfile title="Dockerfile"  linenums="1" 
    # Stage 1: Build the application
    FROM maven:3.9-eclipse-temurin-21 as builder
    WORKDIR /app
    COPY your-spring-boot-app.jar .
    RUN ./mvnw clean package -DskipTests
    
    # Stage 2: Create the distroless image
    FROM gcr.io/distroless/java21-debian12
    # Copy the built jar file from the builder stage
    COPY --from=builder /app/target/your-spring-boot-app.jar /app/your-spring-boot-app.jar
    # Add certificates
    COPY path/to/your/certificates /etc/ssl/certs
    WORKDIR /app
    ENTRYPOINT ["/usr/lib/jvm/java-11-openjdk/jre/bin/java", "-jar", "your-spring-boot-app.jar"]
    ```

=== "With k8s mounting"

    ```yaml title="k8s/deployment.yaml"  linenums="1" hl_lines="30 33-35 55 58-60"
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: distroless-user
      labels:
        app: distroless-user
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: distroless-user
      template:
        metadata:
          labels:
            app: distroless-user
        spec:
          containers:
            - name: distroless-user
              image: paul58914080/distroless-user:latest
              ports:
                - containerPort: 8080
              securityContext:
                capabilities:
                  drop: - ALL
                runAsNonRoot: true
                allowPrivilegeEscalation: false
                readOnlyRootFilesystem: true
                runAsUser: 1000
                runAsGroup: 1000
              volumeMounts:
                - name: tmp-volume
                  mountPath: /tmp
                - name: java-certs-volume
                  mountPath: /etc/ssl/certs/java-certs
                  subPath: java-certs
              resources:
                requests:
                  memory: "512Mi"
                  cpu: "500m"
                limits:
                  memory: "1Gi"
                  cpu: "1"
              readinessProbe:
                httpGet:
                  path: /actuator/health
                  port: 8080
                initialDelaySeconds: 30
                periodSeconds: 10
              livenessProbe:
                httpGet:
                  path: /actuator/health
                  port: 8080
                initialDelaySeconds: 30
                periodSeconds: 10
          volumes:
            - name: tmp-volume
              emptyDir: {}
            - name: java-certs-volume
              secret:
                secretName: java-certs-secret
    ```

## Debugging a distroless image

Debugging a distroless image can be challenging because it does not contain debugging tools like shells or package managers. However, you can use ephemeral containers in Kubernetes to debug a running distroless image. Ephemeral containers are temporary containers that can be added to a running pod for debugging purposes. They do not persist after the pod is restarted and do not affect the existing containers in the pod.

### Debugging a distroless image using ephemeral containers
To debug a distroless image using an ephemeral container, you can use the following command:

```shell
kubectl debug -it <pod-name> --image=busybox --target=<container-name> -- sh
```

The above command adds an ephemeral container to a running pod and allows you to execute commands in the target container for debugging purposes. Replace `<pod-name>` with the name of the running pod and `<container-name>` with the name of the target container within the pod.

### Debugging a distroless image using share-processes

You can also do it via share-processes:

```shell
kubectl debug <podname> -it --image=busybox share-processes --copy-to=<container-name> -- sh
```

This will allow you to share the processes of the target container and debug it using the busybox container.

