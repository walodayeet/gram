FROM golang:1.24-bookworm AS builder
WORKDIR /app
COPY . .
# Downgrade the version in go.mod to 1.24 to match the compiler while keeping the 'tool' block support
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
