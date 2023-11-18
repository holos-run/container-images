FROM public.ecr.aws/docker/library/docker:cli as docker

FROM public.ecr.aws/aws-cli/aws-cli as aws-cli

FROM public.ecr.aws/docker/library/debian:bullseye AS final

# Install tools
RUN apt-get -qq -y update && \
    apt-get -qq -y install \
      curl \
      jq \
      less \
      sudo

# Install AWS CLI
COPY --from=aws-cli /usr/local/aws-cli/ /usr/local/aws-cli/
COPY --from=aws-cli /usr/local/bin/ /usr/local/bin/

# Docker (Needed to write credentials)
COPY --from=docker /usr/local/bin/docker /usr/local/bin/docker

# Install kubectl
RUN curl -Lo /usr/local/bin/kubectl "https://dl.k8s.io/release/v1.28.4/bin/linux/amd64/kubectl" \
  && chmod 0755 /usr/local/bin/kubectl

RUN groupadd --gid 8192 app && \
  useradd -m -d /app -c "App" -m --uid 8192 --gid 8192 app && \
  usermod -aG sudo app && \
  echo '%sudo ALL=(ALL) NOPASSWD: ALL' > /etc/sudoers.d/sudo

WORKDIR /app

USER app
