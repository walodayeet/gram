FROM node:20-bookworm-slim AS builder
WORKDIR /app
ARG GRAM_SERVER_URL
ENV GRAM_SERVER_URL=

RUN corepack enable

# Copy everything first to ensure workspace integrity
# This is more reliable for complex monorepos in Docker
COPY . .

# Install all dependencies
RUN pnpm install --frozen-lockfile

# Build dashboard and all its workspace dependencies (SDKs, elements, etc.)
# The '...' suffix is the key to recursive workspace building
RUN pnpm run --filter dashboard... build

FROM node:20-bookworm-slim
WORKDIR /app
RUN npm install -g serve
COPY --from=builder /app/client/dashboard/dist ./dist
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]
