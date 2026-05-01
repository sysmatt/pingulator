# pingulator

A terminal-based continuous ping monitor. Runs `fping` in looping mode and displays live per-host status in a color-coded curses UI that auto-expands into columns as the host list grows.

## Requirements

- Python 3.8+
- `fping` installed and in `PATH`
  - Ubuntu/Debian: `apt install fping`
  - macOS: `brew install fping`
  - RHEL/Fedora: `dnf install fping`

No third-party Python packages are required.

## Usage

```
pingulator [options] [host ...]
```

Hosts can be supplied directly or via `--generate` / `--wild`. Press `q`, `Q`, or `ESC` to exit.

## Options

| Flag | Description |
|------|-------------|
| `-v`, `--verbose` | Increase logging verbosity |
| `-D`, `--debug` | Enable debug logging |
| `-B`, `--beeps` | Terminal beep when a host goes hard DOWN |
| `-L`, `--flash` | Terminal flash when a host goes hard DOWN |
| `-R`, `--noreverse` | Disable reverse-video on DOWN hosts |
| `-4`, `--ipv4` | Restrict fping to IPv4 |
| `-6`, `--ipv6` | Restrict fping to IPv6 |
| `-d`, `--rdns` | Resolve return-packet addresses via DNS |
| `-g MASK`, `--generate MASK` | Generate target list from CIDR/range (fping `-g`) |
| `-p MS`, `--period MS` | Interval between fping packets in ms (default: 100) |
| `-m N`, `--miss N` | Missed refreshes before amber warning (default: 2) |
| `-t MS`, `--timeout MS` | First-packet timeout in ms, cannot exceed `--period` (default: 100) |
| `-r SEC`, `--refresh SEC` | Screen refresh interval in seconds (default: 1) |
| `-H FILE`, `--hosts FILE` | Hosts file for `--wild` matching (default: `/etc/hosts`) |
| `-F N`, `--hostsfield N` | Zero-indexed field column in hosts file (default: 1) |
| `-W PATTERN`, `--wild PATTERN` | Derive targets from hosts file using fnmatch pattern (repeatable) |

Any remaining arguments are passed directly to `fping`.

## Display

| Color | Meaning |
|-------|---------|
| Green | Host responding normally |
| Amber | Host responding but missing refreshes (possible packet loss) |
| Red (+ reverse) | Host hard DOWN (ICMP unreachable or no response) |

The header line shows elapsed run time and a refresh counter that alternates colors as a live heartbeat indicator.

## Examples

Ping a handful of hosts:
```
pingulator 192.168.1.1 192.168.1.254 8.8.8.8
```

Ping an entire subnet:
```
pingulator -g 10.0.0.0/24
```

Pull targets from `/etc/hosts` matching a pattern:
```
pingulator -W 'web-*' -W 'db-*'
```

Faster polling with a lower miss threshold:
```
pingulator -p 50 -t 50 -m 1 192.168.1.0/24
```
