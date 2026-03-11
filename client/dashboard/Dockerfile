FROM node:20-bookworm-slim AS builder
WORKDIR /app
# Pass build-time URL from compose to Vite
ARG GRAM_SERVER_URL
ENV GRAM_SERVER_URL=$GRAM_SERVER_URL
COPY package.json pnpm-lock.yaml* pnpm-workspace.yaml ./ 
COPY client/dashboard/package.json ./client/dashboard/
RUN corepack enable && pnpm install --frozen-lockfile
COPY . .
RUN pnpm run --filter dashboard build

FROM node:20-bookworm-slim
WORKDIR /app
RUN npm install -g serve
COPY --from=builder /app/client/dashboard/dist ./dist
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]
