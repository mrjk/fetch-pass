
# 🚀 Fetch Pass


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
```bash
asdf install secret-access-tool latest
mise install secret-access-tool
```

### Install via Curl
```bash
curl -o secret-access-tool.sh https://raw.githubusercontent.com/author/secret-access-tool/main/secret-access-tool.sh
chmod +x secret-access-tool.sh
mv secret-access-tool.sh /usr/local/bin/secret-access-tool
```

### Install from Source
```bash
git clone https://github.com/author/secret-access-tool.git
cd secret-access-tool
chmod +x secret-access-tool.sh
mv secret-access-tool.sh /usr/local/bin/secret-access-tool
```

## 🚀 Quickstart

### Basic Usage
Define a backend and query the secret:

```bash
fetch-pass get my_secret
```

### Adding to Local Keyring
```bash
fetch-pass keyring add my_secret
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
- `$XDG_CONFIG_HOME/secret-access-tool/config.ini`
- `$HOME/.config/secret-access-tool/config.ini`

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
fetch-pass --help
```

## ❗ Known Issues
- None

## 💬 Support
Feedback is appreciated! Please submit a PR or open an issue on the GitHub repository.

## 📜 Other Information
- **Author**: Your Name
- **License**: GPLv3
- **Repository**: [GitHub](https://github.com/author/secret-access-tool)
