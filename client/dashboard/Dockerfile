FROM node:20-bookworm-slim AS builder
WORKDIR /app
ARG GRAM_SERVER_URL
ENV GRAM_SERVER_URL=

# Enable pnpm 10 as required by elements/package.json
RUN corepack enable && corepack prepare pnpm@10.30.2 --activate

# Copy the entire repository to ensure workspace links and configs are perfect
COPY . .

# Install all dependencies
RUN pnpm install --frozen-lockfile

# Build the dashboard AND all its internal workspace dependencies (@gram/client, @gram-ai/elements)
# The '...' suffix handles the topological build order automatically
RUN pnpm run --filter dashboard... build

FROM node:20-bookworm-slim
WORKDIR /app
RUN npm install -g serve
# Copy the built files from the correct workspace path
COPY --from=builder /app/client/dashboard/dist ./dist
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]
