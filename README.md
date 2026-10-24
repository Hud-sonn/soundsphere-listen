# Soundsphere Listen

A high-performance Go WebSocket server for Soundsphere's Listen Together
feature. It uses protobuf and gzip compression for fast, efficient
communication between clients.

Derived from [Metroserver](https://github.com/MetrolistGroup/metroserver)
(GPL-3.0); this fork owns its protocol schema and no longer depends on the
upstream `metroproto` submodule.

# Quickstart

## Locally

You need Go, protobuf, and `protoc-gen-go`.

```bash
git clone https://github.com/Hud-sonn/soundsphere-listen
cd soundsphere-listen

# Generate protobuf files (required first time)
./scripts/generate_proto.sh

# Download dependencies
go mod download

# Build the server
go build -o soundsphere-listen ./cmd/soundsphere-listen

# Run on default port 8080
./soundsphere-listen

# Run on custom port
PORT=9000 ./soundsphere-listen
```

## Configuration

`PORT` sets the HTTP/WebSocket port. It defaults to `8080`.

The server writes graceful-shutdown recovery state to `server_state.json` with
`0600` permissions.

## Project Structure

- `cmd/soundsphere-listen` contains the executable entrypoint.
- `internal/server` contains server behavior and tests.
- `proto` contains the protobuf schema (`listentogether.proto`) and the
  generated Go code. The schema is the source of truth for the Android client.
- `scripts` contains development and generation scripts.

## Docker

```bash
# Clone the repository
git clone https://github.com/Hud-sonn/soundsphere-listen
cd soundsphere-listen

# Build locally
docker build -f Dockerfile.for-docker-hosts -t soundsphere-listen:latest .

# Run on port 8080
docker run -d \
  -p 8080:8080 \
  -e PORT=8080 \
  --name soundsphere-listen \
  soundsphere-listen:latest

# Run on custom port
docker run -d \
  -p 9000:9000 \
  -e PORT=9000 \
  --name soundsphere-listen \
  soundsphere-listen:latest
```

## Docker Compose

```yaml
---
services:
  soundsphere-listen:
    image: ghcr.io/hud-sonn/soundsphere-listen:latest
    ports:
      - "8080:8080"
    environment:
      - PORT=8080
    healthcheck:
      test:
        [
          "CMD",
          "wget",
          "--no-verbose",
          "--tries=1",
          "--spider",
          "http://localhost:8080/health",
        ]
      interval: 30s
      timeout: 3s
      retries: 3
      start_period: 5s
    restart: unless-stopped
```
