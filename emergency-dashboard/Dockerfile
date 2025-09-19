# Multi-stage Dockerfile for Angular 20 SSR application
# Stage 1: Build the application
FROM node:20-alpine AS builder

# temporarily add bash and build deps for some native modules (if needed)
RUN apk add --no-cache python3 make g++

WORKDIR /app

# Copy package manifests and install deps
COPY package.json package-lock.json* ./
RUN npm ci

# Copy source and build production SSR bundles
COPY . .
# Build production output (browser + server)
RUN npm run build -- --configuration production

# Stage 2: Lightweight runtime image
FROM node:20-alpine AS runtime

WORKDIR /app
ENV NODE_ENV=production

# Install only production dependencies
COPY package.json package-lock.json* ./
RUN npm ci --omit=dev

# Copy built artifacts from the builder
COPY --from=builder /app/dist ./dist

# Expose port used by the server (server.ts uses 4000 by default)
EXPOSE 4000
ENV PORT=4000

# Default command to run the SSR server
CMD ["node", "dist/emergency-dashboard/server/server.mjs"]
