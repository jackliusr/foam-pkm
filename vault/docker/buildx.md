- buildx
  ```bash
  curl -o  docker-buildx -L https://github.com/docker/buildx/releases/download/v0.13.0/buildx-v0.13.0.linux-amd64
  chmod +x docker-buildx	
  mv docker-buildx $HOME/.docker/cli-plugins/docker-buildx
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
- test
  ```bash
  dotnet new install  MSBuild.Sdk.SqlProj.Templates
  docker run -it -v $(pwd):/app mcr.microsoft.com/dotnet/sdk:8.0 bash
  ```
