# Multi-stage Dockerfile for Ditsmod Server Application

# Stage 1: Build stage
FROM node:20-alpine AS builder

# Set working directory
WORKDIR /app

# Copy package files for dependency installation
COPY package*.json ./
COPY packages/server/package*.json ./packages/server/
COPY packages/shared/package*.json ./packages/shared/

# Install all dependencies (including devDependencies for build)
RUN npm ci

# Copy source code
COPY packages/server ./packages/server
COPY packages/shared ./packages/shared
COPY tsconfig.json ./

# Build the application
WORKDIR /app/packages/server
RUN npm run build

# Stage 2: Production stage
FROM node:20-alpine AS production

# Set working directory
WORKDIR /app

# Copy package files
COPY package*.json ./
COPY packages/server/package*.json ./packages/server/
COPY packages/shared/package*.json ./packages/shared/

# Install all dependencies first (to satisfy postinstall scripts)
RUN npm ci

# Remove devDependencies after installation
RUN npm prune --omit=dev

# Copy built application from builder stage
COPY --from=builder /app/packages/server/dist ./packages/server/dist
COPY --from=builder /app/packages/shared ./packages/shared

# Copy package.json files needed for runtime
COPY packages/server/package.json ./packages/server/

# Set working directory to server package
WORKDIR /app/packages/server

# Environment variables (can be overridden at runtime)
ENV NODE_ENV=production
ENV MYSQL_PORT=3306

# Expose application port (default Ditsmod port)
EXPOSE 3000

# Run the application in production mode
CMD ["node", "dist/main.js"]
