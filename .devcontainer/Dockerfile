# See here for image contents: https://github.com/microsoft/vscode-dev-containers/tree/v0.191.1/containers/jekyll/.devcontainer/base.Dockerfile

FROM mcr.microsoft.com/vscode/devcontainers/jekyll:latest as base

FROM base as node
RUN apt-get update && apt-get install gnupg2 -y
# [Choice] Node.js version: none, lts/*, 16, 14, 12, 10
ARG NODE_VERSION="16"
RUN if [ "${NODE_VERSION}" != "none" ]; then su vscode -c "umask 0002 && . /usr/local/share/nvm/nvm.sh && nvm install ${NODE_VERSION} 2>&1"; fi

# [Optional] Uncomment this section to install additional OS packages.
# RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends <your-package-list-here>

# [Optional] Uncomment this line to install global node packages.
# RUN su vscode -c "source /usr/local/share/nvm/nvm.sh && npm install -g <your-package-here>" 2>&1