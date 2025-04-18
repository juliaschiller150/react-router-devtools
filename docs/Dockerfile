# syntax = docker/dockerfile:1.4

# Adjust NODE_VERSION as desired
ARG NODE_VERSION=22
FROM node:${NODE_VERSION}-slim AS base

# React router app lives here
WORKDIR /app

# Set production environment
ENV NODE_ENV="production"


# Throw-away build stage to reduce size of final image
FROM base as build

# Install packages needed to build node modules
RUN apt-get update -qq && \
    apt-get install --no-install-recommends -y build-essential node-gyp pkg-config python-is-python3

# Install node modules
COPY --link package-lock.json package.json ./
RUN npm ci --include=dev

# Copy application code
COPY --link . .

# Build application
RUN npm run build

# Remove development dependencies
RUN npm prune --omit=dev


# Final stage for app image
FROM base

# Copy built application
COPY --from=build / /

# Start the server by default, this can be overwritten at runtime
EXPOSE 3000
CMD [ "node_modules/.bin/react-router-serve", "./build/server/index.js" ]
