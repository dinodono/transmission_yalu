# Transmission Yalu — Release 4.2.0-yalu

**Release date:** 2026-05-07  
**Base version:** Transmission 4.2.0 (dev)  
**Built on:** Linux x86_64 (Ubuntu 24.04)

---

## New Features

### Privacy Mode (`privacy_mode`)

Hides your IP address from other peers in the swarm.

**What it does:**
- Generates a fresh random **peer ID** on every tracker announce, preventing peers and trackers from fingerprinting or tracking this client across sessions.
- **Disables Peer Exchange (PEX)** so connected peers cannot share your IP address with the rest of the swarm.

**Setting key:** `privacy_mode` (boolean, default `false`)

> ⚠️ **Disclaimer:** This feature is provided for **research purposes only**. Using it to evade legal obligations, circumvent tracker rules, or engage in any malicious or fraudulent activity is strictly prohibited and remains the sole responsibility of the user.

---

### Super-Privacy Mode (`super_privacy_mode`)

A stronger form of Privacy Mode that additionally prevents the tracker from building a download history for this peer.

**What it does (includes everything Privacy Mode does, plus):**
- Reports **0 bytes downloaded**, **0 bytes uploaded**, and **0 bytes corrupt** to the tracker on every announce, so the tracker cannot accrue a transfer history associated with this peer.

**Setting key:** `super_privacy_mode` (boolean, default `false`)

> ⚠️ **Disclaimer:** This feature is provided for **research purposes only**. Using it to evade legal obligations, circumvent tracker rules, or engage in any malicious or fraudulent activity is strictly prohibited and remains the sole responsibility of the user.

---

## GUI Changes

### Qt Client
- **Peers tab → Options group:** Two new checkboxes:
  - *Enable Privacy Mode (research use only)*
  - *Enable Super-Privacy Mode (research use only)*
- Both checkboxes include descriptive tooltips and are persisted to `settings.json`.

### GTK3 Client
- **Peers section:** Two new `GtkCheckButton` widgets:
  - *Enable Privacy Mode (research use only)*
  - *Enable Super-Privacy Mode (research use only)*
- Wired via `init_check_button()` and persisted alongside all other session settings.

### GTK4 Client
- Same two checkboxes added in the peers section UI, identical behaviour to GTK3.

---

## Technical Details

| File | Change |
|------|--------|
| `libtransmission/session.h` | Added `privacy_mode` and `super_privacy_mode` bool settings; `allows_pex()` returns `false` when either mode is active; accessors `privacy_mode_enabled()` / `super_privacy_mode_enabled()` |
| `libtransmission/announcer.cc` | `create_announce_request()` uses a fresh `tr_peerIdInit()` in privacy mode; zeroes `up`/`down`/`corrupt` in super-privacy mode |
| `libtransmission/quark.h` | Added `TR_KEY_privacy_mode`, `TR_KEY_super_privacy_mode` enum entries in correct alphabetical position |
| `libtransmission/quark.cc` | Added `"privacy_mode"`, `"super_privacy_mode"` string entries in correct alphabetical position (required by static assertion) |
| `qt/Prefs.h` | Added `PRIVACY_MODE`, `SUPER_PRIVACY_MODE` pref IDs with key mappings |
| `qt/PrefsDialog.ui` | Two new `QCheckBox` widgets in the Peers tab Options group |
| `qt/PrefsDialog.cc` | Wired both checkboxes via `initWidget()` |
| `gtk/PrefsDialog.cc` | Wired both checkboxes via `init_check_button()` |
| `gtk/ui/gtk3/PrefsDialog.ui` | Two new `GtkCheckButton` widgets (rows 4 and 5) |
| `gtk/ui/gtk4/PrefsDialog.ui` | Two new `GtkCheckButton` widgets (rows 4 and 5) |

---

## Binaries Included

Built with CMake Release mode, Qt5, GTK3:

| Binary | Description |
|--------|-------------|
| `transmission-gtk` | GTK3 graphical client |
| `transmission-qt` | Qt5 graphical client |
| `transmission-daemon` | Headless daemon (JSON-RPC) |
| `transmission-remote` | Command-line remote control |
| `transmission-create` | Torrent creation utility |
| `transmission-edit` | Torrent editing utility |
| `transmission-show` | Torrent info utility |
| `transmission-cli` | Command-line download client |

---

## Bug Fixes (in this branch)

- Fixed `static_assert` failure: `privacy_mode` quark string was not in the correct alphabetical position in the predefined quark table (must come before `private`, after `priority_normal`).
