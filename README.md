# Network Globe - Real-Time TCP Packet Sniffer for Network Location Visualization

See where your TCP packets are coming from/going to in Real-time!

This repository contains a Real-time TCP packet sniffer for network visualization. See the various locations where TCP packets are sent or received. The application parses IP addresses from packets, looks up their locations (lat and lng) using GeoLite2, and visualizes the packet data on a globe using Globe GL.

![Network Globe Visualization](./globe.png)

See a [demo](https://demo.storj.dev)

## Features

- Parses IP addresses from TCP packets using [pcap](https://pkg.go.dev/github.com/google/gopacket/pcap)
- Performs location lookup using [GeoLite2](https://dev.maxmind.com/geoip/geolite2-free-geolocation-data?lang=en)
- Visualizes locations on a globe with [Globe GL](https://globe.gl/)
- Demo file uploads with [Storj](https://storj.io?ref=network-globe)

## Installation

### Prerequisites

- Packet capture library: libpcap (Linux/macOS) or [Npcap](https://npcap.com/) (Windows)
  - Windows: install Npcap with "WinPcap API-compatible mode" checked (requires admin). No `chmod` step needed.
  - Linux/macOS: libpcap (see Install/Build below)
- GeoLite2-City.mmdb database from [MaxMind](https://dev.maxmind.com/geoip/geolite2-free-geolocation-data?lang=en)
  - Obtain the Free [GeoLite2-City.mmdb database](https://dev.maxmind.com/geoip/geolite2-free-geolocation-data?lang=en)
  - Extract and place the database file (`GeoLite2-City.mmdb`) in the project directory or specify path with `--geolite2-path` flag

### Install

#### Ubuntu

```bash
sudo apt install libpcap-dev zip
wget https://github.com/amozoss/network-globe/releases/latest/download/network-globe_linux_amd64.zip
unzip network-globe_linux_amd64.zip
```

#### macOS

```bash
wget https://github.com/amozoss/network-globe/releases/latest/download/network-globe_darwin_arm.zip
unzip network-globe_darwin_arm.zip
```

#### Windows

1. Install [Npcap](https://npcap.com/#download) (check "WinPcap API-compatible mode"), then reboot if prompted.
2. Download the Windows release (or build from source below) and place `GeoLite2-City.mmdb` next to `network-globe.exe`.
3. Open PowerShell **as Administrator** (packet capture requires elevation):

```powershell
.\network-globe.exe --list-devices
.\network-globe.exe --device "\Device\NPF_{YOUR-DEVICE-GUID}" --geolite2-path .\GeoLite2-City.mmdb
```

## Usage

1.  Give read permissions for pcap to read the network packets:

    ```bash
    # for mac
    sudo chmod +r /dev/bpf*
    ```

1.  Configure your network interface:

    ```bash
    # List available network devices
    ./network-globe --list-devices
    # Default device is en0
    ./network-globe --device en0
    ```

    On Windows (PowerShell as Administrator), device names look like `\Device\NPF_{...}`:

    ```powershell
    .\network-globe.exe --list-devices
    .\network-globe.exe --device "\Device\NPF_{YOUR-DEVICE-GUID}"
    ```

1.  Set the GeoLite2 database path:

    ```bash
    # Default path is GeoLite2-City.mmdb
    ./network-globe --geolite2-path GeoLite2-City.mmdb
    ```

1.  Start network-globe:

    ```bash
    # may need to run with sudo on ubuntu
    ./network-globe
    ```

    On Windows, run PowerShell as Administrator:

    ```powershell
    .\network-globe.exe
    ```

1.  Open the browser and navigate to <http://localhost:8000>

1.  (Optional) Set the source location (Latitude and Longitude) for the Globe visualization:

    The origin is configured to be in the United States. You can set the source location using the `--lat` and `--lng` flags.

    ```bash
    ./network-globe --lat 39.781932 --lng -104.970578
    ```


## Build

Clone the repository:

```bash
git clone https://github.com/amozoss/network-globe.git
cd network-globe
```

### linux

```bash
sudo apt install libpcap-dev gcc
CGO_ENABLED=1 go build
```

### macOS

```bash
go build
```

### Windows

Requires Go, gcc (e.g. [TDM-GCC](https://jmeubank.github.io/tdm-gcc/) or mingw-w64), and the [Npcap SDK](https://npcap.com/#download):

```powershell
# Point CGO at the Npcap SDK (adjust path to where you extracted it)
$env:CGO_CFLAGS="-I C:\npcap-sdk\Include"
$env:CGO_LDFLAGS="-L C:\npcap-sdk\Lib\x64 -lwpcap -lPacket"
go build
```

Then run PowerShell as Administrator:

```powershell
.\network-globe.exe --list-devices
.\network-globe.exe --device "\Device\NPF_{YOUR-DEVICE-GUID}" --geolite2-path .\GeoLite2-City.mmdb
```

## Project Structure

- `public/index.html`: HTML file for the Globe GL visualization and websocket connection.
- `main.go`: packet sniffer and IP to lat and lng lookup written in Go using pcap library and MaxMind.
- `server.go`: Handles the packets, formats the message for the frontend, and broadcasts them to the client.

## Contributing

Contributions are welcome! Please submit a pull request or open an issue for any improvements or bug fixes.

## License

This project is licensed under the MIT License.

## Acknowledgements

- [Pcap tutorial](https://www.devdungeon.com/content/packet-capture-injection-and-analysis-gopacket)
- [MaxMind GeoLite2](https://dev.maxmind.com/geoip/geolite2-free-geolocation-data?lang=en)
- [Globe GL](https://github.com/vasturiano/globe.gl)
- [Storj](https://storj.io?ref=network-globe)
