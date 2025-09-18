# PSG - Process Search with Grep

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![GitHub](https://img.shields.io/github/stars/Open-Technology-Foundation/psg?style=social)](https://github.com/Open-Technology-Foundation/psg)

An enhanced process search utility that improves upon traditional `ps | grep` workflows with advanced filtering, sorting, and display options.

## Features

- **Smart Self-Filtering**: Automatically excludes the psg process itself from results using PID-based filtering
- **Exact Process Matching**: Use `-e` flag to match exact process names (e.g., find only "bash" not "/bin/bash")
- **Custom Field Display**: Select which process information fields to display
- **Flexible Sorting**: Sort results by any field (CPU, memory, PID, etc.)
- **Color Highlighting**: Automatic color highlighting of matched patterns
- **Regular Expression Support**: Full regex pattern matching capability
- **Case-Insensitive Search**: Optional case-insensitive searching
- **Multiple Search Terms**: Search for multiple processes in one command
- **Bash Completion**: Tab completion for options and process names

## Installation

### Quick Install

#### From GitHub
```bash
# Clone the repository
git clone https://github.com/Open-Technology-Foundation/psg.git
cd psg

# Copy to system path
sudo cp psg /usr/local/bin/psg
sudo chmod +x /usr/local/bin/psg

# Install bash completion (optional)
sudo cp .bash_completion /etc/bash_completion.d/psg
```

#### Direct Download
```bash
# Download directly
wget https://raw.githubusercontent.com/Open-Technology-Foundation/psg/main/psg
sudo mv psg /usr/local/bin/psg
sudo chmod +x /usr/local/bin/psg
```

### Manual Install
```bash
# Make executable
chmod +x psg

# Add to PATH or create alias
echo "alias psg='$(pwd)/psg'" >> ~/.bashrc
source ~/.bashrc
```

## Usage

```bash
psg [OPTIONS] keyword...
```

### Options

| Option | Long Form | Description |
|--------|-----------|-------------|
| `-f` | `--fields` | Specify which fields to display (comma-separated) |
| `-s` | `--sort` | Sort output by specified field |
| `-e` | `--exact` | Match exact process name only |
| `-r` | `--regex` | Treat keywords as regular expressions |
| `-c` | `--case` | Case-sensitive search (default: insensitive) |
| `-n` | `--no-color` | Disable colored output |
| `-q` | `--quiet` | Don't print header line |
| `-v` | `--verbose` | Print header line (default) |
| `-h` | `--help` | Show help message |
| `--` | | Pass remaining options to ps command |

### Available Fields

- **Process Info**: `pid`, `ppid`, `pgid`, `tty`
- **User Info**: `user`, `ruser`, `group`, `rgroup`
- **Performance**: `pcpu`, `pmem`, `nice`, `vsz`, `rss`
- **Time**: `etime`, `time`
- **Command**: `comm`, `args`
- **State**: `stat`

## Examples

### Basic Search
```bash
# Find all bash processes
psg bash

# Find all python processes
psg python

# Search for multiple processes
psg bash python node
```

### Exact Matching
```bash
# Find only processes named exactly "bash" (not /bin/bash)
psg -e bash

# Find exactly "nginx" master process
psg -e nginx
```

### Custom Field Display
```bash
# Show only PID, user, CPU%, and command
psg -f pid,user,pcpu,args firefox

# Minimal display with PID and command name only
psg -f pid,comm chrome

# Detailed memory information
psg -f pid,user,pmem,vsz,rss,args java
```

### Sorting Results
```bash
# Sort by CPU usage (highest first)
psg -s pcpu

# Sort by memory usage
psg -s pmem chrome

# Sort by elapsed time
psg -s etime
```

### Advanced Filtering
```bash
# Case-sensitive search
psg -c Docker

# Regular expression search
psg -r 'python[23]'
psg -r '^/usr/bin/.*sh$'

# Combine options
psg -f pid,pcpu,args -s pcpu -r 'node|npm'
```

### Process Management
```bash
# Find and kill a process
psg firefox  # Find PID
kill $(psg -q -f pid firefox)

# Monitor specific processes
watch -n 2 "psg -f pid,pcpu,pmem,args -s pcpu java"

# Count processes by name
psg -q postgres | wc -l
```

### System Administration
```bash
# Find all processes by a user
psg -f pid,user,pcpu,pmem,args . | grep username

# Find high CPU consumers
psg -s pcpu -f pid,user,pcpu,args | head -20

# Find processes on specific terminal
psg -f pid,tty,user,args . | grep pts/1

# Pass options to ps
psg -- -u root nginx
```

## Advanced Features

### PID-Based Filtering
PSG uses intelligent PID-based filtering to exclude itself from search results. This works reliably even when:
- Called via different paths (`./psg`, `/usr/local/bin/psg`)
- Invoked through shell scripts
- Running multiple instances simultaneously

### Exact Match Mode
The exact match mode (`-e`) uses the `comm` field from ps, which contains only the program name without path or arguments. This is perfect for finding specific daemons or services.

### Field Management
PSG dynamically manages ps output fields:
- Automatically adds required fields for internal processing
- Removes internal fields from final output
- Preserves original field order and formatting

## Troubleshooting

### No Results Found
- Check if the process name is spelled correctly
- Try without exact match flag (`-e`)
- Use case-insensitive search (default)

### Permission Denied
- Some process information requires root access
- Use `sudo psg` for system processes

### Performance Issues
- For large result sets, limit fields with `-f`
- Use specific search terms instead of broad patterns
- Pipe to `head` or `tail` for limiting output

### Completion Not Working
```bash
# Source the completion file
source /etc/bash_completion.d/psg
# Or
source ~/.bash_completion
```

## Comparison with Traditional Methods

| Task | Traditional | PSG |
|------|-------------|-----|
| Find firefox | `ps aux \| grep firefox \| grep -v grep` | `psg firefox` |
| Find by exact name | `ps aux \| grep '^nginx '` | `psg -e nginx` |
| Sort by CPU | `ps aux \| grep app \| sort -nrk 3` | `psg -s pcpu app` |
| Custom fields | `ps -eo pid,pcpu,args \| grep java` | `psg -f pid,pcpu,args java` |

## Technical Details

- **Language**: Pure Bash (5.2.21+)
- **Dependencies**: Standard Unix utilities (ps, grep, awk, sort, sed)
- **Compatibility**: Linux, macOS, Unix-like systems
- **Performance**: Optimized with single-pass filtering

## Contributing

PSG is actively maintained. For bugs, feature requests, or contributions:

- **Repository**: [https://github.com/Open-Technology-Foundation/psg](https://github.com/Open-Technology-Foundation/psg)
- **Issues**: [https://github.com/Open-Technology-Foundation/psg/issues](https://github.com/Open-Technology-Foundation/psg/issues)
- **Pull Requests**: [https://github.com/Open-Technology-Foundation/psg/pulls](https://github.com/Open-Technology-Foundation/psg/pulls)

### Development Guidelines
- Check existing issues before submitting new ones
- Test changes with various process types and edge cases
- Follow the Bash coding standards in BASH-CODING-STYLE.md
- Ensure compatibility with bash 5.2.21+
- Include examples in commit messages for new features

## License

PSG is open source software. See LICENSE file for details.

## Version History

- **2.0.0**: PID-based filtering, exact match mode, improved field handling
- **1.0.0**: Initial release with basic search, sort, and field selection

---

*PSG - Making process search powerful yet simple*