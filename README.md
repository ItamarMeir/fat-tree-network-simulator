# Fat-Tree Network Simulator

An interactive, browser-based simulator for fat-tree data-centre topologies.  
No dependencies — open `fat_tree_simulator.html` in any modern browser.

![Simulator demo](demo.gif)

---

## Features

- **Topology builder** — 2-tier (spine-leaf) and 3-tier fat-tree, configurable k-ary parameter (power of 2: 2, 4, 8, 16)
- **ECMP path selection** — deterministic enumeration of all (k/2)² cross-pod paths; configurable path count with balanced stride selection
- **Traffic presets** — Tornado (half-tree shift permutation) and Permutation patterns; fully custom connections
- **Real-time animation** — packet dots travel along chosen ECMP paths at configurable link rate
- **Queue & drop modelling** — fluid rate-based model with BDP-scaled queue depth; each cable direction has its own queue and drop counter, so bidirectional traffic is not double-counted
- **Live heatmap** — links sorted by overflow, queue fill and utilisation (top 200 shown); shows util%, queue fill%, and drop count with overflow highlighting
- **Zoom & pan** — mouse wheel zooms at the cursor, drag pans; keyboard: `+` / `-` zoom, arrow keys pan, `0` resets
- **Resizable UI** — draggable sidebar width and vertically resizable connection list / heatmap panels
- **Export** — snapshot current state (topology, all connections with full path details, per-link stats) to a `.txt` file for offline analysis

## Usage

1. Set **Tiers** and **k** then click **Build topology**
2. Configure **Link rate**, **BDP RTT**, and **Queue depth**
3. Add connections manually or apply a **Permutation / Tornado** preset
4. Set **ECMP paths** (use `(k/2)²` for perfectly balanced load) and **Default rate**
5. Click **Start stream** and observe utilisation and queue build-up in the heatmap
6. Drag the sidebar edge or panel bottom bars to resize the layout
7. Click **Export sim data** for a full diagnostic snapshot

## Traffic patterns

| Pattern | Description |
|---|---|
| **Permutation** | Hosts are randomly paired and each pair sends in both directions, so every paired host is a sender and a receiver |
| **Tornado** | Half-tree shift — host *i* ↔ host *i + n/2*; worst-case for ECMP load balancing; all traffic traverses the full tree |

## Queue model

- Ingress rate is computed from connection rates (fluid model, no packet-counting noise)
- Per-direction tracking: each direction has its own queue and drop counter
- Reported per link: `util = max(forward rate, backward rate) / link rate`, queue fill = worse direction, drops = sum of both directions
- A direction's queue fills when its rate exceeds link rate; drains when it drops below
- **0 BDP** = tail-drop with no buffer (immediate drops on overload)
