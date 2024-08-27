# 🚀 Keyring Proxy

A simple tool to access secrets from various sources, quickly. Like its name says, it's a proxy that can cache answers in local keyring for faster/offline access.

It's perfect for fetching passwords in terminal or scripts and provides a simple command to display the password of a given secret name. It also integrates with any password managers that support CLI or API. Keyring-proxy does not replace your password manager, it proxy it and allow to merge various passwords stores.


### Highlights

- **Multi-source**: Easily manage any secret backends with a simple ini configuration. As long your favorite password manager(s) provide a decent CLI tool, you're good to go.
- **Dispatch rules**: You can rewrite secret name globally (like aliases) or per backend (linke rewrite).
- **Local keyring proxy**: Optionnaly uses the local system keyring as a cache layer to retrieve slow-to-fecth secrets. It's also easy to flush the local cache or to resync it with backends.
- **Query history**: Quickly find previously queried passwords. Never try to remember anymore your previous queries. It can be disabled for more privacy.
- **Portable Bash Script**: Single file script that works out of the box on most Unix-based systems. (Tested on linux so far)


## 📚 Table of Contents

- [🚀 Keyring Proxy](#-keyring-proxy)
    - [Highlights](#highlights)
  - [📚 Table of Contents](#-table-of-contents)
  - [💻 Installation](#-installation)
    - [Dependencies](#dependencies)
    - [Install via ASDF or Mise](#install-via-asdf-or-mise)
    - [Install via Curl](#install-via-curl)
    - [Install from Source](#install-from-source)
    - [Verify installation](#verify-installation)
  - [🚀 Quickstart](#-quickstart)
  - [💡 Guide](#-guide)
    - [Create a configuration](#create-a-configuration)
    - [Work with backends](#work-with-backends)
    - [Route secret](#route-secret)
    - [Remaps: Alias and Regex](#remaps-alias-and-regex)
    - [Work with cache](#work-with-cache)
    - [Work directly with keyring](#work-directly-with-keyring)
  - [⚙️ Configuration](#️-configuration)
    - [Configuration Path Lookup](#configuration-path-lookup)
    - [Environment Variables](#environment-variables)
    - [INI Configuration Format](#ini-configuration-format)
    - [Command-Line Help](#command-line-help)
    - [Internal algorithm](#internal-algorithm)
  - [❗ Known Issues](#-known-issues)
  - [Alternatives](#alternatives)
  - [💬 Support](#-support)
  - [📜 Other Information](#-other-information)


## 💻 Installation

Current stable version is: 0.3.0

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

In it's simplest form, it resumes to:
```bash
keyring-proxy get my_secret
```

Call it directly in your shell or from any script, you will get your password to stdout.


You can list "known secret names" (alias keys in config), as cheatsheet:
```bash
keyring-proxy ls
```

Or check history (enabled by default):
```bash
keyring-proxy hist
```

Keyring-proxy works with a config file usually located in `$XDG_CONFIG_HOME/keyring-proxy/config.ini`. This configuration is required and it belongs to the user to build its own.
Below a partial example of config. See next chapter to create your config.

```ini
[config]
backends = keyring_corp,keepass_corp,keepass_personal

[backend "keepass_personal"]
cmd-fetch = keepass-cli get personal %s
remap-alias = corp_user|Pro/Job/my_user@corp.io
remap-regex = corp_account:(.*)|Pro/Dep/Devops/Account_\1
```

## 💡 Guide

This little guide should help you to cover most topics of keyring-proxy.

### Create a configuration

Ensure configuration exists and edit configuration file:
```
mkdir -p $XDG_CONFIG_HOME/keyring-proxy/
nano $XDG_CONFIG_HOME/keyring-proxy/config.ini
```

Check it corresponds well to path reported in `KEYRING_PROXY__CONFIG_FILE` key:
```
keyring-proxy config
```

### Work with backends

A backend a password source of truth, and it can be any password store, if it provides a CLI access. It's one of the most important concepts in keyring-proxy. Backends are defined in the `config.ini` file, here is an example with keepass, bitwarden and a custom made script:

```ini file=config.ini
[config]
  # Define here in which order you want backends to be queried
  backends = bitwarden_job,keepass_perso,dump_script

# Job secrets with bitwarden-cli
[backend "bitwarden_job"]
  cmd-fetch = bw get %s

# Personal secrets with keepass-xc
[backend "keepass_perso"]
  cmd-fetch = keepassx-cli get john %s

# A custom backend
[backend "dump_script"]
  cmd-fetch = dumb-script.sh --get-pass-stdout %s
```

This configuration defines that for a given secret query, it must first ask the first backend `bitwarden_job`, if it does not find anything, it will ask `keepass_perso` and so on. The `fetch-cmd` command must return a non empty password on stdout in clear text.


  > Note: See configuration file section for exact syntax


Backend order may have it's importance, depending how you want your secrets to be queried. Some backends are also longuer to query than others, you may want to put the longest at last. Even if the keyring-proxy cache is enabled, first queries can sometimes take time to answer. Finally, if no backend answer, then keyring-proxy exits with non 0 exit code.


### Route secret

TODO

### Remaps: Alias and Regex

For a given secret name, you may need to rewrite its name to before querying the backend. Indeed, usually, each password backends have it own structure and secret names. Keyring-proxy allow you to define `remap-alias` and `remap-regex`. For example:

```ini file=config.ini
# Job secrets with bitwarden-cli
[backend "bitwarden_job"]
  cmd-fetch = bw get %s

  # Direct aliases, remap `corp_user` to `Corp/jdoes@corp.com`
  remap-alias = corp_user|Corp/jdoes@corp.com

  # More aliases
  remap-alias = corp_keepass_devops|Corp/Keepass-Devops
  remap-alias = corp_vault_user|Corp/Ansible User Vault
  remap-alias = corp:user:corp_user|Corp/Ansible User Vault

  # A simple regex rule (sed -E syntax), shortcut to access `Corp/Ansible User Pass`
  remap-regex = myldap_account.*|Corp/Ansible User Pass

[backend "keepass_perso"]
  cmd-fetch = keepassx-cli get john %s

  # Another alias in another backend
  remap-alias = home_lab|Home/lab_password

```

The pipe `|` char act as delimiter.

  > Note: See configuration file section for exact syntax


### Work with cache

By default or unless disabled (`KEYRING_PROXY__DISABLE_CACHE=true`), keyring-proxy will cache backend results into the local keyring. This is designed to accelerate password retrieval on slow backends, while avoiding unecessary external calls when querying many passwords at a time. This caching mechanism drastically improves execution time for fetching a secret, but it may pose issues on secrets that must not be cached, like temporary or timed secrets. 

Also, it will tend to fill your local keyring with `keyring-proxy: cache - ...` secrets. They can be safely deleted by hand, keyring-proxy will recreate them if needed.

If you are running in trouble with cache, you can always inspect the current items:
```
keyring-proxy cache ls
```

To flush all of them:
```
keyring-proxy cache clear
```

To completely disable caching mechanism, setup the `KEYRING_PROXY__DISABLE_CACHE=true` environment variable or use the `--no-cache` option.


### Work directly with keyring

Sometimes, you don't have any password backends, and you just want a store a simple secret in the local keyring. It is possible with the `keyring` sub-command:

```
$ keyring-proxy keyring --help
keyring-proxy: Manage keyring-proxy

usage: keyring-proxy keyring add NAME
       keyring-proxy keyring rm NAME
       keyring-proxy keyring st NAME
       keyring-proxy keyring help

commands:
  add               NAME [BACKEND]        Add secret to local keyring
  rm                NAME [BACKEND]        Remove secret to local keyring
  st                NAME [BACKEND]        Show secret status on local keyring

options:
  -h|--help|help|h                        Show help

```

TODO:
- Describe how are stored keys

## ⚙️ Configuration

### Configuration Path Lookup

The tool looks for the configuration paths, it work for main app directories (`config` and `cache`): 
- `$KEYRING_PROXY__CONFIG_DIR`
- `$XDG_CONFIG_HOME/keyring-proxy/`
- `.config/keyring-proxy/`

Thus configuration file `config.ini` (and `hist.log`):
- `$KEYRING_PROXY__CONFIG_FILE`
- `$KEYRING_PROXY__CONFIG_DIR/config.ini`


  > Note: There is no support of runtime parameters in `config.ini`, history and cache settings are managed via commad line options or environment variables.

### Environment Variables

All environment variables are inspectable with the `config` command:
```
$ keyring-proxy config
KEYRING_PROXY__CONFIG_DIR           ~/.config/keyring-proxy        
KEYRING_PROXY__CACHE_DIR            ~/.cache/keyring-proxy      
KEYRING_PROXY__CONFIG_FILE          ~/.config/keyring-proxy/config.ini        
KEYRING_PROXY__HIST_FILE            ~/.cache/keyring-proxy/hist.log      
KEYRING_PROXY__DISABLE_HIST         false          
KEYRING_PROXY__DISABLE_CACHE        false          
```

Specific app vars:

- `KEYRING_PROXY__CONFIG_FILE`: Keyring proxy configuration file.
- `KEYRING_PROXY__DISABLE_HIST`: Disable history (default: `false`).
- `KEYRING_PROXY__DISABLE_CACHE`: Disable local keyring cache (default: `false`).


### INI Configuration Format

The configuration is in INI format and allows defining multiple backends and secrets. Internally, this configuration is handled with `git-config`.

Top level configuration:

* `backends`:
  * desc: Names of the backends to query in order, comma separated
  * section: `[config]`
  * syntax: `backends = <BACKEND1>[,<BACKEND2>,...]`

Backend configuration:

* `cmd-fetch`:
  * desc: Command to execute to query backend. Use the `%s` to inject secret name in command.
  * section: `[backend NAME]`
  * syntax: `cmd-fetch = <CMD> %s`

* `remap-alias`:
  * desc: Alias a key to another name
  * section: `[backend NAME]`
  * syntax: `remap-alias = <SOURCE_SECRET>|<DEST_SECRET>`

* `remap-regex`:
  * desc: Remap a key with a regex
  * section: `[backend NAME]`
  * syntax: `remap-regex = <MATCH_REGEX>|<MATCH_REPLACE>`


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
  config                                  Show current configuration
  get               NAME                  Get password name
  hist                                    Show query history
  keyring           COMMAND               Manage keyring
  keyring add       NAME [BACKEND]        Add secret to local keyring
  keyring rm        NAME [BACKEND]        Remove secret to local keyring
  keyring st        NAME [BACKEND]        Show secret status on local keyring
  ls                                      List secret aliases and history
  usage                                   Show usage

options:
  -h|--help|h                             Show this help message
  -C|--no-cache                           Disable local keyring cache
  -v|-vv|-vvv|--verbose  [LEVEL]               Set verbosity level
  -V|--version                            Show version

status:
  config file:  ~/.config/keyring-proxy/config.ini
  history file: ~/.cache/keyring-proxy/hist.log (enabled)

info:
  author:       mrjk <mrjk.78 at gmail.com>
  version:      0.3.0-stable (2024-08-20)
  license:      GPLv3
  website:      https://github.com/mrjk/keyring-proxy

```

### Internal algorithm

No secrets are ever stored in clear text at any moment.

The lookup algorihtm is like:

* `SECRET_NAME` query:
  * Read config file `config.ini`
  * Get the order of backends to query
  * Query on backend1
    * Remap with `remap-regex`
    * Remap with `remap-alias`
    * Query with `NEW_SECRET_NAME`
    * Continue if no answer
  * Test on backend2
    * And so on ...
  * Return non-0 exit code if no secret found


## ❗ Known Issues

- Only tested on linux
- Git ini file support and bash choice of script language make sometime difficult or impossible to correctly handle arguments with spaces for backends

## Alternatives

See also different alternative and approaches:

- https://codeberg.org/caveman/nsapass
- https://github.com/sschmid/pw-terminal-password-manager


## 💬 Support
Feedback is appreciated! Please submit a PR or open an issue on the GitHub repository.

## 📜 Other Information
- **Author**: Your Name
- **License**: GPLv3
- **Repository**: [GitHub](https://github.com/mrjk/keyring-proxy)

