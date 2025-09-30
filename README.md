# Simple-venv

A simple and convenient tool for managing Python venv on Linux.

## Features

- Easy creation and management of Python virtual environments
- All environments stored in `~/simple-venv/`
- Simple command-line interface
- Environment copying and resetting capabilities

## Install

Download and install with a single command:

```bash
curl -o ~/s-venv.sh https://raw.githubusercontent.com/llaa33219/simple-venv/refs/heads/main/s-venv.sh && chmod +x ~/s-venv.sh
```

Or if you prefer `wget`:

```bash
wget -O ~/s-venv.sh https://raw.githubusercontent.com/llaa33219/simple-venv/refs/heads/main/s-venv.sh && chmod +x ~/s-venv.sh
```

## Usage

```bash
~/s-venv.sh [command] [arguments]
```

### Commands

| Command | Description |
|---------|-------------|
| `create <name>` | Create a new virtual environment |
| `enter <name>` | Activate a virtual environment |
| `list` | List all virtual environments |
| `copy <source> <target>` | Copy a virtual environment |
| `reset <name>` | Reset a virtual environment (removes all packages) |
| `remove <name>` | Delete a virtual environment |
| `help` | Show help message |

## Examples


**Create a new environment**
```
~/s-venv.sh create test1
```

**Activate the environment**
```
~/s-venv.sh enter test1
```

**List all environments**
```
~/s-venv.sh list
```

**Copy an environment**
```
~/s-venv.sh copy test1 test1-backup
```

**Reset an environment**
```
~/s-venv.sh reset test1
```

**Remove an environment**
```
~/s-venv.sh remove test1
```


## Requirements

- Python 3.3 or higher (with venv module)
- Linux/Unix-like operating system
- Bash shell

## License

MIT License
