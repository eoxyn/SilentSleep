# SilentSleep 🛏️

An ultra-lightweight, high-performance packet-level sleep message blocker for modern Minecraft (1.20 - 1.21+) servers running on highly multi-threaded architectures like **Folia**, **Canvas**, and standard **Paper** / **Spigot**.

## Why SilentSleep?

In vanilla Minecraft, when a player enters a bed, an action bar message is broadcast to players in the world:
- `X/Y players sleeping` (e.g. `1/48 players sleeping`)
- `Sleeping through this night`

SilentSleep completely intercepts and discards these messages at the packet level before they are sent over the network, leaving sleep mechanics fully intact while keeping the action bar clean.

## Features

- **100% Asynchronous & Thread-Safe**: Filters packets directly on Netty network threads. Compatible with Folia's asynchronous region architecture and will never cause server crashes or lag.
- **Zero Garbage Collection Overhead**: Pre-checks packet structures and translation keys without allocating new strings or serializing components unless matching. Extremely performant for servers handling 10,000+ concurrent players.
- **Dependency-free**: Runs completely standalone using native Netty packet injection.
- **Ultra-lightweight**: The compiled JAR is under 7 KB and contains zero shading or bloated dependencies.

## Requirements

- **Minecraft**: 1.20.x up to 1.21.x+
- **Java**: 17 or higher
- **Dependency**: None (Fully Standalone)

## Installation

1. Download `SilentSleep-1.0.0.jar` and place it in your server's `plugins` folder.
2. Restart or start your server.

## How It Works

SilentSleep injects a custom `ChannelDuplexHandler` into each player's Netty connection pipeline when they join. 

When outbound packets are processed:
1. It checks the packet class to determine if it is a system chat or action bar packet.
2. It reflectively scans the fields of matching packets (utilizing a thread-safe `ConcurrentHashMap` field cache to ensure **zero garbage collection allocations**).
3. If it encounters the translation keys `sleep.players_sleeping` or `sleep.skipping_night` in any component or raw string field, the packet transmission is blocked.
4. When a player disconnects, the channel handler is automatically ejected to prevent memory leaks.

