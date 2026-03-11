FROM golang:1.24-bookworm AS builder
WORKDIR /app
RUN go version
COPY . .
# Safeguard: downgrade go.mod version to match compiler while keeping tool block support
RUN sed -i 's/go 1.26/go 1.24/g' go.mod
RUN go mod download
# Build from the correct entrypoint
RUN go build -o /app/gram ./server/main.go

FROM debian:bookworm-slim
WORKDIR /app
RUN apt-get update && apt-get install -y ca-certificates && rm -rf /var/lib/apt/lists/*
COPY --from=builder /app/gram /app/gram
COPY --from=builder /app/server/migrations /app/server/migrations
ENV GO_ENV=production
ENTRYPOINT ["/app/gram"]
