# 🚀 Keyring Proxy

A simple tool to access secrets from various sources, quickly. Like its name says, it's a proxy that can cache answers in local keyring for faster/offline access.

It's perfect for fetching passwords in terminal or scripts and provides a simple command to display the password of a given secret name. It also integrates with any password managers that support CLI or API.


### Highlights

- **Backends agnostic**: Easily manage any secret backends with a simple ini configuration.
- **Dispatch rules**: You can rewrite secret name globally (like aliases) or per backend (linke rewrite).
- **Local keyring proxy**: Optionnaly uses the local system keyring as a cache layer to retrieve slow-to-fecth secrets. It's also easy to flush the local cache or to resync it with backends.
- **Query history**: Quickly find previously queried passwords. Never try to remember anymore your previous queries. It can be disabled for more privacy.
- **Portable Bash Script**: Single file script that works out of the box on most Unix-based systems. (Tested on linux so far)


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
keyring-proxy is a simple tool to access secrets from various sources, quickly.

usage: keyring-proxy [<OPTS>] <COMMAND>
       keyring-proxy help

commands:
  cache             COMMAND               Manage cache
  cache clear                             Clear keyring cache entries
  cache ls                                List keyring cache entries
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

status:
  config file:  /home/rcordier/.config/keyring-proxy/config.ini
  history file: /home/rcordier/.cache/keyring-proxy/hist.log (enabled)

info:
  author:       mrjk <mrjk.78 at gmail.com>
  version:      0.1.1-stable (2024-08-20)
  license:      GPLv3
  website:      https://github.com/mrjk/keyring-proxy
```

## ❗ Known Issues

- Only tested on linux
- Git ini file support and bash choice of script language make sometime difficult or impossible to correctly handle arguments with spaces for backends

## 💬 Support
Feedback is appreciated! Please submit a PR or open an issue on the GitHub repository.

## 📜 Other Information
- **Author**: Your Name
- **License**: GPLv3
- **Repository**: [GitHub](https://github.com/mrjk/keyring-proxy)
