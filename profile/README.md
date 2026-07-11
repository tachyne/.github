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

## How it fits together

```
   Java 1.21.5–.8      Java 26.2       Bedrock (latest)
        │                  │                 │
   gw-java-770        gw-java-776       gw-bedrock          ← render per version
        └───────────┬──────┴─────────────────┘
                    │  typed domain events (attach protocol)
              tachyne-world                                  ← ONE versionless engine
```

A new Minecraft version is a new translation step in a gateway — the engine
never changes. The ingress routes each client to the right gateway by
protocol version; the access service holds one policy store (whitelist,
bans, roles, IP rules) enforced at the edge.

## What runs today

- 🌍 **A full survival game** — original terrain/cave/biome generation, real
  lighting, mining/crafting/smelting, 1.9-style combat, the complete vanilla
  mob roster with breeding/taming/riding, enchanting/brewing/anvils,
  villages with scheduled villagers and trading, weather, redstone tier 1,
  the Nether, and the End with the full dragon fight.
- 🏆 **The vanilla advancement system** — the complete advancement tree with
  vanilla reveal semantics (the tree unfolds as you progress; hidden
  advancements appear only once earned), toasts, chat announcements, and XP
  rewards, on every supported client version.
- 🧩 **Live world sharding** — one world split across pods with silent,
  no-loading-screen handover: cross-border visibility, mobs that chase you
  across the seam, momentum preserved.
- 🗺️ **Earth mode** — real-world terrain from open elevation data. The
  flagship world is **greater Cape Town at true 1:1 scale**, with a raised
  world ceiling so Table Mountain stands its full height.
- 🔀 **Multi-version for real** — one canonical composition plus a chained,
  data-driven translation layer; a 1.21.5 client and a 26.2 client stand in
  the same world and see the same things, each in their own wire dialect.
- ⚙️ **Kubernetes-native** — every component is a small Go binary in a
  container, built by CI on every push; vanilla-style flow control
  (chunk-batch pacing) keeps joins fast and flight smooth.

## Where it's headed

The goal is **full vanilla feature parity**, worked through systematically —
progression systems first (advancements ✅, statistics and the recipe book
next), then interactive blocks and UI (scoreboards, maps, signs, beacons),
world simulation depth (fluids, fire spread, redstone tier 2, data-driven
loot), structures (fortresses, end cities, monuments, raids), and item/combat
depth (fishing, the full enchantment set, crossbows/tridents).

Honest expectations before you commit an evening:
**[the feature matrix](https://github.com/tachyne/tachyne-world#what-to-expect-vanilla-parity-at-a-glance)**
in tachyne-world's README lists what's implemented, what's partial, and
what's missing.

## The repos

| Repo | Role |
|---|---|
| [tachyne-world](https://github.com/tachyne/tachyne-world) | the engine: simulation, worldgen, sharding, earth mode |
| [tachyne-common](https://github.com/tachyne/tachyne-common) | shared library: attach protocol, renderer, translation chain, gateway pipeline |
| [tachyne-gw-java-770](https://github.com/tachyne/tachyne-gw-java-770) · [-776](https://github.com/tachyne/tachyne-gw-java-776) · [-bedrock](https://github.com/tachyne/tachyne-gw-bedrock) | per-version client gateways |
| [tachyne-ingress](https://github.com/tachyne/tachyne-ingress) | the single public entrypoint: version routing + UDP forwarding |
| [tachyne-access](https://github.com/tachyne/tachyne-access) | authorization: whitelist, bans, roles, IP ACL |
| [tachyne](https://github.com/tachyne/tachyne) | quickstart: Docker Compose + Kubernetes examples |

Contributions welcome — each repo has a `CONTRIBUTING.md`. Licensed
Apache-2.0. Built by a human maintainer working with an AI coding agent;
every change is reviewed, tested and gated by CI.

> tachyne is an unofficial, independent project — not affiliated with,
> endorsed, or approved by Mojang Studios, Mojang Synergies AB, or Microsoft.
> "Minecraft" is a trademark of Mojang Synergies AB. This project contains no
> Minecraft game code.
