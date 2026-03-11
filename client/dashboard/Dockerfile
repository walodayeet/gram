FROM node:20-bookworm-slim AS builder
WORKDIR /app
ARG GRAM_SERVER_URL
ENV GRAM_SERVER_URL=

RUN corepack enable

# Copy root workspace files
COPY package.json pnpm-lock.yaml* pnpm-workspace.yaml ./ 

# Copy all package.json files for correct workspace resolution
COPY client/dashboard/package.json ./client/dashboard/
COPY client/sdk/package.json ./client/sdk/
COPY server/package.json ./server/
COPY functions/package.json ./functions/

# Install all dependencies and create workspace symlinks
RUN pnpm install --frozen-lockfile

# Copy source code
COPY . .

# CRITICAL: Build the SDK dependency first so its 'dist/esm' folder exists
RUN pnpm run --filter @gram/client build

# Now build the dashboard
RUN pnpm run --filter dashboard build

FROM node:20-bookworm-slim
WORKDIR /app
RUN npm install -g serve
COPY --from=builder /app/client/dashboard/dist ./dist
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]
