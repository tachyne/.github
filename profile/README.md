# tachyne

**A Minecraft-compatible server, written from scratch in pure Go — with a
versionless core.**

The engine speaks no Minecraft wire protocol at all. The game itself becomes a
**versionless database of simulation events**, and every version-specific
concern is pushed to the **edge**: per-version **gateway** processes terminate
real clients and render those events into whatever wire format the client
speaks. Cross-version support isn't a proxy bolted onto a Java server after the
fact — it's the shape of the system. One world, simultaneously playable from
Java 1.21.5–1.21.8, Java 26.2, and Bedrock, with no client mods.

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
- 🧩 **World sharding — the hard part, prototyped** — the silent cross-pod
  handover works: no loading screen, cross-border visibility, mobs that chase
  you across the seam, momentum preserved. It runs today as a hard-border MVP;
  the public world runs **single-pod**, with seamless multi-pod sharding the
  active roadmap rather than a shipped default.
- 🗺️ **Earth mode** — real-world terrain from open elevation data. The
  flagship world is **greater Cape Town at true 1:1 scale**, with a raised
  world ceiling so Table Mountain stands its full height.
- 🔀 **Multi-version for real** — one canonical composition plus a chained,
  data-driven translation layer; a 1.21.5 client and a 26.2 client stand in
  the same world and see the same things, each in their own wire dialect.
- ⚙️ **Kubernetes-native** — every component is a small Go binary in a
  container, built by CI on every push; vanilla-style flow control
  (chunk-batch pacing) keeps joins fast and flight smooth.

## How it compares

Conventional servers (vanilla, Paper, Fabric) and tachyne sit at different
points in the design space:

| | Conventional Java server | tachyne |
|---|---|---|
| **Process model** | One monolithic JVM process | Distributed pods: engine, per-version gateways, ingress, authz |
| **Multi-version** | Proxy/plugin layers bolted on (ViaVersion etc.) | Native: a versionless engine + per-version render gateways |
| **Concurrency** | One main game thread; plugins fight for it | Go goroutines per connection; simulation isolated per world pod |
| **Scaling** | Vertical only — a bigger machine | Horizontal: front tier replicates freely today; world-sharding to split one map across pods is prototyped (single-pod in production for now) |
| **Footprint** | JVM heap, GC tuning, warmup | Small static Go binaries, containers measured in tens of MB |
| **Gameplay** | Complete vanilla | Vanilla parity **in progress** — see the [feature matrix](https://github.com/tachyne/tachyne-world#what-to-expect-vanilla-parity-at-a-glance) |
| **Ecosystem** | Massive plugin ecosystem | Young but complete plumbing: a Bukkit-shaped [Go plugin API](https://github.com/tachyne/tachyne-world/blob/main/docs/PLUGINS.md), a language-agnostic event bus with hot-installable daemon plugins, a [plugin registry](https://github.com/tachyne/tachyne-registry) for discovery, and progressive fleet-wide plugin rollouts |

Unlike headless Go/Java frameworks (Minestom-style), tachyne is **not** a
build-your-own-gameplay kit: the engine ships real survival Minecraft —
terrain, mobs, combat, villages, the dragon fight — and is closing the gap to
full parity feature by feature. Pick a conventional server if you want the
complete game and its mature plugin ecosystem today; pick tachyne if you want a
multi-version, cloud-native world built to scale horizontally, and can live
with the parity matrix as it stands.

Numbers we'll stand behind: ~150 concurrent sessions measured on a single
node so far. The architecture is built to go well past that (sharding,
stateless front tier), but we publish what we've measured, not what we hope.

## Where it's headed

The goal is **full vanilla feature parity**, worked through systematically.
Shipped so far: progression systems ✅ (advancements, statistics, the recipe
book, scoreboards & teams), interactive blocks & UI ✅ (signs, maps, beacons),
world-simulation depth ✅ (fluids, fire spread, data-driven loot, redstone
tiers 1–2, the sculk family & the Warden), item/combat depth ✅ (crossbows,
tridents, the mace, fishing), and a growing set of generated structures ✅ —
villages, pillager outposts and ancient cities built from **real vanilla NBT
templates via jigsaw assembly**, plus shipwrecks, buried treasure, ocean
monuments, trial chambers and woodland mansions. The largest gaps left are the
full enchantment set and the long tail of block/mob behaviours — and, on the
infrastructure side, **seamless multi-pod world sharding** (the prototype
exists; making it the production default is the flagship goal).

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
| [tachyne-registry](https://github.com/tachyne/tachyne-registry) | plugin registry: discovery over git-hosted plugins |
| [tachyne-plugin-manager](https://github.com/tachyne/tachyne-plugin-manager) | pulls, builds, boots and supervises daemon plugins; the fleet control plane |
| [tachyne](https://github.com/tachyne/tachyne) | quickstart: Docker Compose + Kubernetes examples |

Contributions welcome — each repo has a `CONTRIBUTING.md`. Licensed
Apache-2.0. Built by a human maintainer working with an AI coding agent;
every change is reviewed, tested and gated by CI.

> tachyne is an unofficial, independent project — not affiliated with,
> endorsed, or approved by Mojang Studios, Mojang Synergies AB, or Microsoft.
> "Minecraft" is a trademark of Mojang Synergies AB. This project contains no
> Minecraft game code.
