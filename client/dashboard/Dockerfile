FROM node:20-bookworm-slim AS builder
WORKDIR /app
ARG GRAM_SERVER_URL
ENV GRAM_SERVER_URL=

RUN corepack enable

# Copy root workspace files
COPY package.json pnpm-lock.yaml pnpm-workspace.yaml ./ 

# Copy all package.json files for correct workspace resolution
# This ensures symbolic links are correctly created during install
RUN mkdir -p client/dashboard client/sdk elements server functions ts-framework/functions evaluation ts-framework/create-function/gram-template-mcp ts-framework/create-function/gram-template-gram
COPY client/dashboard/package.json ./client/dashboard/
COPY client/sdk/package.json ./client/sdk/
COPY elements/package.json ./elements/
COPY server/package.json ./server/
COPY functions/package.json ./functions/
COPY ts-framework/functions/package.json ./ts-framework/functions/
COPY ts-framework/create-function/package.json ./ts-framework/create-function/
COPY evaluation/package.json ./evaluation/

# Install all dependencies
RUN pnpm install --frozen-lockfile

# Copy source code
COPY . .

# CRITICAL: Build workspace dependencies in sequence
# This generates the 'dist' folders that Vite needs to bundle the dashboard
RUN pnpm run --filter @gram/client build
RUN pnpm run --filter @gram-ai/elements build

# Now build the dashboard UI
RUN pnpm run --filter dashboard build

FROM node:20-bookworm-slim
WORKDIR /app
RUN npm install -g serve
COPY --from=builder /app/client/dashboard/dist ./dist
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]
