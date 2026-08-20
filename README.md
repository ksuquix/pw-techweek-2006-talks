# pw-techweek-2006-talks

Two companion click-through presentations for Tech Week, built as self-contained HTML slide
decks (Claude Artifacts). Both share one visual identity — same fonts, palette, and layout
components — so they read as one series.

## DNS in Depth: Simple Until It Isn't

`dns-deck.html` — DNS fundamentals for a mixed beginner/experienced audience: resolution,
primary DNS servers, caching/TTL, A/CNAME/alias records, dig/nslookup, search paths,
third-level domains, private hosted zones, `/etc/hosts`, `curl --resolve`, Zscaler
hijacking, and SPF/DKIM/DMARC. Each stop pairs a real example with an "ask the room"
audience-participation question.

Live: https://claude.ai/code/artifact/26a58358-73a7-40b3-b3ad-72d819ca457a

## TLS in Depth: Trusted Until It Isn't

`tls-deck.html` — the companion session. Crypto foundations, the handshake, PKI trust chains,
and x.509 fields as a short warm-up, then the real subject: hands-on debugging of untrusted
authority, cipher/protocol mismatch, hostname mismatch, expired/revoked certs, and networking
issues, plus OpenSSL, Node.js, and Go tooling. Same "ask the room" format throughout.

Live: https://claude.ai/code/artifact/5853ce33-0bd4-40c5-ba13-61b02ef6fb99

## Presenter

Don Eisele — Platform Engineering, Operations Team.

## Editing

See `CLAUDE.md` for the full design system, slide-by-slide breakdown, content decisions, and
reconstruction notes for both decks. To update a deck: edit the HTML file directly, then
republish via the Artifact tool to the same URL.
