# 🚀 Keyring Proxy

A simple tool to access secrets from various sources. It's perfect for fetching passwords in terminal or scripts and provides a simple endpoint to display a password for a given secret name. It also allows integration with any password managers that support CLI or API.


### Highlights

- **Secret Management**: Easily manage any secret backends with a simple configuration.
- **Dispatch rules**: You can specify query rules depending the secret name that forward to specific backend.
- **Local keyring proxy**: Sometimes, you just want to use local keyring as a fast proxy in front of slow/remote bakends. This is now easy to sync your local keyring password from other source of truth.
- **Query history**: Quickly find previously passwords. Never try to remember again your previously entered secrets. But it can be disabled for more privacy.
- **Local Keyring Support**: Integrates with the local keyring daemon for faster access. But it can be disabled.
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

Current stable version is: 0.1.1

### Dependencies

Ensure the following dependencies are installed:
- [`git`](https://git-scm.com/)
- [`git-db`](https://github.com/mrjk/git-db), also installable via [asdf-git-db](https://github.com/mrjk/asdf-git-db)


### Install via ASDF or Mise

Can be installed by both `mise` or `asdf` via its own [`asdf-keyring-proxy`](https://github.com/mrjk/asdf-keyring-proxy) plugin:
```bash
# With mise
mise plugin install keyring-proxy https://github.com/mrjk/asdf-keyring-proxy.git
mise use keyring-proxy

# With asdf
asdf plugin add keyring-proxy https://github.com/mrjk/asdf-keyring-proxy.git
asdf install keyring-proxy latest
asdf global keyring-proxy latest
```

### Install via Curl

Install it via curl (oneliner):
```bash
curl \
    -o /usr/local/bin/keyring-proxy \
    "https://raw.githubusercontent.com/mrjk/keyring-proxy/main/keyring-proxy" \
    && chmod +x /usr/local/bin/keyring-proxy
```

### Install from Source

Install via git:
```bash
git clone https://github.com/mrjk/keyring-proxy.git
cd keyring-proxy
chmod +x keyring-proxy
ln -s $PWD/keyring-proxy /usr/local/bin/keyring-proxy
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

### Check available secrets

Look at your config remaps:
```bash
keyring-proxy ls
```

Is history is enabled (true by default):
```bash
keyring-proxy hist
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

Specific app vars:

- `KEYRING_PROXY__CONF`: Keyring proxy configuration file.
- `KEYRING_PROXY__DISABLE_HIST`: Disable history (default: `false`).
- `KEYRING_PROXY__DISABLE_KEYRING`: Disable local keyring (default: `false`).

Generic app vars:

- `APP_DRY`: Enable dry run mode (default: `false`).
- `APP_FORCE`: Force execution (default: `false`).


## 📄 Configuration File


### Backends

TODO

### Secrets

TODO

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

Command line usage:
```
keyring-proxy is command line tool to proxy secret keyring queries among different backends

usage: keyring-proxy <COMMAND> <TARGET> [<ARGS>]
       keyring-proxy help

commands:
  config            OPTS                  Query config
  db                OPTS                  Query config
  get               NAME                  Get password name
  hist                                    Show query history
  keyring           COMMAND               Manage keyring
  keyring add       NAME [BACKEND]        Add secret to local keyring
  keyring rm        NAME [BACKEND]        Remove secret to local keyring
  keyring st        NAME [BACKEND]        Show secret status on local keyring
  ls                                      List secret names
  usage                                   Show usage

options:
  -h|--help|h                             Show this help message
  -n|--dry                                Enable dry mode
  -f|--force                              Enable force mode
  -K|--no-keyring                         Disable local keyring
  -v|-vv|-vvv|--verbose  [LEVEL]               Set verbosity level
  -V|--version                            Show version

info:
  config dir:   /home/mrjk/.config/keyring-proxy
  history file: /home/mrjk/.cache/keyring-proxy/hist.log (enabled)
  author:       mrjk <mrjk.78 at gmail.com>
  version:      0.1.1-stable (2024-08-17)
  license:      GPLv3
  website:      https://github.com/mrjk/keyring-proxy
```

## ❗ Known Issues

- Only tested on linux

## 💬 Support
Feedback is appreciated! Please submit a PR or open an issue on the GitHub repository.

## 📜 Other Information
- **Author**: Your Name
- **License**: GPLv3
- **Repository**: [GitHub](https://github.com/mrjk/keyring-proxy)
