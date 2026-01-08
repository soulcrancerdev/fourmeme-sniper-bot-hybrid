# 🚀 Meteora DAMM V2 Monitor

<div align="center">

![Rust](https://img.shields.io/badge/rust-%23000000.svg?style=for-the-badge&logo=rust&logoColor=white)
![Solana](https://img.shields.io/badge/Solana-9945FF?style=for-the-badge&logo=solana&logoColor=white)
![License](https://img.shields.io/badge/license-MIT-blue.svg?style=for-the-badge)

**Real-time Solana transaction monitoring and automated copy trading for Meteora DAMM V2**

[Features](#-features) • [Installation](#-installation) • [Configuration](#-configuration) • [Usage](#-usage) • [Architecture](#-architecture)

</div>

---

## ✨ Features

- 🔍 **Real-time Monitoring**: Monitor Meteora DAMM V2 swaps using Yellowstone gRPC and Helius Laserstream
- ⚡ **Low-Latency Execution**: Automatically execute copy trades when target wallet swaps are detected
- 🎯 **Multi-Service Support**: Choose from Jito, Nozomi, or Zero Slot for transaction confirmation
- 💰 **Smart Quote Calculation**: Automatically calculates swap amounts based on detected transactions
- 🛡️ **Slippage Protection**: Configurable slippage tolerance to protect against unfavorable price movements
- 🔐 **Secure**: Built with Rust for memory safety and performance
- 📊 **Comprehensive Logging**: Detailed transaction logs with timestamps and performance metrics

## 🏗️ Architecture

```
┌─────────────────┐
│  Yellowstone    │
│  gRPC Geyser    │────┐
└─────────────────┘    │
                       ├──► Carbon Pipeline ──► Meteora DAMM V2 Decoder
┌─────────────────┐    │                              │
│  Helius          │────┘                              ▼
│  Laserstream     │                    ┌─────────────────────────┐
└─────────────────┘                    │  Transaction Processor   │
                                       │  - Detect Swap Events    │
                                       │  - Calculate Quotes      │
                                       │  - Build Instructions    │
                                       └──────────┬────────────────┘
                                                  │
                    ┌─────────────────────────────┼─────────────────────────────┐
                    │                             │                             │
                    ▼                             ▼                             ▼
            ┌──────────────┐            ┌──────────────┐            ┌──────────────┐
            │    Jito      │            │   Nozomi     │            │  Zero Slot   │
            │  Confirmation│            │ Confirmation │            │ Confirmation │
            └──────────────┘            └──────────────┘            └──────────────┘
```

## 📋 Prerequisites

- **Rust** (latest stable version)
- **Solana CLI** (for keypair management)
- **Geyser endpoint** (Yellowstone gRPC or Helius Laserstream)
- **Solana wallet** with sufficient balance for swaps and fees

## 🚀 Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/soulcrancerdev/meteora-damm-v2-monitor.git
   cd meteora-damm-v2-monitor
   ```

2. **Install dependencies**
   ```bash
   cargo build --release
   ```

3. **Set up environment variables**
   ```bash
   cp .env.example .env
   # Edit .env with your configuration
   ```

## ⚙️ Configuration

Create a `.env` file in the project root with the following variables:

```env
# Target wallet to monitor (required)
TARGET_WALLET=YourTargetWalletAddress

# Your wallet keypair path (required)
WALLET_KEYPAIR_PATH=/path/to/your/keypair.json

# RPC endpoint (required)
RPC_URL=https://api.mainnet-beta.solana.com

# Geyser endpoint (required)
GEYSER_URL=your-geyser-endpoint-url

# Optional: Geyser authentication token
X_TOKEN=your-geyser-token

# Helius Laserstream endpoint (optional, for redundancy)
LASER_ENDPOINT=your-laserstream-endpoint
LASER_TOKEN_KEY=your-laserstream-token

# Confirmation service: JITO, NOZOMI, or ZERO_SLOT (required)
CONFIRM_SERVICE=JITO

# Jito configuration (if using Jito)
JITO_BLOCK_ENGINE_URL=https://mainnet.block-engine.jito.wtf
JITO_TIP_ACCOUNT=your-jito-tip-account

# Nozomi configuration (if using Nozomi)
NOZOMI_API_URL=your-nozomi-api-url
NOZOMI_API_KEY=your-nozomi-api-key

# Zero Slot configuration (if using Zero Slot)
ZSLOT_API_URL=your-zslot-api-url
ZSLOT_API_KEY=your-zslot-api-key

# Trading parameters
BUY_SOL_AMOUNT=0.1                    # Amount of SOL to use per swap (in SOL)
SLIPPAGE=1.0                          # Slippage tolerance (percentage, e.g., 1.0 = 1%)

# Priority fees
CU=200000                              # Compute units
PRIORITY_FEE_MICRO_LAMPORT=100000     # Priority fee in micro-lamports
THIRD_PARTY_FEE=0.001                 # Tip amount for confirmation service (in SOL)
```

## 🎮 Usage

### Basic Usage

```bash
cargo run --release
```

The monitor will:
1. Connect to your configured Geyser endpoints
2. Start monitoring transactions involving the target wallet
3. Automatically execute copy trades when swaps are detected
4. Log all activity with timestamps and performance metrics

### Example Output

```
Using payer: YourWalletAddress...
Starting Meteora DAMM V2 Monitor...
Received target's signature : Signature123...
Current time : 2024-01-15T10:30:45Z
Target Bought 1000.5 tokens with 0.1 sol
Submitting tx --> Current time: 2024-01-15T10:30:45.123Z
Period from start: 45ms
Transaction confirmed --> : {"result": "success"}
Current time: 2024-01-15T10:30:45.456Z
Period from start: 378ms
```

## 🔧 Advanced Configuration

### Multiple Data Sources

The monitor supports multiple Geyser endpoints for redundancy. Configure both `GEYSER_URL` and `LASER_ENDPOINT` to use dual data sources.

### Confirmation Service Selection

Choose your preferred confirmation service based on your needs:

- **Jito**: Best for MEV protection and bundle inclusion
- **Nozomi**: Fast confirmation with competitive pricing
- **Zero Slot**: Ultra-low latency execution

### Slippage Protection

The `SLIPPAGE` parameter protects against unfavorable price movements. Set it based on your risk tolerance:
- `0.5` = 0.5% slippage tolerance (conservative)
- `1.0` = 1.0% slippage tolerance (moderate)
- `2.0` = 2.0% slippage tolerance (aggressive)

## 📁 Project Structure

```
meteora-damm-v2-monitor/
├── src/
│   ├── main.rs                 # Main entry point
│   ├── lib.rs                  # Library exports
│   ├── config/                 # Configuration management
│   │   ├── clients.rs          # Client initialization
│   │   ├── credentials.rs      # Credential handling
│   │   └── trade_setting.rs    # Trading parameters
│   ├── instructions/           # Instruction builders
│   │   └── swap.rs             # Swap instruction logic
│   ├── service/                 # External service integrations
│   │   ├── jito/               # Jito integration
│   │   ├── nozomi/             # Nozomi integration
│   │   ├── zero_slot/          # Zero Slot integration
│   │   └── utils/              # Service utilities
│   └── utils/                  # Helper utilities
│       ├── blockhash.rs        # Blockhash management
│       ├── build_and_sign.rs   # Transaction building
│       ├── parse.rs            # Transaction parsing
│       ├── swap_quote.rs       # Quote calculation
│       └── utils.rs            # General utilities
├── Cargo.toml                  # Rust dependencies
└── README.md                   # This file
```

## 🛠️ Development

### Building

```bash
# Debug build
cargo build

# Release build (optimized)
cargo build --release
```

### Running Tests

```bash
cargo test
```

### Code Formatting

```bash
cargo fmt
```

### Linting

```bash
cargo clippy
```

## ⚠️ Important Notes

- **Risk Warning**: Automated trading carries significant risk. Only use funds you can afford to lose.
- **Network Fees**: Be aware of network fees, priority fees, and confirmation service tips.
- **Slippage**: Market conditions can cause slippage. Configure appropriate tolerance levels.
- **Monitoring**: Always monitor the bot's activity and performance.
- **Keypair Security**: Never share your wallet keypair. Keep it secure and backed up.

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🙏 Acknowledgments

- [Carbon](https://github.com/carbon-xyz) - Carbon Core framework
- [Meteora](https://www.meteora.ag/) - Meteora DAMM V2 protocol
- [Yellowstone gRPC](https://github.com/rpcpool/yellowstone-grpc) - Geyser client
- [Jito](https://www.jito.wtf/) - MEV infrastructure
- [Nozomi](https://nozomi.finance/) - Fast confirmation service
- [Zero Slot](https://zeroslot.io/) - Low-latency execution

## 📞 Support

For issues, questions, or contributions, please open an issue on GitHub.

---

<div align="center">

**Built with ❤️ for the Solana ecosystem**

⭐ Star this repo if you find it useful!

</div>

