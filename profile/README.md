# tachyne

**A Minecraft-compatible server, written from scratch in pure Go — with a
versionless core.**

The engine speaks no Minecraft wire protocol at all. It simulates the world
and emits typed **domain events**; per-version **gateway** processes terminate
real clients and render those events into whatever wire format the client
speaks. One world, simultaneously playable from Java 1.21.5–1.21.8, Java 26.2,
and Bedrock — no client mods, no proxies bolted on after the fact.

**Get playing in one command:** [tachyne/tachyne](https://github.com/tachyne/tachyne)
has a Docker Compose stack and Kubernetes manifests — classic infinite
survival by default, real-Cape-Town earth mode as a variant.

What runs today:

- 🌍 **A full survival game** — terrain, caves, biomes, combat, the full mob
  roster, redstone, Nether + End, villages, enchanting, brewing, weather.
- 🧩 **Live world sharding** — one world split across pods with silent,
  no-loading-screen handover: cross-border visibility, mobs that chase you
  across the seam, momentum preserved.
- 🗺️ **Earth mode** — real-world terrain from open elevation data. The
  flagship world is **greater Cape Town at 1:1 scale**: spawn in the city
  bowl and look up at Table Mountain.
- ⚙️ **Kubernetes-native** — every component is a small Go binary in a
  container, built by CI on every push.

| Repo | Role |
|---|---|
| [tachyne-world](https://github.com/tachyne/tachyne-world) | the engine: simulation, worldgen, sharding, earth mode |
| [tachyne-common](https://github.com/tachyne/tachyne-common) | shared library: attach protocol, renderer, translation chain, gateway pipeline |
| [tachyne-gw-java-770](https://github.com/tachyne/tachyne-gw-java-770) · [-776](https://github.com/tachyne/tachyne-gw-java-776) · [-bedrock](https://github.com/tachyne/tachyne-gw-bedrock) | per-version client gateways |
| [tachyne-ingress](https://github.com/tachyne/tachyne-ingress) | the single public entrypoint: version routing + UDP forwarding |
| [tachyne-access](https://github.com/tachyne/tachyne-access) | authorization: whitelist, bans, roles, IP ACL |

Contributions welcome — each repo has a `CONTRIBUTING.md`. Licensed
Apache-2.0. Built by a human maintainer working with an AI coding agent;
every change is reviewed, tested and gated by CI.

> tachyne is an unofficial, independent project — not affiliated with,
> endorsed, or approved by Mojang Studios, Mojang Synergies AB, or Microsoft.
> "Minecraft" is a trademark of Mojang Synergies AB. This project contains no
> Minecraft game code.
