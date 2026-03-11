FROM golang:1.24-bookworm AS builder
WORKDIR /app
# Explicitly install Go 1.26 to satisfy clue dependency
RUN apt-get update && apt-get install -y wget &&     wget https://go.dev/dl/go1.26.linux-amd64.tar.gz &&     rm -rf /usr/local/go && tar -C /usr/local -xzf go1.26.linux-amd64.tar.gz
ENV PATH="/usr/local/go/bin:/opt/venv/bin:/opt/venv-a0/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
RUN go version
COPY . .
RUN go mod download
RUN go build -o /app/gram ./server/main.go

FROM debian:bookworm-slim
WORKDIR /app
RUN apt-get update && apt-get install -y ca-certificates && rm -rf /var/lib/apt/lists/*
COPY --from=builder /app/gram /app/gram
COPY --from=builder /app/server/migrations /app/server/migrations
ENV GO_ENV=production
ENTRYPOINT ["/app/gram"]
