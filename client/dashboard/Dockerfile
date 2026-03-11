FROM node:20-bookworm-slim AS builder
WORKDIR /app
ARG GRAM_SERVER_URL
ENV GRAM_SERVER_URL=$GRAM_SERVER_URL

COPY package.json pnpm-lock.yaml* pnpm-workspace.yaml ./ 
COPY client/dashboard/package.json ./client/dashboard/
COPY client/sdk/package.json ./client/sdk/

RUN corepack enable && pnpm install --frozen-lockfile

# Copy source code
COPY . .

# Build the SDK dependency first
RUN pnpm run --filter @gram/client build

# Build the dashboard
RUN pnpm run --filter dashboard build

FROM node:20-bookworm-slim
WORKDIR /app
RUN npm install -g serve
COPY --from=builder /app/client/dashboard/dist ./dist
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]
