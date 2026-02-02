# DTPX - Distributed Torrent-based Peer-to-Peer File Exchange

A Python-based distributed file downloading system that accelerates downloads by distributing work across multiple peer servers.

## Architecture

The system consists of three components:

| Component | Purpose | Default Port |
|-----------|---------|--------------|
| **Tracker** (`tracker.py`) | Central registry that coordinates peers | 5000 |
| **Peer Server** (`server.py`) | Downloads file segments on behalf of clients | 6000 |
| **Peer Client** (`client.py`) | Initiates downloads, distributes work to peers | - |

## How It Works

1. **Peer Servers** register with the **Tracker**
2. **Client** requests a file URL and asks the Tracker for available peers
3. **Client** divides the file into segments (one per peer)
4. Each **Peer Server** downloads its assigned segment in parallel
5. **Client** reassembles all segments into the final file

```
┌────────┐         ┌─────────┐         ┌─────────────┐
│ Client │ ──1──▶  │ Tracker │ ◀──reg──│ Peer Server │
└────────┘         └─────────┘         └─────────────┘
    │                                        ▲
    │                                        │
    └────────────2. Request Segments─────────┘
                 3. Download & Return
```

## Project Structure

```
dtpx/
├── tracker.py              # Tracker entry point
├── server.py               # Peer server entry point
├── client.py               # Client entry point
├── trackermodule/          # Tracker logic
├── peerserver/             # Server logic
├── peerclient/             # Client logic
├── utils/                  # Shared utilities (downloader, file handling)
└── configfiles/            # Configuration (ports, threads, directories)
```

## Usage

### 1. Start the Tracker

```bash
python tracker.py
```

### 2. Start Peer Server(s)

Run on one or more machines:

```bash
python server.py
```

### 3. Download a File

```bash
python client.py <file_url>
```

## Configuration

Configuration files are located in `configfiles/`:

- `tracker-config.ini` - Tracker port settings
- `peer-server-config.ini` - Server port, tracker address, threads
- `peer-client-config.ini` - Tracker address, download directory, threads, proxy settings

## Fallback Behavior

- If no peer servers are available, the client falls back to multithreaded download locally
- If the target URL doesn't support range requests, same fallback applies

## Requirements

- Python 3.8+
- urllib3
