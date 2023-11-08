FROM public.ecr.aws/docker/library/golang:1.21-bullseye AS final

# Install NodeJS 20
RUN mkdir -p /etc/apt/keyrings && \
    curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg && \
    echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_20.x nodistro main" \
      | tee /etc/apt/sources.list.d/nodesource.list

# Install tools
RUN apt-get -qq -y update && \
    apt-get -qq -y install \
      build-essential \
      nodejs \
      git \
      curl

RUN curl -fsSL -o /bin/yarn https://github.com/yarnpkg/yarn/releases/download/v1.22.19/yarn-1.22.19.js && \
    chmod a+x /bin/yarn

RUN groupadd --gid 8192 app && useradd -c "App" -m --uid 8192 --gid 8192 app
