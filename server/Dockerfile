FROM golang:1.24-bookworm AS builder
WORKDIR /app
# Copy the entire root since go.mod is in the root
COPY . .
# Downgrade the version in go.mod to 1.24 just to be safe with the 1.24 compiler, 
# but keep the 'tool' block which 1.24 supports.
RUN sed -i 's/go 1.26/go 1.24/g' go.mod
RUN go mod download
# The main.go is actually in the server/ directory according to your file tree
RUN go build -o /app/gram ./server/main.go

FROM debian:bookworm-slim
WORKDIR /app
RUN apt-get update && apt-get install -y ca-certificates && rm -rf /var/lib/apt/lists/*
COPY --from=builder /app/gram /app/gram
COPY --from=builder /app/server/migrations /app/migrations
ENV GO_ENV=production
ENTRYPOINT ["/app/gram"]
