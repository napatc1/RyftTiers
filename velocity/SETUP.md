# Velocity Proxy Setup for RyftPvP

## Overview
Velocity sits in front of two Paper servers:
- **Hub server** (port 25566) — your current RyftTiers server (spawn, tier testing, bot)
- **Practice server** (port 25567) — new server just for the practice/duel system

Players connect to port 25565 → Velocity → Hub or Practice

## Step 1: Download Velocity
1. Go to https://papermc.io/downloads/velocity
2. Download Velocity 3.3.x jar
3. Create a new folder: `velocity/` next to your server folder
4. Put the jar in it, copy `velocity.toml` from this repo into it

## Step 2: Create the forwarding secret
1. Generate a random string (or use the one in forwarding-secret.txt)
2. Create `velocity/forwarding.secret` with just that string on one line (no .txt extension)
3. Example: `RyftPvP_XkQ9mP2vLs` (make it random)

## Step 3: Configure Hub server (your current server)
In `server.properties`:
```
online-mode=false
server-port=25566
```

In `config/paper-global.yml`:
```yaml
proxies:
  velocity:
    enabled: true
    online-mode: true
    secret: "RyftPvP_XkQ9mP2vLs"  # same as forwarding.secret
```

## Step 4: Create Practice server
1. Create a new folder `ryftpractice-server/`
2. Copy a fresh Paper 26.3 jar into it
3. Run once to generate configs, then configure:

`server.properties`:
```
online-mode=false
server-port=25567
```

`config/paper-global.yml`:
```yaml
proxies:
  velocity:
    enabled: true
    online-mode: true
    secret: "RyftPvP_XkQ9mP2vLs"  # same secret
```

Install Skript + SkriptReflect into the practice server plugins, copy `ryft-practice.sk` there.

## Step 5: Hub commands (ryft-commands.sk additions)
Add `/practice` command to hub server to send players to practice:
```skript
command /practice:
    trigger:
        connect player to server "practice"
```

Add `/hub` command to practice server:
```skript
command /hub:
    trigger:
        connect player to server "hub"
```

## Step 6: Start order
1. Start Hub server (25566)
2. Start Practice server (25567)
3. Start Velocity (25565)
4. Players connect to 25565

## Notes
- Dawn can run multiple server processes — just open each server folder in Dawn or run them with `java -jar paper.jar`
- Keep Geyser on the Hub server (Bedrock players connect to hub)
- AuthMe stays on Hub only; practice server trusts Velocity's player info
- Velocity handles authentication — both servers run offline-mode=false
