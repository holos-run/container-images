FROM public.ecr.aws/docker/library/docker:cli AS docker
FROM public.ecr.aws/aws-cli/aws-cli AS aws-cli
FROM registry.k8s.io/kubectl:v1.31.0 AS kubectl
FROM public.ecr.aws/docker/library/debian:bullseye AS final

# Install tools
RUN apt-get -qq -y update && \
    apt-get -qq -y install \
      openssh-client \
      curl \
      jq \
      less \
      sudo \
      && apt-get clean \
      && rm -rf /var/cache/apt/archives /var/lib/apt/lists/*

# Install kubectl
RUN mkdir -p /etc/ssl/certs
COPY --from=kubectl /bin/kubectl /bin/kubectl
COPY --from=kubectl /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt
ENV SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt

# Install AWS CLI
COPY --from=aws-cli /usr/local/aws-cli/ /usr/local/aws-cli/
COPY --from=aws-cli /usr/local/bin/ /usr/local/bin/

# Docker (Needed to write credentials)
COPY --from=docker /usr/local/bin/docker /usr/local/bin/docker

RUN groupadd --gid 8192 app && \
  useradd -m -d /app -c "App" -m --uid 8192 --gid 8192 app && \
  usermod -aG sudo app && \
  echo '%sudo ALL=(ALL) NOPASSWD: ALL' > /etc/sudoers.d/sudo

WORKDIR /app

USER app
