FROM node:20-bookworm-slim AS builder
WORKDIR /app
ARG GRAM_SERVER_URL
# Vite requires this during build time
ENV GRAM_SERVER_URL=

RUN corepack enable

# Copy workspace config and lockfile
COPY package.json pnpm-lock.yaml* pnpm-workspace.yaml ./ 

# Copy ALL package.json files to allow pnpm to resolve the workspace structure
# This ensures symbolic links are correctly created during install
COPY client/dashboard/package.json ./client/dashboard/
COPY client/sdk/package.json ./client/sdk/
COPY server/package.json ./server/
COPY functions/package.json ./functions/

RUN pnpm install --frozen-lockfile

# Now copy the source code
COPY . .

# Build the dashboard and ALL its workspace dependencies (like @gram/client)
# The '...' suffix is the standard pnpm way to handle this
RUN pnpm run --filter dashboard... build

FROM node:20-bookworm-slim
WORKDIR /app
RUN npm install -g serve
COPY --from=builder /app/client/dashboard/dist ./dist
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]
