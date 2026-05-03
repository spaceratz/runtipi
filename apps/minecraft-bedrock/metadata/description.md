# Minecraft Bedrock Server

Self-hosted [Minecraft Bedrock Edition](https://www.minecraft.net/en-us/download/server/bedrock) dedicated server, packaged for runtipi using the excellent [itzg/docker-minecraft-bedrock-server](https://github.com/itzg/docker-minecraft-bedrock-server) image.

Bedrock is the cross-platform edition of Minecraft. This server lets the following clients all play together on the same world:

- **iPad / iPhone** (Minecraft Pocket Edition)
- **Android** phones and tablets
- **Nintendo Switch**
- **Xbox** (Series X/S, Xbox One)
- **PlayStation** (4 / 5)
- **Windows 10/11** (Minecraft for Windows / "Bedrock", *not* the Java launcher)

> **Java Edition will not work.** If anyone in the family has the Java launcher (the dirt block icon), they need "Minecraft for Windows" (the green grass cube icon) which they likely already own for free via the Minecraft Launcher.

## Connecting

The server listens on **UDP 19132** (IPv4) and **UDP 19133** (IPv6).

### From the same network (LAN / WiFi)

On most Bedrock clients the server appears automatically under **Friends → LAN Games** within ~30 seconds of joining the multiplayer screen. Just tap it.

If LAN discovery does not work (common on iPad over some WiFi setups, or across VLANs), add the server manually:

1. Open Minecraft → **Play** → **Servers** tab.
2. **Add Server**.
3. Server Name: anything (e.g. *Home Minecraft*).
4. Server Address: the **IP address of your runtipi host** (e.g. `172.29.53.x`).
5. Port: `19132`.
6. Save and tap to join.

### From outside the network

You will need to either:

- Forward **UDP 19132** on your router to the runtipi host (exposes the server to the public internet, not recommended without an allow list), or
- Have the kids connect via your VPN (e.g. WireGuard) so they appear on the local network.

## Setting up safely for kids

By default this app starts with the **allow list disabled** so you can confirm the server is reachable. Once everyone has joined for the first time, lock it down:

1. Have each kid connect once. The server logs will print their **gamertag** and **XUID** as they join. View logs from the runtipi app page or via `docker logs minecraft-bedrock`.
2. Edit the app config in runtipi:
   - Set **Allow List Users** to a comma-separated list like `kid1:1234567890,kid2:0987654321`.
   - Set **Operators** to your own gamertag (or XUID) so you can run admin commands.
   - Toggle **Enable Allow List** to ON.
3. Save and restart the app. Now only listed players can join.

The `ONLINE_MODE` setting is hardcoded to `true`, which means the server validates each player's Xbox Live identity before letting them connect. This stops random people from impersonating your kids' gamertags.

## Server console / admin commands

To open an interactive console and run commands like `op`, `gamerule`, `kick`, etc:

```bash
ssh tipi
docker attach minecraft-bedrock
```

Detach without stopping the server using **Ctrl-p, Ctrl-q**.

To send a single command without attaching:

```bash
docker exec minecraft-bedrock send-command "say Dinner is ready"
docker exec minecraft-bedrock send-command "gamerule keepInventory true"
```

## World data

All world data, configuration, and the downloaded Bedrock server binary live in `${APP_DATA_DIR}/data` on the host. The `worlds/` subfolder contains the actual world saves, named after the **Level Name** config field. Back this folder up if you care about your builds.

## Updating the Bedrock server version

The container's `VERSION` env var defaults to `LATEST`, which means the Bedrock server binary is re-checked on every container start and upgraded if Mojang has shipped a newer one. Just restart the app from runtipi to pick up new server versions.

The container *image* itself is pinned in the appstore manifest and updated via Renovate.
