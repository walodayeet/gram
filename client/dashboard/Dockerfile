FROM node:20-bookworm-slim AS builder
WORKDIR /app
ARG GRAM_SERVER_URL
ENV GRAM_SERVER_URL=

RUN corepack enable

# Copy everything to ensure all workspace dependencies and configs are present
COPY . .

# Install all dependencies
RUN pnpm install --frozen-lockfile

# Sequential build of workspace dependencies to ensure 'dist' folders exist
RUN pnpm run --filter @gram/client build
RUN pnpm run --filter @gram-ai/elements build

# Finally build the dashboard UI
RUN pnpm run --filter dashboard build

FROM node:20-bookworm-slim
WORKDIR /app
RUN npm install -g serve
COPY --from=builder /app/client/dashboard/dist ./dist
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]
