FROM public.ecr.aws/docker/library/debian:bullseye AS final

# Install tools
RUN apt-get -qq -y update && \
    apt-get -qq -y install \
      curl \
      jq \
      && apt-get clean \
      && rm -rf /var/cache/apt/archives /var/lib/apt/lists/*

RUN groupadd --gid 8192 app && useradd -m -d /app -c "App" -m --uid 8192 --gid 8192 app

WORKDIR /app

USER app
