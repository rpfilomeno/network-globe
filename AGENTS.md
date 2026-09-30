# AGENTS.md — network-globe

Go + embedded React frontend. Real-time TCP sniffer → GeoIP lookup → Globe.GL via WebSocket.

## Structure
- `main.go` — pcap capture, `ipToCoord`, flags, HTTP bootstrap.
- `server.go` — `Server` (mux + websockets + broadcast), `Queue`/`Broadcast`, Storj upload. Frontend embedded via `//go:embed public/*`.
- `public/index.html` — Globe.GL UI (React UMD + Babel standalone, no build step). WS at `/ws`.

## Commands
- `go build` (Windows needs Npcap SDK; Linux: `sudo apt install libpcap-dev gcc`, `CGO_ENABLED=1 go build`)
- `go vet ./...`
- Run: `./network-globe --list-devices`, then `./network-globe --device <name> --geolite2-path ./GeoLite2-City.mmdb` → http://localhost:8000
- Flags: `--host/--port/--lat/--lng/--batch-size/--frontend-dir/--access-grant/--bucket/--debug`

## Invariants
- Only TCP with non-empty payload is processed (`ipToCoord` skips handshakes, IPv6).
- `myIp` is hardcoded to `192.168.1.100` in `main.go:139` (`getIPv4FromInterface` exists but unused) — direction Upload/Download depends on it.
- Default `--device` is a Windows NPF path; override per machine.
- Broadcast ticks every 6s (`uploadInterval`), drops msgs below `--batch-size`, prunes dead sockets on write fail.
- `GeoLite2-City.mmdb` is gitignored but required at runtime (`--geolite2-path`).
- No tests, no linter config.
