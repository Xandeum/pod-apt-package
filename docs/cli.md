# pNode CLI Usage

Complete reference for all command-line arguments and configuration options.

## Basic Usage

### Start with Default Settings
```bash
pod --keypair /path/to/keypair.json
```

Starts the pnode with default configuration:
- pRPC server on `127.0.0.1:6000` (private)
- Stats server on `127.0.0.1:80` (private)
- Uses default bootstrap node for peer discovery
- Requires a valid Solana-format keypair file

### Check Version
```bash
pod --version
# Output: pod 1.2.0
```

### Display Network Gossip Peers
```bash
pod --gossip
# Queries the default bootstrap node and displays all known peers

# Query a specific node's peer list
pod --gossip --rpc-ip 192.168.1.100
```

### Get Help
```bash
pod --help
# Shows complete usage information with all options and examples
```

**Output:**
```
Xandeum pNode is a high-performance blockchain node that provides JSON-RPC API, 
peer-to-peer communication via gossip protocol, and real-time statistics monitoring.

PORTS:
    6000    pRPC API (configurable IP binding)
    80      Stats dashboard (localhost only)  
    9001    Gossip protocol (peer communication)
    5000    Atlas connection (outbound)

DOCUMENTATION:
    For complete documentation, visit /usr/share/doc/pod/ after installation

Usage: pod [OPTIONS]

Options:
      --keypair <PATH>
          Path to keypair file (required unless using --gossip or --trynet)
          Must be a valid Solana-format JSON keypair
          Auto-generated for --trynet if not provided

      --rpc-ip <IP_ADDRESS>
          Set RPC server IP binding [default: 127.0.0.1 for private]
          
      --entrypoint <IP:PORT>
          Bootstrap node for peer discovery
          Devnet default: 173.212.207.32:9001
          Trynet default: 149.102.137.195:9001

      --no-entrypoint
          Disable peer discovery (run in isolation)

      --atlas-ip <IP:PORT>
          Atlas server address for data streaming
          Devnet default: 95.217.229.171:5000
          Trynet default: 65.108.233.175:5000

      --trynet
          Use Trynet environment configuration
          Auto-generates keypair if not provided

      --gossip
          Display network gossip peers and exit
          Can be combined with --rpc-ip to query specific nodes
          Uses network-specific defaults (--trynet affects target)

      --log <FILE_PATH>
          Create a log file at the specified path
          Logs are written to this file in addition to stdout

  -h, --help
          Print help (see a summary with '-h')

  -V, --version
          Print version
```

## Network Environments

The pod supports two primary network environments:

### Devnet (Default)
Production-ready network for development and testing:
- **Atlas**: `95.217.229.171:5000`
- **Bootstrap**: `173.212.207.32:9001`
- **Gossip**: `161.97.97.41`
- **Requires**: Valid keypair file

```bash
# Start on Devnet (default)
pod --keypair ./my-key.json
```

### Trynet
Experimental test network with simplified setup:
- **Atlas**: `65.108.233.175:5000`
- **Bootstrap**: `149.102.137.195:9001`
- **Gossip**: `149.102.137.195`
- **Auto-generates**: Keypair if not provided

```bash
# Start on Trynet with auto-generated keypair
pod --trynet

# Start on Trynet with specific keypair
pod --trynet --keypair ./trynet-key.json
```

!!! warning "Network Isolation"
    Devnet and Trynet are completely separate networks. Nodes on one network cannot discover or communicate with nodes on the other network.

## Command-Line Arguments

### --keypair `<PATH>`
*Optional* - Path to the Solana-format JSON keypair file.

**Required**: Yes (unless using `--gossip` or `--trynet` flag)

!!! note "Auto-generated Keypairs for Trynet"
    When using `--trynet` without specifying a keypair, a new keypair will be automatically generated and saved as `trynet-keypair.json` in the current directory.

=== "Examples"

    ```bash
    # Use a keypair file in the current directory
    pod --keypair ./key.json

    # Use a keypair file with absolute path
    pod --keypair /home/user/keys/validator-keypair.json

    # Use keypair from Solana default location
    pod --keypair ~/.config/solana/id.json
    ```

