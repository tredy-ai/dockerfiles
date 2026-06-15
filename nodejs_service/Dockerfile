## Tredy Base Image
## Provides common foundation for server, frontend, and collector services
## Usage: FROM ghcr.io/tredy-ai/nodejs_service:latest (or build locally)
##
## This base image includes:
## - Alpine Linux + Node.js 24
## - Non-root user setup (tredy:tredy)
## - pnpm package manager
## - OpenSSL for crypto operations
##
## Build: docker build -f docker/Dockerfile -t nodejs_service:local .

#############################################
# Build Arguments
#############################################
ARG BASEDIR="/app"
ARG ARG_UID=770
ARG ARG_GID=770
ARG USER_NAME="tredy"
ARG GROUP_NAME="tredy"
ARG PNPM_VERSION="11.6.0"

## This is a comment so that we can deploy this

#############################################
# Base Image
#############################################
FROM node:24-alpine

## Propagate build arguments
ARG BASEDIR
ARG ARG_UID
ARG ARG_GID
ARG USER_NAME
ARG GROUP_NAME
ARG PNPM_VERSION

#####
# Install runtime dependencies only (no build tools!)
# OpenSSL is needed for RSA key generation and crypto operations
#####
RUN apk add --no-cache openssl curl

#####
# Install pnpm via corepack
# Corepack is built into Node.js 16+ and manages package manager versions
#####
ENV PNPM_HOME="/usr/local/share"
ENV PATH="$PNPM_HOME:$PATH"
RUN corepack enable && corepack prepare "pnpm@${PNPM_VERSION}" --activate

#####
# Create non-root user and group
# ...
# Best practice: Never run Node.js apps as root in production
# -D: Creates a system user without a password
# -u: User's UID (770 for compatibility with Railway/other platforms)
# -h: Home directory
# -G: Primary group
#####
RUN addgroup -g ${ARG_GID} "${GROUP_NAME}" && \
  adduser \
    -D \
    -u ${ARG_UID} \
    -h "${BASEDIR}" \
    -G "${GROUP_NAME}" \
    "${USER_NAME}"

#####
# Initialize base directory with correct ownership
#####
RUN mkdir -p "${BASEDIR}" && \
  chown -R "${USER_NAME}:${GROUP_NAME}" "${BASEDIR}"

#####
# Force pnpm installation (triggers actual download)
# Without this, corepack lazily downloads pnpm on first use
#####
USER ${USER_NAME}
RUN pnpm --version
USER root

#####
# Set working directory and default user
#####
WORKDIR ${BASEDIR}
USER ${USER_NAME}

#############################################
# Metadata
#############################################
LABEL maintainer="Tredy AI <support@tredy.ai>"
LABEL description="Base image for Tredy services"
LABEL org.opencontainers.image.source="https://github.com/tredy-ai/dockerfiles"
LABEL org.opencontainers.image.licenses="MIT"

#############################################
# Default Command
#############################################
CMD ["/bin/sh"]
