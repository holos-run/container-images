FROM registry.k8s.io/kubectl:v1.31.0 AS kubectl
FROM public.ecr.aws/docker/library/golang:1.23-bookworm AS final

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
      curl \
      openssh-client \
      gnupg \
      jq \
      less \
      sudo \
      && apt-get clean \
      && rm -rf /var/cache/apt/archives /var/lib/apt/lists/*

# Install holos
RUN go install github.com/holos-run/holos/cmd/holos@latest

# Install kubectl
RUN mkdir -p /etc/ssl/certs
COPY --from=kubectl /bin/kubectl /usr/local/bin/kubectl
COPY --from=kubectl /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt
ENV SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt

# Install helm to /usr/local/bin/helm
# https://helm.sh/docs/intro/install/#from-script
# https://holos.run/docs/v1alpha5/tutorial/setup/#dependencies
RUN curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 \
  && chmod 700 get_helm.sh \
  && DESIRED_VERSION=v3.16.2 ./get_helm.sh \
  && rm -f get_helm.sh

# Install go releaser
RUN curl -fsSL -o install.sh https://raw.githubusercontent.com/golangci/golangci-lint/master/install.sh \
  && chmod 700 install.sh \
  && TAG=v2.5.0 ./install.sh \
  && rm -f install.sh

# Match GitHub Actions workspace UID of 1001
RUN groupadd --gid 1001 app && \
  useradd -m -d /app -c "App" -m --uid 1001 --gid 1001 app && \
  usermod -aG sudo app && \
  echo '%sudo ALL=(ALL) NOPASSWD: ALL' > /etc/sudoers.d/sudo

# Change ownership to the runner UID
RUN chown -R 1001:1001 /go

WORKDIR /app
USER app

# Make a build cache for holos
RUN git clone https://github.com/holos-run/holos && \
  cd holos && \
  make tools && \
  make install

# Cache test dependencies too, we don't care if the tests pass or not we want the cache.
run cd holos && make test || true
