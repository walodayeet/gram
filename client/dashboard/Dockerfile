FROM node:20-bookworm-slim AS builder
WORKDIR /app
ARG GRAM_SERVER_URL
ENV GRAM_SERVER_URL=
RUN corepack enable
COPY . .
RUN pnpm install --frozen-lockfile
RUN pnpm run --filter dashboard... build

FROM node:20-bookworm-slim
WORKDIR /app
RUN npm install -g serve
COPY --from=builder /app/client/dashboard/dist ./dist
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]
