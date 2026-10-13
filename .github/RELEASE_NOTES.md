Unofficial build of the **transfer join method** ([PR #110](https://github.com/timvisee/lazymc/pull/110)),
for people who need it before upstream reviews the change.

Built from this fork's `transfer-method` branch: upstream `master` (8 commits
past `v0.2.11`, up to `204324f`) plus the single commit `d8c6739`. No other
changes, so everything else behaves exactly like upstream.

### Why

The `hold` method keeps a joining client in the login state without sending
anything, so the client times out after 30 seconds. Servers that start slower
than that always fall through to `kick`, and the player has to reconnect by
hand — the normal case for modded servers.

The `transfer` method keeps the client in the configuration state with keep
alive packets instead, so there is no timeout, and sends a transfer packet once
the server is up. The client reconnects on its own and does a full handshake
with the real server, so no registry or mod data has to be emulated — unlike
the `lobby` method, this works with Forge, NeoForge and modpacks.

### Requirements

- Minecraft client 1.20.5 or newer (protocol 766+). Older clients are passed on
  to the next join method, so keep `kick` as a fallback.
- `accepts-transfers=true` in the server's `server.properties`.

### Usage

```toml
[join]
methods = [
    "transfer",
    "kick",
]

[join.transfer]
#timeout = 600            # how long to hold the client while the server starts
#keep_alive_interval = 10 # seconds between keep alive packets
#address = "example.com:25565"  # what the client reconnects to; defaults to lazymc's public address
```

### Which file

| File | Use |
|---|---|
| `lazymc-<tag>-linux-x64` | most Linux servers |
| `lazymc-<tag>-linux-x64-static` | Linux without a matching glibc (musl, static) |
| `lazymc-<tag>-linux-aarch64` | ARM64 Linux |
| `lazymc-<tag>-linux-armv7` | ARMv7 Linux |
| `lazymc-<tag>-windows.exe` | Windows |

`chmod +x` the Linux binaries before running them.

### Caveats

This is a community build from a fork, not reviewed or endorsed by upstream.
It has been running a NeoForge 21.1.250 / Minecraft 1.21.1 pack (~390 mods,
about a minute to start) since early October 2026. GPL-3.0, same as upstream.