!!! info "Keypair Format"
    The keypair must be a valid Solana-format JSON file containing a 64-byte array.
    The pod will validate the keypair on startup by testing signing and verification.

!!! warning "Security Note"
    Keep your keypair file secure and never share it. The keypair identifies your node on the network.

### --rpc-ip `<IP_ADDRESS>`
*Optional* - Specifies the IP address for the pRPC server to bind to.

**Default**: `127.0.0.1` (private, localhost only)

=== "Examples"

    ```bash
    # Private access (default)
    pod --rpc-ip 127.0.0.1

    # Public access from any interface
    pod --rpc-ip 0.0.0.0

    # Bind to specific network interface
    pod --rpc-ip 192.168.1.100

    # IPv6 localhost
    pod --rpc-ip ::1
    ```

!!! warning "Security Note"
    Using `0.0.0.0` makes your pRPC API accessible from any network interface. Only use this if you understand the security implications.

### --entrypoint `<IP:PORT>`
*Optional* - Specifies a bootstrap node to connect to for initial peer discovery.

**Default**: 
- Devnet: `173.212.207.32:9001`
- Trynet: `149.102.137.195:9001` (when using `--trynet`)

=== "Examples"

    ```bash
    # Connect to specific bootstrap node
    pod --entrypoint 192.168.1.50:9001

    # Connect to custom port
    pod --entrypoint 10.0.0.5:9002

    # Use Trynet default bootstrap
    pod --trynet
    ```

### --no-entrypoint
*Optional* - Disables bootstrap peer discovery. The pnode will start without attempting to connect to any initial peers.

=== "Example"

    ```bash
    # Start without peer discovery
    pod --no-entrypoint
    ```

!!! info "Isolated Mode"
    When using `--no-entrypoint`, your pnode will operate in isolation until other pnodes connect to it directly.

### --atlas-ip `<IP:PORT>`
*Optional* - Specifies the Atlas server address for data streaming and synchronization.

**Default**: 
- Devnet: `95.217.229.171:5000`
- Trynet: `65.108.233.175:5000` (when using `--trynet`)

=== "Examples"

    ```bash
    # Connect to local Atlas server
    pod --atlas-ip 127.0.0.1:5000

    # Connect to custom Atlas server
    pod --atlas-ip 10.0.0.10:5000

    # Use Trynet default Atlas
    pod --trynet
    ```

### --version
*Standalone* - Displays the pnode software version and exits immediately.

```bash
pod --version
# Output: pod 1.2.0
```

### --trynet
*Optional* - Use Trynet environment configuration instead of Devnet.

When enabled, the pod will:
- Connect to Trynet Atlas server (`65.108.233.175:5000`)
- Use Trynet bootstrap node (`149.102.137.195:9001`)
- Use Trynet gossip network (`149.102.137.195`)
- Auto-generate a keypair if none is provided

=== "Example"

    ```bash
    # Start on Trynet with auto-generated keypair
    pod --trynet

    # Start on Trynet with specific keypair
    pod --trynet --keypair ./my-key.json
    
    # Query Trynet gossip network
    pod --trynet --gossip
    ```

!!! info "Network Isolation"
    Trynet and Devnet are completely separate networks. Nodes on one network cannot communicate with nodes on the other.

### --gossip
*Standalone* - Displays all known peers in the gossip network and exits immediately. This command queries a pod's RPC endpoint to retrieve the peer list.

**Default Target**: 
- Devnet: `161.97.97.41` (default bootstrap node)
- Trynet: `149.102.137.195` (when using `--trynet` flag)

=== "Examples"

    ```bash
    # Query default bootstrap node for peer list
    pod --gossip

    # Query specific node for its peer list
    pod --gossip --rpc-ip 192.168.1.100

    # Query public node
    pod --gossip --rpc-ip 161.97.97.41
    ```

!!! info "Network Discovery"
    This is useful for discovering active nodes in the network and checking connectivity.

