# Issue
Compare the duration of  
- `=> [build  5/12] RUN swift package resolve`: 30s vs. 550s
- `=> [build  7/12] RUN swift build -c release --static-swift-stdlib`: 125s vs. ♾️

The step `[build  7/12]`, I cancelled it after 830s. In my first experience it failed on its own.

## Environment

- Apple M2 Pro
- Ventura 13.4.1 (c)
- `fly v0.1.67 darwin/arm64 Commit: b4b52efbd4e0b8f392ce0b9521f772a4dceea3fe BuildDate: 2023-07-31T17:19:24Z`
    - Installed via `curl -L https://fly.io/install.sh | sh`

# Output

## Normal Local Build 

```bash
docker-compose up app --build

[+] Building 160.6s (23/23) FINISHED
 => [app internal] load build definition from Dockerfile                                                                                                0.0s
 => => transferring dockerfile: 2.77kB                                                                                                                  0.0s
 => [app internal] load .dockerignore                                                                                                                   0.0s
 => => transferring context: 95B                                                                                                                        0.0s
 => [app internal] load metadata for docker.io/library/ubuntu:jammy                                                                                     1.4s
 => [app internal] load metadata for docker.io/library/swift:5.8-jammy                                                                                  1.0s
 => [app build  1/12] FROM docker.io/library/swift:5.8-jammy@sha256:49b1d6bdb9f968e42493f91f09a9488c46701790d9f647eb76b191b659c8a35b                    0.0s
 => [app stage-1 1/5] FROM docker.io/library/ubuntu:jammy@sha256:0bced47fffa3361afa981854fcabcd4577cd43cebbb808cea2b1f33a3dd7f508                       0.0s
 => [app internal] load build context                                                                                                                   0.0s
 => => transferring context: 49.05kB                                                                                                                    0.0s
 => CACHED [app build  2/12] RUN export DEBIAN_FRONTEND=noninteractive DEBCONF_NONINTERACTIVE_SEEN=true     && apt-get -q update     && apt-get -q dis  0.0s
 => CACHED [app build  3/12] WORKDIR /build                                                                                                             0.0s
 => [app build  4/12] COPY ./Package.* ./                                                                                                               0.0s
 => [app build  5/12] RUN swift package resolve                                                                                                        30.2s
 => [app build  6/12] COPY . .                                                                                                                          0.0s
 => [app build  7/12] RUN swift build -c release --static-swift-stdlib                                                                                125.7s
 => [app build  8/12] WORKDIR /staging                                                                                                                  0.0s
 => [app build  9/12] RUN cp "$(swift build --package-path /build -c release --show-bin-path)/App" ./                                                   1.1s
 => [app build 10/12] RUN find -L "$(swift build --package-path /build -c release --show-bin-path)/" -regex '.*\.resources$' -exec cp -Ra {} ./ ;       0.8s
 => [app build 11/12] RUN [ -d /build/Public ] && { mv /build/Public ./Public && chmod -R a-w ./Public; } || true                                       0.1s
 => [app build 12/12] RUN [ -d /build/Resources ] && { mv /build/Resources ./Resources && chmod -R a-w ./Resources; } || true                           0.4s
 => CACHED [app stage-1 2/5] RUN export DEBIAN_FRONTEND=noninteractive DEBCONF_NONINTERACTIVE_SEEN=true     && apt-get -q update     && apt-get -q dis  0.0s
 => CACHED [app stage-1 3/5] RUN useradd --user-group --create-home --system --skel /dev/null --home-dir /app vapor                                     0.0s
 => CACHED [app stage-1 4/5] WORKDIR /app                                                                                                               0.0s
 => [app stage-1 5/5] COPY --from=build --chown=vapor:vapor /staging /app                                                                               0.2s
 => [app] exporting to image                                                                                                                            0.2s
 => => exporting layers                                                                                                                                 0.2s
 => => writing image sha256:03e0d052359e741baef70ed30ff37831ec3687b23aafe0ce12df7a0029dd70a3                                                            0.0s
 => => naming to docker.io/library/template-bare:latest                                                                                                 0.0s
[+] Running 2/2
 ✔ Network reproduce-fly-local-deploy-build-issue_default  Created                                                                                      0.0s
 ✔ Container reproduce-fly-local-deploy-build-issue-app-1  Created                                                                                      0.0s
Attaching to reproduce-fly-local-deploy-build-issue-app-1
reproduce-fly-local-deploy-build-issue-app-1  | [ DEBUG ] Could not load .env.production file: open(file:oFlag:mode:): No such file or directory (errno: 2) (Vapor/DotEnv.swift:123)
reproduce-fly-local-deploy-build-issue-app-1  | [ DEBUG ] Could not load .env file: open(file:oFlag:mode:): No such file or directory (errno: 2) (Vapor/DotEnv.swift:123)
reproduce-fly-local-deploy-build-issue-app-1  | [ NOTICE ] Server starting on http://0.0.0.0:8080 (Vapor/HTTPServer.swift:298)
```

## fly deploy --local-only

```bash
fly deploy --local-only
==> Verifying app config
Validating /Users/username/Documents/myway/_Develop/Dene/reproduce-fly-local-deploy-build-issue/fly.toml
Platform: machines
✓ Configuration is valid
--> Verified app config
==> Building image
==> Building image with Docker
--> docker host: 24.0.2 linux aarch64
[+] Building 1381.1s (15/22)
 => [internal] load build definition from Dockerfile                                                                                                    0.0s
 => => transferring dockerfile: 2.77kB                                                                                                                  0.0s
 => [internal] load .dockerignore                                                                                                                       0.0s
 => => transferring context: 95B                                                                                                                        0.0s
 => [internal] load metadata for docker.io/library/ubuntu:jammy                                                                                         0.9s
 => [internal] load metadata for docker.io/library/swift:5.8-jammy                                                                                      0.8s
 => [build  1/12] FROM docker.io/library/swift:5.8-jammy@sha256:49b1d6bdb9f968e42493f91f09a9488c46701790d9f647eb76b191b659c8a35b                        0.0s
 => [internal] load build context                                                                                                                       0.0s
 => => transferring context: 49.54kB                                                                                                                    0.0s
 => [stage-1 1/5] FROM docker.io/library/ubuntu:jammy@sha256:0bced47fffa3361afa981854fcabcd4577cd43cebbb808cea2b1f33a3dd7f508                           0.0s
 => CACHED [stage-1 2/5] RUN export DEBIAN_FRONTEND=noninteractive DEBCONF_NONINTERACTIVE_SEEN=true     && apt-get -q update     && apt-get -q dist-up  0.0s
 => CACHED [stage-1 3/5] RUN useradd --user-group --create-home --system --skel /dev/null --home-dir /app vapor                                         0.0s
 => CACHED [stage-1 4/5] WORKDIR /app                                                                                                                   0.0s
 => CACHED [build  2/12] RUN export DEBIAN_FRONTEND=noninteractive DEBCONF_NONINTERACTIVE_SEEN=true     && apt-get -q update     && apt-get -q dist-up  0.0s
 => CACHED [build  3/12] WORKDIR /build                                                                                                                 0.0s
 => [build  4/12] COPY ./Package.* ./                                                                                                                   0.0s
 => [build  5/12] RUN swift package resolve                                                                                                           550.0s
 => [build  6/12] COPY . .                                                                                                                              0.0s
 => [build  7/12] RUN swift build -c release --static-swift-stdlib                                                                                    830.1s
^CWARN failed to finish build in graphql: Post "https://api.fly.io/graphql": context canceled

```