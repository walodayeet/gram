FROM node:20-bookworm-slim AS builder
WORKDIR /app
ARG GRAM_SERVER_URL
ENV VITE_GRAM_SERVER_URL=$GRAM_SERVER_URL

RUN corepack enable
COPY package.json pnpm-lock.yaml* pnpm-workspace.yaml ./ 
COPY client/dashboard/package.json ./client/dashboard/
COPY client/sdk/package.json ./client/sdk/

# Install all workspace dependencies
RUN pnpm install --frozen-lockfile

# Copy source code
COPY . .

# CRITICAL: Build the SDK first, then the dashboard
RUN pnpm run --filter @gram/client build
RUN pnpm run --filter dashboard build

FROM node:20-bookworm-slim
WORKDIR /app
RUN npm install -g serve
COPY --from=builder /app/client/dashboard/dist ./dist
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]
