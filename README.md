# Satellite 6.x workarounds

Copy/paste runbooks for Red Hat Satellite 6.19 — focused on **connected Satellite**, **disconnected Satellite**, and **Capsule**.

---

## Pick a path

| I need to… | Run where | Open |
|------------|-----------|------|
| Sync content **connected → disconnected** (ISS / remote Hammer) | Connected Satellite | [ISS push workaround](satellite-connected-push-to-satellite-disconnected-workaround) |
| Install **satellite-cli** on Capsule and sync Capsule content | Capsule (API = Satellite) | [Capsule sync workaround](satellite-capsule-push-to-satellite-workaround.txt) |

One rule: Hammer’s `--server` / `:host:` is always the **API owner** (Satellite), never “this box” unless this box *is* Satellite.

---

## Topology cheat sheet

```
CDN ──► connected-satellite ──► disconnected-satellite   (ISS)
                 │
                 └──► connected-capsule                  (Capsule content sync)
```

| Role | Example FQDN | What it does in these KBs |
|------|--------------|---------------------------|
| Connected Satellite | `connected-satellite.example.com` | CDN sync; Hammer API for Capsule; ISS source |
| Disconnected Satellite | `disconnected-satellite.example.com` | ISS target; remote Hammer `--server` |
| Capsule | `connected-capsule.example.com` | Runs `satellite-cli`; syncs *via* Satellite API |

---

## Capsule repos (6.19) — always disable first

On Capsule, wipe then enable Capsule required + utils (for `satellite-cli`):

```bash
subscription-manager repos --disable "*"
subscription-manager repos \
  --enable=rhel-9-for-x86_64-baseos-rpms \
  --enable=rhel-9-for-x86_64-appstream-rpms \
  --enable=satellite-capsule-6.19-for-rhel-9-x86_64-rpms \
  --enable=satellite-maintenance-6.19-for-rhel-9-x86_64-rpms \
  --enable=satellite-utils-6.19-for-rhel-9-x86_64-rpms
dnf install -y satellite-cli
```

Full Capsule install + sync steps: [satellite-capsule-push-to-satellite-workaround.txt](satellite-capsule-push-to-satellite-workaround.txt)

---

## Quick starts

### A — ISS (connected → disconnected)

1. On **connected**: combine CA trust so remote Hammer trusts disconnected HTTPS.
2. From **connected**: `hammer --server https://<disconnected-fqdn> …`
3. Connected must already have the same content from CDN.

Details: [satellite-connected-push-to-satellite-disconnected-workaround](satellite-connected-push-to-satellite-disconnected-workaround)

### B — Capsule content sync (from Capsule)

1. On **capsule**: `repos --disable "*"`, enable Capsule 6.19 + `satellite-utils-6.19`, install `satellite-cli`.
2. Point Hammer at **connected-satellite** (`--server` or `:host:`).
3. `hammer --server https://connected-satellite… capsule content synchronize --name connected-capsule… --async`

Details: [satellite-capsule-push-to-satellite-workaround.txt](satellite-capsule-push-to-satellite-workaround.txt)

---

## Placeholders

Replace in every KB before running:

| Placeholder | Meaning |
|-------------|---------|
| `sampleorg` / `YOUR_ORG` | Organization name |
| `YOUR_DISCONNECTED_ADMIN_PASSWORD` / `YOUR_PASSWORD` | Admin (or equivalent) password |
| `*.example.com` | Your lab/prod FQDNs |

---

## Documents

| File | Intent |
|------|--------|
| [satellite-connected-push-to-satellite-disconnected-workaround](satellite-connected-push-to-satellite-disconnected-workaround) | CA bundle + remote Hammer ISS list/sync |
| [satellite-capsule-push-to-satellite-workaround.txt](satellite-capsule-push-to-satellite-workaround.txt) | Capsule repos/`satellite-cli` + Capsule content sync via Satellite API |

---

## Contributing

- One workaround = one plain-text file in the repo root (or a clear topic folder).
- Lead with **audience → where to run → API target → copy/paste**.
- Link every new file from the **Pick a path** table above.
- Prefer real FQDN examples; mark secrets as placeholders — never commit passwords.
