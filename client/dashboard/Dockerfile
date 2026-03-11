FROM node:20-bookworm-slim AS builder
WORKDIR /app
ARG GRAM_SERVER_URL
ENV GRAM_SERVER_URL=

RUN corepack enable

# Copy workspace config and lockfile
COPY package.json pnpm-lock.yaml* pnpm-workspace.yaml ./ 

# Copy ALL package.json files across the monorepo
# This ensures pnpm install creates correct symbolic links for all dependencies
RUN mkdir -p client/dashboard client/sdk server functions elements ts-framework/functions evaluation
COPY client/dashboard/package.json ./client/dashboard/
COPY client/sdk/package.json ./client/sdk/
COPY server/package.json ./server/
COPY functions/package.json ./functions/
COPY elements/package.json ./elements/
COPY ts-framework/functions/package.json ./ts-framework/functions/
COPY evaluation/package.json ./evaluation/

# Install all dependencies
RUN pnpm install --frozen-lockfile

# Copy the entire source code
COPY . .

# Build the dashboard and ALL its workspace dependencies recursively
# This will build @gram/client, @gram-ai/elements, and others in the correct order
RUN pnpm run --filter dashboard... build

FROM node:20-bookworm-slim
WORKDIR /app
RUN npm install -g serve
COPY --from=builder /app/client/dashboard/dist ./dist
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]