### --log `<FILE_PATH>`
*Optional* - Creates a log file at the specified path. All log output will be written to this file in addition to standard output.

=== "Examples"

    ```bash
    # Log to a file in the current directory
    pod --keypair ./key.json --log ./pod.log

    # Log to a specific path
    pod --keypair ./key.json --log /var/log/pod/pod.log

    # Combine with other options
    pod --keypair ./key.json --rpc-ip 0.0.0.0 --log ./pod.log
    ```

!!! tip "Log Rotation"
    For production deployments, consider using external log rotation tools like `logrotate` to manage log file sizes.

### --help
*Standalone* - Displays complete usage information including all options, examples, and port information.

```bash
pod --help
# Shows complete usage information with all options and examples
```

## Common Usage Patterns

### 🔒 Private Development Setup
```bash
pod --keypair ./dev-key.json --no-entrypoint
```
Perfect for local development and testing. No external connections, pRPC only accessible locally.

### 🌐 Public Node (Devnet)
```bash
pod --keypair ./validator-key.json --rpc-ip 0.0.0.0
```
Runs a public node on Devnet with pRPC API accessible from any network interface. Uses default bootstrap for peer discovery.

### 🧪 Trynet Testing (Auto-generated Keypair)
```bash
pod --trynet
```
Quick start on Trynet with auto-generated keypair. Perfect for testing and experimentation without managing keypairs.

### 🧪 Trynet with Specific Keypair
```bash
pod --trynet --keypair ./trynet-key.json --rpc-ip 0.0.0.0
```
Run on Trynet with a specific keypair and public API access.

### 🏢 Enterprise/Private Network
```bash
pod --keypair ./enterprise-key.json \
    --rpc-ip 192.168.1.100 \
    --entrypoint 192.168.1.50:9001 \
    --atlas-ip 192.168.1.10:5000
```
Configured for private corporate networks with custom Atlas server and internal bootstrap node.

### 🧪 Local Testing with Custom Atlas
```bash
pod --keypair ./test-key.json --atlas-ip 127.0.0.1:5000 --no-entrypoint
```
For testing with a local Atlas server without peer discovery.

### 🔍 Network Discovery
```bash
# Check peers on Devnet
pod --gossip

# Check peers on Trynet
pod --trynet --gossip

# Query specific node for peers
pod --gossip --rpc-ip 10.0.0.5
```
Discover active nodes in the network without running a full node.

## Connection Management

### Idle Timeout
The pod maintains connections with the Atlas server using QUIC protocol. Connections are automatically managed with the following behavior:

- **Idle Timeout**: 60 seconds (1 minute)
- **Reconnection**: Automatic on connection loss
- **Keypair Preservation**: Node identity is maintained across reconnections

!!! info "Connection Behavior"
    If a connection to the Atlas server is idle for more than 60 seconds, it will be closed. The pod will automatically reconnect when needed, preserving your node's identity using the same keypair.

!!! tip "Active Connections"
    During active data streaming and operations, the connection remains open. The 60-second timeout only applies when there is no activity.

## Port Information

| Service | Default Port | Configurable | Description |
|---------|-------------|-------------|-------------|
| pRPC API | 6000 | IP Only | JSON-RPC API endpoint |
| Stats Dashboard | 80 | ❌ Fixed | Web-based statistics dashboard (localhost only) |
| Gossip Protocol | 9001 | ❌ Fixed | Peer-to-peer communication and bootstrap discovery |
| Atlas Connection | 5000 | ❌ Fixed | Connection to Atlas server for data streaming |

### Firewall Configuration

For public nodes, ensure these ports are accessible:

- **Port 6000**: pRPC API (if using `--rpc-ip 0.0.0.0`)
- **Port 9001**: Gossip protocol (always required for peer communication and discovery)
- **Port 5000**: Atlas connection (outbound to Atlas server)

## Error Handling

### Keypair Errors
```bash
pod --keypair invalid-file.json
# Error: Failed to load keypair from invalid-file.json: Keypair file does not exist
```

```bash
pod --keypair corrupted.json
# Error: Keypair validation failed: Invalid keypair file: expected 64 bytes
```

