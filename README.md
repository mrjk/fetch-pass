# 🚀 Keyring Proxy


A simple tool to access secrets from various sources. It's perfect for fetching passwords in terminal or scripts and provides a simple endpoint to display a password for a given secret name. It also allows integration with various password managers.

### Highlights

- **Secret Management**: Easily manage any secret backends with a simple configuration.
- **Local Keyring Support**: Integrates with the local keyring daemon for faster access.
- **Portable Bash Script**: Works out of the box on most Unix-based systems.


## 📚 Table of Contents
- [Installation](#installation)
- [Quickstart](#quickstart)
- [Usage](#usage)
- [Configuration File](#configuration-file)
- [Known Issues](#known-issues)
- [Support](#support)
- [Other Information](#other-information)

## 💻 Installation

### Dependencies
Ensure the following dependencies are installed:
- [`git-db`]()

### Install via ASDF or Mise

Can be installed by both `mise` or `asdf`:
```bash
mise plugin install keyring-proxy https://github.com/mrjk/asdf-keyring-proxy.git
mise use keyring-proxy

# With asdf
asdf plugin add keyring-proxy https://github.com/mrjk/asdf-keyring-proxy.git
asdf install keyring-proxy latest
asdf global keyring-proxy latest
```

### Install via Curl

Install it via curl:
```bash
curl -o keyring-proxy https://raw.githubusercontent.com/mrjk/keyring-proxy/main/keyring-proxy
chmod +x keyring-proxy
mv keyring-proxy /usr/local/bin/keyring-proxy
```

### Install from Source

Install via git:
```bash
git clone https://github.com/mrjk/keyring-proxy.git
cd keyring-proxy
chmod +x keyring-proxy
ln -s keyring-proxy /usr/local/bin/keyring-proxy
```

### Verify installation

Should be able to get `keyring-proxy` version and help message:
```bash
keyring-proxy --version
keyring-proxy --help
```

## 🚀 Quickstart

### Basic Usage
Define a backend and query the secret:

```bash
keyring-proxy get my_secret
```

### Adding to Local Keyring
```bash
keyring-proxy keyring add my_secret
```

### Simple INI Configuration Example
```ini
[config]
backends = keyring_corp,keepass_corp,keepass_personal

[backend "keepass_personal"]
cmd-fetch = keepass-cli get personal %s
remap = corp_user|Pro/Job/my_user@corp.io
```

## ⚙️ Usage

### Configuration Path Lookup
The tool looks for the configuration file in the following paths:
- `$XDG_CONFIG_HOME/keyring-proxy/config.ini`
- `$HOME/.config/keyring-proxy/config.ini`

### Environment Variables
- `APP_DRY`: Enable dry run mode (default: `false`).
- `APP_FORCE`: Force execution (default: `false`).

## 📄 Configuration File

### INI Configuration Format
The configuration is in INI format and allows defining multiple backends and secrets. Here's an example:

```ini
[config]
backends = keyring_corp,keepass_corp,keepass_personal

[backend "keepass_personal"]
cmd-fetch = keepass-cli get personal %s
remap = corp_user|Pro/Job/my_user@corp.io
```

## 💡 Usage

### Command-Line Help
```bash
keyring-proxy --help
```

## ❗ Known Issues
- None

## 💬 Support
Feedback is appreciated! Please submit a PR or open an issue on the GitHub repository.

## 📜 Other Information
- **Author**: Your Name
- **License**: GPLv3
- **Repository**: [GitHub](https://github.com/mrjk/keyring-proxy)
