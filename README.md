
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

Can be installed by both `mise` or `asdf`:
```bash
mise plugin install fetch-pass https://github.com/mrjk/asdf-fetch-pass.git
mise use fetch-pass

# With asdf
asdf plugin add fetch-pass https://github.com/mrjk/asdf-fetch-pass.git
asdf install fetch-pass latest
asdf global fetch-pass latest
```

### Install via Curl

Install it via curl:
```bash
curl -o fetch-pass https://raw.githubusercontent.com/mrjk/fetch-pass/main/fetch-pass
chmod +x fetch-pass
mv fetch-pass /usr/local/bin/fetch-pass
```

### Install from Source

Install via git:
```bash
git clone https://github.com/author/fetch-pass.git
cd fetch-pass
chmod +x fetch-pass
ln -s fetch-pass /usr/local/bin/fetch-pass
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
- `$XDG_CONFIG_HOME/fetch-pass/config.ini`
- `$HOME/.config/fetch-pass/config.ini`

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
- **Repository**: [GitHub](https://github.com/author/fetch-pass)
