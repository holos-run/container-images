FROM registry.k8s.io/kubectl:v1.31.0 AS kubectl
FROM quay.io/holos-run/golang:1.23-bookworm AS go

# Install holos
RUN go install github.com/holos-run/holos/cmd/holos@latest

# Use debian for the final image
FROM public.ecr.aws/docker/library/debian:bookworm AS final

# Install tools
RUN apt-get -qq -y update && \
    apt-get -qq -y install \
      openssh-client \
      git \
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

# Copy holos
COPY --from=go /go/bin/holos /bin/holos

# Install helm to /usr/local/bin/helm
# https://helm.sh/docs/intro/install/#from-script
RUN curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 \
  && chmod 700 get_helm.sh \
  && ./get_helm.sh \
  && rm -f get_helm.sh

# Match GitHub Actions workspace UID of 1001
RUN groupadd --gid 1001 app && \
  useradd -m -d /app -c "App" -m --uid 1001 --gid 1001 app && \
  usermod -aG sudo app && \
  echo '%sudo ALL=(ALL) NOPASSWD: ALL' > /etc/sudoers.d/sudo

WORKDIR /app
USER app
