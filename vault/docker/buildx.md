- buildx
  ```bash
  curl -o  docker-buildx -L https://github.com/docker/buildx/releases/download/v0.13.0/buildx-v0.13.0.linux-amd64
  chmod +x docker-buildx
  mv docker-buildx $HOME/.docker/cli-plugins/docker-buildx
  ```
- compose
   ```
   DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
   mkdir -p $DOCKER_CONFIG/cli-plugins
   curl -SL https://github.com/docker/compose/releases/download/v2.24.7/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
   ```
- secrets
  ```bash
  # /root/.nuget/packages/
  docker buildx build \
           --secret id=NX_USR,src=$HOME/.nexus/user \
           --secret id=NX_PWD,src=$HOME/.nexus/token  \
           -t test-fhir-engine \
           .  > builder.log 2>&1
  ```
