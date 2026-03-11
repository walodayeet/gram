FROM golang:1.26-bookworm AS builder
WORKDIR /app
# This will show up in Dokploy logs to verify the version
RUN go version
COPY . .
RUN go mod download
RUN go build -o /app/gram ./server/main.go

FROM debian:bookworm-slim
WORKDIR /app
RUN apt-get update && apt-get install -y ca-certificates && rm -rf /var/lib/apt/lists/*
COPY --from=builder /app/gram /app/gram
COPY --from=builder /app/server/migrations /app/migrations
ENV GO_ENV=production
ENTRYPOINT ["/app/gram"]
