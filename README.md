# satellite6

Satellite 6 knowledge base and workarounds.

Practical notes and copy/paste commands for Red Hat Satellite 6 operations—especially **connected** vs **disconnected** (ISS network sync) setups.

## Contents

| Document | Topic |
|----------|--------|
| [kb-iss-connected-disconnected-hammer.txt](kb-iss-connected-disconnected-hammer.txt) | Self-signed CA trust for remote `hammer`, list enabled repos, sync all repos (ISS pull). Run CLI on **connected**, target **disconnected** with `--server`. |

## Quick start (ISS hammer KB)

1. On **connected**: build the combined CA bundle so remote `hammer` trusts disconnected HTTPS.
2. From **connected**: `hammer --server https://<disconnected-fqdn> ...` to list and sync repos on disconnected.
3. Ensure connected has already synced those repos from the Red Hat CDN before disconnected pulls.

Placeholders in the KB: `sampleorg`, `YOUR_DISCONNECTED_ADMIN_PASSWORD`, and example FQDNs—replace with your values.

## Contributing

Add new workarounds as plain-text KB files in the repo root (or under a clear topic folder) and link them from this README.
