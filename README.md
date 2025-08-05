# Reliable Data Transfer (RDT) Protocol v2.2 Implementation

[![Python](https://img.shields.io/badge/Python-3.7+-blue.svg)](https://www.python.org/downloads/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Complete-brightgreen.svg)]()

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Installation](#installation)
- [Usage](#usage)
- [Configuration](#configuration)
- [Technical Details](#technical-details)
- [Protocol Specification](#protocol-specification)
- [Examples](#examples)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

This project implements a **Reliable Data Transfer (RDT) Protocol version 2.2** simulation, demonstrating how reliable communication can be achieved over an unreliable network. The implementation showcases fundamental networking concepts including error detection, flow control, and retransmission mechanisms.

### What is RDT Protocol?

The RDT Protocol is a simplified version of TCP (Transmission Control Protocol) that ensures reliable data transmission over unreliable networks. It handles:

- **Packet corruption detection** using checksums
- **Duplicate packet detection** using sequence numbers
- **Automatic retransmission** of lost or corrupted packets
- **Stop-and-wait flow control** mechanism

## ✨ Features

### Core Functionality
- ✅ **Reliable Data Transfer**: Ensures message delivery despite network issues
- ✅ **Error Detection**: Checksum-based corruption detection
- ✅ **Flow Control**: Stop-and-wait protocol implementation
- ✅ **Retransmission**: Automatic packet resending on failure
- ✅ **Sequence Numbering**: Alternating 0/1 sequence for duplicate detection

### Network Simulation
- 🌐 **Unreliable Network Layer**: Simulates real-world network conditions
- 🔧 **Configurable Reliability**: Adjustable packet delivery probability
- ⏱️ **Variable Delays**: Configurable round-trip time simulation
- 🐛 **Controlled Corruption**: Simulate packet and ACK corruption

### Debugging & Monitoring
- 📊 **Debug Mode**: Detailed logging of packet transmission
- 🔍 **Packet Inspection**: View packet contents and checksums
- 📈 **Performance Monitoring**: Track retransmission attempts

## 🏗️ Architecture

### System Components

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Application   │    │   Transport     │    │     Network     │
│     Layer       │    │     Layer       │    │     Layer       │
├─────────────────┤    ├─────────────────┤    ├─────────────────┤
│ SenderProcess   │◄──►│   RDTSender     │◄──►│  NetworkLayer   │
│ ReceiverProcess │◄──►│  RDTReceiver    │◄──►│                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

### File Structure

```
Networks/
├── main.py          # Entry point and orchestration
├── network.py       # Unreliable network layer simulation
├── sender.py        # RDT sender implementation
├── receiver.py      # RDT receiver implementation
└── README.md        # This file
```

## 🚀 Installation

### Prerequisites

- **Python 3.7 or higher**
- **No external dependencies required** (uses only Python standard library)

### Setup

1. **Clone or download the project**
   ```bash
   git clone <repository-url>
   cd Networks
   ```

2. **Verify Python installation**
   ```bash
   python --version
   # Should display Python 3.7+
   ```

3. **Ready to run!** No additional setup required.

## 📖 Usage

### Basic Usage

```bash
python main.py msg="Hello World" rel=0.8 delay=1 debug=0
```

### Command Line Arguments

| Parameter | Type | Description | Default | Example |
|-----------|------|-------------|---------|---------|
| `msg` | string | Message to send | Required | `msg="Hello"` |
| `rel` | float | Network reliability (0.0-1.0) | 1.0 | `rel=0.8` |
| `delay` | int | Network delay in seconds | 1 | `delay=2` |
| `debug` | int | Enable debug mode (0/1) | 0 | `debug=1` |
| `pkt` | int | Corrupt packets (0/1) | 1 | `pkt=1` |
| `ack` | int | Corrupt ACKs (0/1) | 1 | `ack=0` |

### Usage Examples

#### 1. Simple Message Transmission
```bash
python main.py msg="Hello World"
```

#### 2. Unreliable Network Simulation
```bash
python main.py msg="Test Message" rel=0.6 delay=2
```

#### 3. Debug Mode with Corruption
```bash
python main.py msg="Debug Test" rel=0.7 delay=1 debug=1 pkt=1 ack=1
```

#### 4. High Reliability Network
```bash
python main.py msg="Important Data" rel=0.95 delay=0.5 debug=0
```

## ⚙️ Configuration

### Network Parameters

| Parameter | Range | Effect |
|-----------|-------|--------|
| `reliability` | 0.0 - 1.0 | Higher values = more reliable network |
| `delay` | 0+ seconds | Simulates network latency |
| `pkt_corrupt` | true/false | Enables packet corruption |
| `ack_corrupt` | true/false | Enables ACK corruption |

### Debug Options

- **Debug Mode**: Provides detailed packet transmission logs
- **Packet Inspection**: View packet contents, checksums, and sequence numbers
- **Corruption Control**: Fine-tune corruption simulation

## 🔧 Technical Details

### Packet Structure

```python
# Sender Packet
{
    'sequence_number': '0' or '1',  # Alternating sequence
    'data': 'character',            # Single character to send
    'checksum': int                 # ASCII value for error detection
}

# Receiver ACK Packet
{
    'ack': '0' or '1',             # Acknowledged sequence number
    'checksum': int                 # Checksum of ACK
}
```

### Checksum Algorithm

```python
def get_checksum(data):
    """Calculate checksum for error detection"""
    return ord(data)  # ASCII value of character
```

### Error Detection

- **Corruption Detection**: Compares received checksum with calculated checksum
- **Sequence Validation**: Ensures packets arrive in correct order
- **Duplicate Detection**: Identifies and handles duplicate packets

## 📋 Protocol Specification

### RDT v2.2 Features

1. **Stop-and-Wait Protocol**
   - Sender transmits one packet at a time
   - Waits for acknowledgment before sending next packet

2. **Error Detection**
   - Checksum-based corruption detection
   - Sequence number validation

3. **Retransmission**
   - Automatic resending of corrupted/lost packets
   - Continues until successful acknowledgment

4. **Flow Control**
   - Prevents sender from overwhelming receiver
   - Ensures reliable delivery

### State Machine

```
Sender States:
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   WAIT      │───►│   SEND      │───►│   WAIT_ACK  │
│  CALL_FROM  │    │   PACKET    │    │             │
│   APP       │    │             │    │             │
└─────────────┘    └─────────────┘    └─────────────┘
       ▲                                    │
       │                                    │
       └─────────────┐    ┌─────────────┐   │
                     │    │   TIMEOUT   │◄──┘
                     └────┤             │
                          └─────────────┘
```

## 💡 Examples

### Example 1: Successful Transmission

```bash
$ python main.py msg="Hello" rel=0.9 delay=1 debug=1

Output:
Sender is sending: ['H', 'e', 'l', 'l', 'o']
[Debug logs showing packet transmission]
Sender Done!
Receiver received: ['H', 'e', 'l', 'l', 'o']
```

### Example 2: Network with Corruption

```bash
$ python main.py msg="Test" rel=0.5 delay=2 debug=1

Output:
Sender is sending: ['T', 'e', 's', 't']
[Debug logs showing retransmissions due to corruption]
Sender Done!
Receiver received: ['T', 'e', 's', 't']
```

### Example 3: High-Delay Network

```bash
$ python main.py msg="Data" rel=0.8 delay=5 debug=0

Output:
Sender is sending: ['D', 'a', 't', 'a']
[Waits 5 seconds between packets]
Sender Done!
Receiver received: ['D', 'a', 't', 'a']
```

## 🔍 Troubleshooting

### Common Issues

#### 1. **Python Version Error**
```bash
Error: Python version not supported
Solution: Ensure Python 3.7+ is installed
```

#### 2. **Missing Arguments**
```bash
Error: Missing required argument 'msg'
Solution: Provide all required arguments
```

#### 3. **Invalid Parameter Values**
```bash
Error: Reliability must be between 0.0 and 1.0
Solution: Use valid parameter ranges
```

### Debug Tips

1. **Enable Debug Mode**: Use `debug=1` to see detailed packet information
2. **Check Network Reliability**: Lower `rel` values simulate more unreliable networks
3. **Monitor Retransmissions**: Debug mode shows retransmission attempts
4. **Verify Checksums**: Debug output shows checksum calculations

### Performance Optimization

- **High Reliability Networks**: Use `rel=0.9+` for minimal retransmissions
- **Low Delay**: Use `delay=0.1` for faster simulation
- **Debug Mode**: Disable for production use (`debug=0`)

## 🤝 Contributing

We welcome contributions! Here's how you can help:

### Development Setup

1. **Fork the repository**
2. **Create a feature branch**: `git checkout -b feature/amazing-feature`
3. **Make your changes**
4. **Test thoroughly**: Run with various parameters
5. **Submit a pull request**

### Contribution Guidelines

- **Code Style**: Follow PEP 8 Python style guidelines
- **Documentation**: Update README for new features
- **Testing**: Test with various network conditions
- **Bug Reports**: Include detailed reproduction steps

### Areas for Improvement

- [ ] Add support for larger packet sizes
- [ ] Implement sliding window protocol
- [ ] Add network congestion simulation
- [ ] Create GUI interface
- [ ] Add performance benchmarking tools

---
## 🙏 Acknowledgments

- **Computer Networking**: Concepts based on networking fundamentals
- **TCP Protocol**: Inspiration from Transmission Control Protocol
- **Educational Purpose**: Designed for learning networking concepts

## 📞 Support

For questions, issues, or contributions:

- **Issues**: Create an issue in the repository
- **Discussions**: Use GitHub Discussions for questions
- **Email**: Contact maintainers for direct support

---

**Made with ❤️ for educational purposes**

*This project demonstrates fundamental networking concepts and is perfect for learning about reliable data transfer protocols.* 


## 🛡️ License

This project is licensed under the [MIT License](LICENSE).

---

## 🙌 Author

**Momen H.**  
📂 [GitHub Profile »](https://github.com/Momenh2)


