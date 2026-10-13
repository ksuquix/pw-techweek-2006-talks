# pw-techweek-2006-talks

Four companion click-through presentations for Tech Week, built as self-contained HTML slide
decks (Claude Artifacts): three "in depth" talks plus an applied finale. All four share one visual
identity — same fonts, palette, and layout components — so they read as one series, and each
deck's wrap-up slide links to the other three.

## DNS in Depth: Simple Until It Isn't

[`dns-deck.html`](https://ksuquix.github.io/pw-techweek-2006-talks/dns-deck.html) — DNS fundamentals for a mixed beginner/experienced audience:
resolution, primary DNS servers, caching/TTL, A/CNAME/alias records, dig/nslookup, search paths,
third-level domains, private hosted zones (split-horizon DNS), `/etc/hosts`, `curl --resolve`,
Zscaler hijacking, and SPF/DKIM/DMARC. Each stop pairs a real example with an "ask the room"
audience-participation question.

## TLS in Depth: Trusted Until It Isn't

[`tls-deck.html`](https://ksuquix.github.io/pw-techweek-2006-talks/tls-deck.html) — the second talk. Crypto foundations, the handshake, PKI trust
chains, and x.509 fields as a short warm-up, then the real subject: hands-on debugging of
untrusted authority, cipher/protocol mismatch, hostname mismatch, expired/revoked certs, and
networking issues, plus OpenSSL, Node.js, and Go tooling. Same "ask the room" format throughout.

## Connectivity in Depth: It's Not Always DNS

[`tcp-deck.html`](https://ksuquix.github.io/pw-techweek-2006-talks/tcp-deck.html) — the third talk, closing the loop on the series. TCP
fundamentals and a network layout diagram (VPCs, subnets, ALB/NLB/NAT GW, peering, VPN), then
connectivity tooling — curl, netcat, Test-NetConnection, and openssl s_client — read through
their connection-error messages to pinpoint which layer actually failed. Covers Zscaler as an
explicit three-layer proxy (DNS, TLS, connection), the security layers beyond "is it
routable" (NACLs, security groups, host firewalls, Kubernetes NetworkPolicy, app policy),
and closes with a four-rung debugging ladder tying back to the DNS and TLS talks.

## Connectivity Errors: Reading the Tea Leaves

[`practice-deck.html`](https://ksuquix.github.io/pw-techweek-2006-talks/practice-deck.html) — the applied finale. No new theory: eleven real
cases, each pulled from one week of PurpleWave's Datadog logs (October 5–12, 2026), worked
through four questions (exact words, who said it, which rung, when and how wide) on the
DNS → port → TLS → app debugging ladder. Covers deleted-resource ENOTFOUNDs, mis-parsed
connection strings, a cluster DNS blip, refused vs. timed out, idle-connection resets, nginx's
two kinds of upstream timeout, a deploy-driven 502 spike, IPv6 "network unreachable," TLS noise
from health checks and scanners, and Cloudflare's 52x codes, plus a war-stories lightning
round. Each case slide carries the Datadog query to reproduce it.

## Running the decks

Each deck is a single self-contained HTML file — no build step. They're served by GitHub Pages
from the `main` branch:

- [DNS in Depth](https://ksuquix.github.io/pw-techweek-2006-talks/dns-deck.html)
- [TLS in Depth](https://ksuquix.github.io/pw-techweek-2006-talks/tls-deck.html)
- [Connectivity in Depth](https://ksuquix.github.io/pw-techweek-2006-talks/tcp-deck.html)
- [Connectivity Errors](https://ksuquix.github.io/pw-techweek-2006-talks/practice-deck.html)

Or clone the repo and open any of the `.html` files directly in a browser.

## Claude Artifact versions

Each deck is also published as a Claude Artifact, used as the editing/publishing target (these
links are PurpleWave-internal — share the github.io links above instead). See `CLAUDE.md`:

- [DNS in Depth](https://claude.ai/artifact/5mnp62Pn6WKzbkv2DdEWNV)
- [TLS in Depth](https://claude.ai/artifact/BucHK35eb4hM3FWeb6ojUL)
- [Connectivity in Depth](https://claude.ai/artifact/18mbKNSdGT9Ey6eJuimRqW)
- [Connectivity Errors](https://claude.ai/artifact/PwDTWaadB2URBKtvZizVgw)

## Presenter

Don Eisele — Platform Engineering, Operations Team.

## Editing

See `CLAUDE.md` for the full design system, slide-by-slide breakdown, content decisions, and
reconstruction notes for all four decks. To update a deck: edit the HTML file directly, then
republish via the Artifact tool to the same URL (pass the artifact's `url` from the "Claude
Artifact versions" section above), then commit and merge to `main` so GitHub Pages picks it up.

---

<p align="center">
  <img src="assets/qr-code.png" alt="QR code linking to the series" width="180"><br>
  <a href="https://ksuquix.github.io/pw-techweek-2006-talks/">ksuquix.github.io/pw-techweek-2006-talks</a>
</p>
