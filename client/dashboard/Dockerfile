FROM node:20-bookworm-slim AS builder
WORKDIR /app
ARG GRAM_SERVER_URL
ENV GRAM_SERVER_URL=

RUN corepack enable

# Copy ALL workspace metadata first
COPY package.json pnpm-lock.yaml pnpm-workspace.yaml ./ 
RUN mkdir -p client/dashboard client/sdk elements server functions ts-framework/functions evaluation
COPY client/dashboard/package.json ./client/dashboard/
COPY client/sdk/package.json ./client/sdk/
COPY elements/package.json ./elements/
COPY server/package.json ./server/
COPY functions/package.json ./functions/
COPY ts-framework/functions/package.json ./ts-framework/functions/
COPY evaluation/package.json ./evaluation/

# Install everything to establish workspace links
RUN pnpm install --frozen-lockfile

# Copy source code
COPY . .

# Build internal libraries in order
RUN pnpm run --filter @gram/client build
RUN pnpm run --filter @gram-ai/elements build

# Finally build the dashboard
RUN pnpm run --filter dashboard build

FROM node:20-bookworm-slim
WORKDIR /app
RUN npm install -g serve
COPY --from=builder /app/client/dashboard/dist ./dist
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]
