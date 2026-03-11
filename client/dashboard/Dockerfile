FROM node:20-bookworm-slim AS builder
WORKDIR /app
ARG GRAM_SERVER_URL
ENV GRAM_SERVER_URL=

RUN corepack enable

# Copy EVERYTHING first - required for reliable workspace linking in Docker
COPY . .

# Install all dependencies
RUN pnpm install --frozen-lockfile

# Build the ENTIRE workspace in recursive mode
# This ensures sdk, elements, and dashboard are built in the exact topological order
RUN pnpm -r build

FROM node:20-bookworm-slim
WORKDIR /app
RUN npm install -g serve
# Note: dist is built in the dashboard subfolder
COPY --from=builder /app/client/dashboard/dist ./dist
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]