### Missing Keypair
```bash
pod
# error: the following required arguments were not provided:
#   --keypair <PATH>
#
# Usage: pod --keypair <PATH> [OPTIONS]
```

### Invalid IP Address
```bash
pod --rpc-ip invalid-ip
# Error: Invalid IP address 'invalid-ip': invalid IP address syntax
```

### IP Address Not Available
```bash
pod --rpc-ip 192.168.1.200
# Error: Cannot bind to IP address 192.168.1.200 on port 6000: Address not available.
# Make sure the IP address is available on this system.
```

### Missing Argument Value
```bash
pod --rpc-ip
# error: a value is required for '--rpc-ip <IP_ADDRESS>' but none was supplied
# 
# For more information, try '--help'.
```

### Unknown Argument
```bash
pod --invalid-option
# error: unexpected argument '--invalid-option' found
# 
# Usage: pod [OPTIONS]
# 
# For more information, try '--help'.
```

## Configuration Examples

### Development Environment
```bash
# Generate a new keypair (using Solana CLI)
solana-keygen new -o dev-keypair.json

# Start isolated pnode for development
pod --keypair ./dev-keypair.json --no-entrypoint

# Test pRPC locally
curl -X POST http://127.0.0.1:6000/rpc \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"get-version","id":1}'
```

### Production Public Node
```bash
# Start public node with proper logging
RUST_LOG=info pod --keypair /secure/validator-key.json --rpc-ip 0.0.0.0 --log /var/log/pod/pod.log

# Verify pRPC is accessible
curl -X POST http://YOUR_PUBLIC_IP:6000/rpc \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"get-version","id":1}'
```

### Private Network Setup
```bash
# Configure for private network
pod \
  --keypair ./private-key.json \
  --rpc-ip 10.0.1.100 \
  --entrypoint 10.0.1.50:9001 \
  --atlas-ip 10.0.1.10:5000
```

## Logging

### File Logging
Use the `--log` flag to write logs to a file:

```bash
# Log to a file
pod --keypair ./key.json --log ./pod.log

# Log to a specific directory
pod --keypair ./key.json --log /var/log/pod/pod.log

# Combine with public RPC and Trynet
pod --trynet --rpc-ip 0.0.0.0 --log ./trynet.log
```

### Environment Variables

You can also configure logging and other runtime behavior:

```bash
# Set log level
export RUST_LOG=debug
pod --keypair ./key.json

# Enable specific module logging
export RUST_LOG=pod::rpc=debug,pod::gossip=info
pod --keypair ./key.json

# Combine with file logging
RUST_LOG=debug pod --keypair ./key.json --log ./debug.log

# Debug mode for gossip network discovery
export POD_DEBUG=1
pod --gossip
```

## Systemd Service

For production deployments, the pnode can be managed as a systemd service. When installed via apt, the service file is automatically configured.

```ini
[Unit]
Description=Xandeum Pod System service
After=network.target

[Service]
ExecStart=/usr/bin/pod --keypair /etc/pod/keypair.json --rpc-ip 0.0.0.0 --log /var/log/pod/pod.log
Restart=always
User=pod
Environment=NODE_ENV=production
Environment=RUST_LOG=info
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=xandeum-pod

[Install]
WantedBy=multi-user.target
```

```bash
# Enable and start the service
sudo systemctl enable pod
sudo systemctl start pod

# Check status
sudo systemctl status pod

# View logs
sudo journalctl -u pod -f
```

## Troubleshooting

### Port Already in Use
```bash
# Check what's using port 6000
sudo lsof -i :6000

# Kill process using the port
sudo kill -9 <PID>
```

### Network Interface Issues
```bash
# List available network interfaces
ip addr show

# Test if IP is accessible
ping <your-ip>
```

### Peer Discovery Problems
```bash
# Test connectivity to bootstrap node
nc -u 173.212.207.32 9001

# Check local gossip port
sudo netstat -ulnp | grep 9001
```

!!! tip "Logging"
    Use `RUST_LOG=debug` to get detailed logs for troubleshooting network and configuration issues. 