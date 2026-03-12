# Multi-stage build for Docusaurus website

# Stage 1: Build stage
FROM node:20-alpine AS builder

WORKDIR /app

# Copy package files from root
COPY package*.json ./

# Install root dependencies
RUN npm ci

# Copy all source files
COPY . .

# Build the website using workspace command
WORKDIR /app/website
RUN npm install && npm run build

# Stage 2: Production stage
FROM node:20-alpine AS production

WORKDIR /app

# Install serve package globally for serving static files
RUN npm install -g serve

# Copy built website from builder stage
COPY --from=builder /app/website/build ./build

# Expose port for serving
EXPOSE 3000

# Serve the built website
CMD ["serve", "-s", "build", "-l", "3000"]
