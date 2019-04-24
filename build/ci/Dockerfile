# Docker image aimed to run compilation and tests in CI
FROM golang:1.11

ENV KUBEBUILDER_VERSION=1.0.8
ENV KUSTOMIZE_VERSION=1.0.11
ENV GCLOUD_VERSION=232.0.0
ENV KUBECTL_VERSION 1.13.2
ENV DOCKER_VERSION=18.03.1-ce

# Download kubebuilder release to get required tools (etcd, apiserver, etc.)
RUN curl -L -O https://github.com/kubernetes-sigs/kubebuilder/releases/download/v${KUBEBUILDER_VERSION}/kubebuilder_${KUBEBUILDER_VERSION}_linux_amd64.tar.gz && \
    tar -zxvf kubebuilder_${KUBEBUILDER_VERSION}_linux_amd64.tar.gz && \
    mv kubebuilder_${KUBEBUILDER_VERSION}_linux_amd64 /usr/local/kubebuilder
# update PATH to include downloaded binaries
ENV PATH=${PATH}:/usr/local/kubebuilder/bin

# Download required golang tools
RUN go get github.com/golang/dep/cmd/dep golang.org/x/tools/cmd/goimports

# Download kustomize for deploying the operator
RUN curl -L -O https://github.com/kubernetes-sigs/kustomize/releases/download/v${KUSTOMIZE_VERSION}/kustomize_${KUSTOMIZE_VERSION}_linux_amd64 && \
    mv kustomize_${KUSTOMIZE_VERSION}_linux_amd64 /usr/local/bin/kustomize && chmod +x /usr/local/bin/kustomize

# Download gcloud for provisioning gke clusters
ENV PATH=${PATH}:/usr/local/gcloud/google-cloud-sdk/bin
RUN curl -L -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-${GCLOUD_VERSION}-linux-x86_64.tar.gz && \
    mkdir -p /usr/local/gcloud && \
    tar -C /usr/local/gcloud -zxvf google-cloud-sdk-${GCLOUD_VERSION}-linux-x86_64.tar.gz && \
    /usr/local/gcloud/google-cloud-sdk/install.sh && \
    gcloud config set core/disable_usage_reporting true && \
    gcloud config set component_manager/disable_update_check true && \
    gcloud components install beta --quiet

# Download kubectl for deploying the operator and running e2e tests
RUN curl -L -O https://storage.googleapis.com/kubernetes-release/release/v${KUBECTL_VERSION}/bin/linux/amd64/kubectl && \
    mv kubectl /usr/local/bin/kubectl && chmod +x /usr/local/bin/kubectl

# Install Docker client for building and pushing images
RUN curl -fsSLO https://download.docker.com/linux/static/stable/x86_64/docker-${DOCKER_VERSION}.tgz \
  && tar xzvf docker-${DOCKER_VERSION}.tgz --strip 1 \
                 -C /usr/local/bin docker/docker \
  && rm docker-${DOCKER_VERSION}.tgz