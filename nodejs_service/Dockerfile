## Tredy Base Image
## Provides common foundation for standard NodeJS services across Tredy AI.
## Usage: FROM ghcr.io/tredy-ai/nodejs_service:<X.Y.Z> (or build locally)
##        No :latest tag is published; pin a release version.
##
## This base image includes:
## - Alpine Linux + Node.js 26 (Lithium LTS)
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

#############################################
# Base Image
#############################################
FROM node:lithium-alpine

## Fetch PNPM
COPY --from=ghcr.io/pnpm/pnpm:12.18.0-alpine /opt/pnpm /opt/pnpm
RUN for bin in pnpm pn pnpx pnx; do ln -s "/opt/pnpm/$bin" "/usr/local/bin/$bin"; done

## Propagate build arguments
ARG BASEDIR
ARG ARG_UID
ARG ARG_GID
ARG USER_NAME
ARG GROUP_NAME

#####
# Install runtime dependencies only (no build tools!)
# OpenSSL is needed for RSA key generation and crypto operations
#####
RUN apk add --no-cache openssl curl

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
LABEL description="Base image for Tredy's NodeJS services"
LABEL org.opencontainers.image.source="https://github.com/tredy-ai/dockerfiles"
LABEL org.opencontainers.image.licenses="MIT"

#############################################
# Default Command
#############################################
CMD ["/bin/sh"]
