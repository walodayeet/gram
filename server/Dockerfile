FROM golang:1.24-bookworm AS builder
WORKDIR /app
RUN go version
COPY . .
# Downgrade go.mod version to 1.24 so it works with the 1.24 compiler while keeping the 'tool' block
RUN sed -i 's/go 1.26/go 1.24/g' go.mod
RUN go mod download
RUN go build -o /app/gram ./server/main.go

FROM debian:bookworm-slim
WORKDIR /app
RUN apt-get update && apt-get install -y ca-certificates && rm -rf /var/lib/apt/lists/*
COPY --from=builder /app/gram /app/gram
COPY --from=builder /app/server/migrations /app/migrations
ENV GO_ENV=production
ENTRYPOINT ["/app/gram"]
