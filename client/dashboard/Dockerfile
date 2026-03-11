FROM node:20-bookworm-slim AS builder
WORKDIR /app
ARG GRAM_SERVER_URL
ENV GRAM_SERVER_URL=

# Install pnpm and dependencies
RUN corepack enable
COPY package.json pnpm-lock.yaml* pnpm-workspace.yaml ./ 
# Copy all package.json files for resolution
COPY client/dashboard/package.json ./client/dashboard/
COPY client/sdk/package.json ./client/sdk/
RUN pnpm install --frozen-lockfile

# Copy full source for build
COPY . .

# Build the dashboard and ALL its workspace dependencies (the ... suffix)
RUN pnpm run --filter dashboard... build

FROM node:20-bookworm-slim
WORKDIR /app
RUN npm install -g serve
COPY --from=builder /app/client/dashboard/dist ./dist
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]
