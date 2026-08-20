# Presentation Series — "In Depth"

This project holds two companion click-through presentations built as Claude Artifacts, sharing
one visual identity (see "Design system" below — both decks use the identical CSS token set,
fonts, and component classes) so they read as one series. See each section below for the deck
specific to it.

---

# DNS Presentation — "DNS in Depth"

## What this is

A single-file HTML slide deck (click-through presentation) teaching DNS fundamentals to a
mixed beginner/experienced audience, built as a Claude Artifact. Each topic slide pairs a
real example (dig output, record table, config snippet) with an "ask the room" /
"ask the audience" reveal-on-click question, so the presenter pauses for audience guesses
before showing the answer.

- **Source file**: `/home/ksuquix/scratch/techweek/dns-deck.html` (copied here from the session
  scratchpad so it survives past the session; when editing, republish this path via the
  `Artifact` tool with the same `url` to update the live deck in place).
- **Published artifact URL**: https://claude.ai/code/artifact/26a58358-73a7-40b3-b3ad-72d819ca457a
- **Title**: "DNS in Depth: Simple Until It Isn't" (renamed from the earlier
  "DNS in Depth - How It Works, How to Debug Problems")
- **Favicon**: 🌐 (keep stable across redeploys)

To update: edit the HTML file directly, then republish via the `Artifact` tool with the same
`file_path` to redeploy to the same URL. Do not pass `title` if the file already has a
`<title>` tag — the tag always wins.

## Design system

- **Fonts**: Manrope (headings), IBM Plex Sans (body), IBM Plex Mono (code/data), loaded from
  Google Fonts.
- **Palette**: cool slate-green neutral base, not a default grey — light theme uses `#eef1ef`
  background; dark theme (auto via `prefers-color-scheme`, or `[data-theme="dark"]`) uses
  `#11151a`. Accent is a muted teal (`#1f7a6c` light / `#5fc9b3` dark) for structural elements;
  a warm amber/rust (`#b9631f` light / `#e0a15c` dark) is reserved specifically for the
  "ask the room" question boxes and reveal buttons, so questions are visually distinct from
  regular content. A muted red (`--danger`) marks warnings (Zscaler interception node, error
  callouts).
- **Layout**: one slide (`.slide`) visible at a time inside a card, fixed bottom navbar with
  prev/next buttons, dot indicators, and a counter. Arrow keys / space navigate. The title
  slide's topic chips are `<button>`s calling `goTo(i)` (a slide-index jump, distinct from
  `go(delta)` which steps relative) — if slides are reordered/added/removed, the hardcoded
  indices in those `onclick="goTo(N)"` calls must be updated to match. All theme tokens are
  CSS variables on `:root`, redefined for dark mode per the standard three-state pattern
  (system dark via media query, explicit `[data-theme]` override).
- **Recurring components**: `.path` (boxes-and-arrows resolution chain diagrams), `.term`
  (terminal/output blocks), `table.records` (DNS record tables), `.ask` (dashed amber box with
  a `.q-label`, `.q-text`, a `.reveal-btn` that toggles the next `.answer` block's `.shown`
  class), `.callout` (side-note strip), `.zone-diagram` (monospace hierarchy breakdown), `.egg`
  (dotted quiet box with an `.egg-label` "🥚 Easter egg" — passive extra info shown directly,
  no button, no audience discussion; used for asides that don't warrant stopping for
  discussion).

## Slide order (current, 15 total)

1. **Title** — topic list chips (now buttons via `goTo(i)`, jumping straight to the matching
   slide index — see `goTo()` in the script block), a presenter-bio callout (Don Eisele,
   Platform Engineering, Operations Team since Jan 2026, 30 years at K-State, the "basics are
   the fundamental key" framing tying in Aikido/Iaido) in place of the earlier
   format-explainer callout, which was removed since the user is presenting live and doesn't
   need it spelled out on-slide
2. **DNS is a lookup table** — resolution chain diagram; "ask the room" reveal comparing how
   Chrome / dig / nslookup / curl each report the same NXDOMAIN for a typo'd domain
3. **Primary DNS server** — resolver chain diagram (laptop → resolver → root → TLD →
   authoritative); ask: same-WiFi VPN-vs-not query to `www.purplewave.com` giving different
   answers; answer flags Zscaler as an especially bad offender here (teased, covered later)
4. **Caching** — cache-layer chain diagram; ask (moved here from the A-record slide): TTL math
   for when a changed IP becomes visible
5. **A record** — plain record table, no question (kept minimal on purpose)
6. **CNAME / Route53 alias / Cloudflare proxy** — chain diagram + two side panels (alias vs.
   proxied); ask: why a `dig` on a known company's domain returns a Cloudflare IP
7. **Verifying DNS truth without incomplete errors** (dig/nslookup) — basic dig/nslookup
   examples; ask-the-audience: annotated raw dig output for `foo.example.com` with
   `status: NXDOMAIN` next to a non-empty ANSWER section and a malformed CNAME target
   (`://target.com.`) — two things to spot at once
8. **Search path** — resolv.conf-style suffix example; ask: VPN-profile search-domain mismatch;
   **🥚 easter egg** (passive, no discussion): phone vs. computer giving different results on
   the same network — Android ignores the DHCP search suffix, iOS/macOS are inconsistent
   about it
9. **Reading a domain name / third-level domains** — right-to-left hierarchy breakdown; ask:
   good 3rd-level-domain use case at Purplewave — answer is the promotion path pattern
   (`servicename.qa.purplewave.com` / `.staging.` / `.prod.`)
10. **Private hosted zones** — public zone `cliquidator.info` vs. private zone
    `cliquidator.info` (split-horizon) with `www` / `gw-qa` records; ask: `dig` from off-VPN
    coffee shop → NXDOMAIN; **🥚 easter egg** (passive, no discussion): why different answers
    in different places inside AWS — covers multiple PHZs and how each is associated to
    specific VPCs
11. **/etc/hosts** (both OS paths shown) — ask: dig shows old IP but dev insists new IP is live
    → hosts file bypasses dig entirely
12. **curl --resolve** — one-off override example; ask: why this might still fail on a
    Purplewave dev workstation even though it works from staging/a k8s pod — answer lists
    /etc/hosts, Zscaler, Route53 PHZ/VPC mapping, and plain typos
13. **Zscaler hijacking** — interception diagram; one ask plus two easter eggs (the
    corporate-vs-personal-WiFi "which IP is real" question was cut):
    - ask: "everything about this app looks correct, internal DNS is set up, still getting
      NXDOMAIN on my workstation" → Zscaler proxies traffic on the back end, and if *it*
      can't reach the end resource, it won't resolve the name — looks exactly like a DNS
      problem when the real cause could be security groups, routing, or a firewall rule
    - two separate **🥚 easter eggs** (passive, no discussion), both "pseudo-hijacking,
      closer to home," folded in from what used to be a standalone slide:
    - **Kubernetes**: why `nats-auth-service` resolves as a bare name in a configMap →
      cluster DNS / search-suffix resolution, actual FQDN
      `nats-auth-service.production.svc.cluster.local`
    - **nginx**: why `http://elasticsearch` in an nginx config isn't DNS at all → an nginx
      `upstream` block resolved against local config before DNS is ever consulted
14. **SPF / DKIM / DMARC** — TXT record table + a single callout explaining that
    `p=reject` blocks a spoofed "billing@purplewave.com" email at a compliant receiver;
    the interactive question here was cut to save time, folded into a plain callout instead
15. **Wrap-up** — four-panel mental model summary (Resolution / Records / Trust /
    Interception) + a debugging checklist callout

## Content/framing decisions made during authoring (keep these if reconstructing)

- Every domain example originally used `purplewave.com`; the internal-zones slide was later
  switched to a real-feeling domain `cliquidator.info` for both the public and private zone
  (intentionally illustrating split-horizon DNS — same name, two zones), with records renamed
  to `www` and `gw-qa`.
- Questions are deliberately placed to build on each other: the primary-resolver slide plants
  the "different resolvers, different truth" idea and explicitly defers Zscaler ("we'll talk
  about it separately down the road") rather than explaining it early.
- The k8s/nginx "pseudo-hijacking" content was originally its own standalone slide (13b),
  then merged into the Zscaler slide as a passive "🥚 Easter egg" callout to save a slide and
  cut discussion time. The pairing is still intentional: Zscaler is hijacking imposed from
  *outside* the app by policy; the k8s/nginx aside shows the same "the name you typed isn't
  literally what got resolved" pattern happening *inside* your own platform for benign,
  structural reasons (cluster DNS search path, nginx upstream config) — it just no longer
  needs its own slide or audience discussion to make that point.
- The A-record slide was deliberately left as a plain record table with no question after the
  TTL question was moved to the caching slide — don't re-add a question there without reason.
- **Time budget**: user has a 55-minute slot. Original full-discussion estimate was
  ~75–90 minutes. Cuts made to close the gap, in order:
  1. The two "bonus ask" questions (search path, internal zones) were converted from
     interactive reveal-and-discuss questions into passive "🥚 Easter egg" callouts — read
     aloud, no button, no audience discussion. Saves ~8–10 min combined.
  2. The SPF/DKIM/DMARC question was cut entirely and folded into a one-line callout.
     Saves ~5 min.
  3. The standalone "Pseudo-hijacking — closer to home" slide was removed and its two Q&As
     folded into two separate "🥚 Easter egg" callouts on the Zscaler slide (one for
     Kubernetes, one for nginx) — same content, no separate slide, no discussion. Saves
     another ~7 min plus the per-slide transition overhead of one fewer slide.
  - After those three cuts, the Zscaler slide's original question (corporate vs. personal
    WiFi, "which IP is real") was swapped out for a new one ("everything looks correct but
    I'm getting NXDOMAIN — is it really DNS, or Zscaler failing to reach the resource") — a
    net-neutral swap on time, not an addition, since the old question was then cut outright.
  - **Revised estimate: ~55–60 minutes**, back in line with the original three-cut estimate.
    If still running long live, the SPF slide's example table could be trimmed to 2 rows, or
    the /etc/hosts and curl --resolve slides (both cover overriding resolution locally) could
    be presented back-to-back with one combined discussion instead of two.

## Reconstruction notes

If the scratchpad file is gone, this document plus the slide-order list above is sufficient to
regenerate the deck from scratch: recreate the 16 sections in the order listed, reusing the
component classes described under "Design system," and re-derive each slide's example content
and question/answer text from the bullet points above.

---

# TLS Presentation — "TLS in Depth: Trusted Until It Isn't"

## What this is

The companion deck to "DNS in Depth," same format: short conceptual setup, then hands-on
debugging as the main event, with "ask the room" reveal-on-click questions throughout.
Explicitly scoped by the user as: crypto foundations and the handshake should be brief/layman's
level (not a crypto course), PKI and x.509 fields are quick reference, and **debugging is the
meat of the course** — untrusted authority, cipher mismatch, hostname mismatch, expired/revoked,
plus OpenSSL/Node.js/Go tooling.

- **Source file**: `/home/ksuquix/scratch/techweek/tls-deck.html`
- **Published artifact URL**: https://claude.ai/code/artifact/5853ce33-0bd4-40c5-ba13-61b02ef6fb99
- **Title**: "TLS in Depth: Trusted Until It Isn't"
- **Favicon**: 🔒 (deliberately different from the DNS deck's 🌐, so the two are distinguishable
  in a tab bar/gallery, while sharing the same visual design system)

To update: edit the HTML file directly, then republish via the `Artifact` tool passing this
file's `url` (since a fresh session/conversation won't have published this path before).

## Design system

Identical token set, fonts, and component classes to the DNS deck — see "Design system" under
the DNS section above for the full palette/type/layout rationale. Reused verbatim: `.path`,
`.term`, `table.records`, `.ask`/`.answer`/`.reveal-btn`, `.callout`, `.egg`/`.egg-label`, the
title-slide `.title-list` button-based topic nav (`goTo(i)`), the fixed navbar with dots/counter,
and the three-state dark-mode token structure. One new component: `.chain-diagram` (monospace
hierarchy display, styled like the DNS deck's `.zone-diagram` but renamed since this deck
doesn't have "zones") — currently unused in the shipped content (the PKI slide ended up using
the `.path` chain-diagram instead) and can be removed or repurposed freely.

## Slide order (17 total)

0. **Title** — topic button nav (`goTo(i)`), frames the format, and a presenter-bio callout
   (same bio as the DNS deck's title slide, kept in sync between the two)
1. **Crypto foundations** — four-panel layman's explanation (symmetric, asymmetric, hashing,
   signatures); ask: why HTTPS doesn't feel slow despite using "slow" asymmetric crypto →
   asymmetric only bootstraps a session key, bulk data uses symmetric
2. **Handshake** — simplified TLS 1.2-style message sequence diagram (ClientHello → ServerHello
   + cert → validate → key exchange → Finished); ask: exact point the client decides trust →
   right after receiving the cert in ServerHello, before any encrypted app data
3. **PKI** — root → intermediate → leaf chain diagram; ask: root trusted, intermediate missing
   from what the server sent → still an untrusted-chain error; server, not client, is
   responsible for sending the full chain
4. **x.509 fields** — 8-row table: CN, SAN, Not Before/After, Fingerprint, Issuer, Key Usage /
   EKU / Constraints (merged — includes `CA:TRUE`/`CA:FALSE`), Subject/Authority Key ID (the
   chain-matching hashes), Public Key Algorithm (RSA vs. ECDSA, key size); ask: which field
   actually decides hostname match now → SAN, not CN (CN matching deprecated in Chrome 58).
   The CN and SAN row wording was deliberately trimmed ("now mostly vestigial" cut from CN,
   "actual" cut from SAN) so the table doesn't give away the answer before the reveal.
5. **Debugging overview** — states the **five**-bucket framing (untrusted authority,
   cipher/protocol mismatch, hostname mismatch, expired/revoked, networking issues) that
   structures every slide after this one
6. **Bucket 1: untrusted authority** — curl/browser error examples; ask: curl fails, Chrome on
   the same machine works → different trust stores (curl's system CA bundle vs. Chrome's own,
   often more current, managed store); two easter eggs: `curl -k`/`--insecure` for debug-only
   use (not scripts), and a reminder that different apps/runtimes may read their CA store from
   a different location than the OS trust store — track it down, don't assume
7. **Bucket 2: cipher/protocol mismatch** — ask was rewritten from the original Java/OpenSSL
   framing to a "spot the related pair" puzzle: `curl: (35) ... wrong version number` next to
   a plain `HTTP/1.1 400 Bad Request`, revealing that both came from swapped port/scheme
   (`curl https://host:80` and `curl -I http://host:443`) rather than a real negotiation failure
8. **Bucket 3: hostname mismatch** — ERR_CERT_COMMON_NAME_INVALID example, cert SAN vs. bare-IP
   connection; ask: chain trusted, dates fine, still a mismatch error → trust and identity are
   separate checks, connected name isn't in the SAN list; easter egg: wildcard certs aren't
   cryptographically weaker, just a larger blast radius if the shared key leaks (slower to
   rotate everywhere it's deployed)
9. **Bucket 4: expired/revoked** — side-by-side expired vs. revoked error examples; ask: dates
   look fine but browser says revoked → OCSP/CRL is a separate, active check from the
   Not-After date, used for early revocation (key compromise, mis-issuance); easter egg: a
   skewed system clock is a real, mundane cause of date errors, but you can't just fake the
   clock to trick a browser — it cross-checks time via Roughtime and the server's `Date` header
10. **Bucket 5: networking issues** (new bucket) — `curl: (35) ... Connection reset by peer`
    and SSL connection timeout examples; ask: a handshake resets/times out — what do you check
    before touching cert config at all? → security groups/firewall rules, LB idle-timeout,
    MTU/fragmentation, or a middlebox terminating the connection; if the client never got a
    ServerHello, the problem is below TLS entirely
11. **OpenSSL page 1** — `s_client -connect -servername` example output; ask: why `-servername`
    is needed alongside `-connect` → SNI, virtual hosting means the server needs to be told
    which cert to present; easter egg: Zscaler intercepts and re-signs the connection, so
    `s_client` run on a Zscaler-managed machine shows Zscaler's cert, not the real one — test
    from off that network path
12. **OpenSSL page 2** — reference commands only, no question: `x509 -noout -text/-dates/
    -fingerprint`, `openssl verify -CAfile`, forcing a protocol version with `-tls1_2`, plus a
    callout on piping `s_client` straight into `x509`; two easter eggs: you should never need
    to *ask* someone for a cert off a live server (fetch it yourself), and — a different story —
    the private key itself can't be fetched this way, so a freshly-regenerated (not renewed)
    key still has to be shipped to everywhere that needs it
13. **Verifying the entire chain** (new page) — `openssl s_client -showcerts -connect host:443
    -servername app.purplewave.com`; a callout on why order is critical (each cert signs the
    one before it: leaf → intermediate(s) → root, and the client's own CA store must already
    trust that root); easter egg: yes it's noisy, narrow it with the flags from the previous
    page depending on what you're actually chasing
14. **Node.js debugging** — table mapping Node TLS error codes to the buckets
    (UNABLE_TO_VERIFY_LEAF_SIGNATURE, DEPTH_ZERO_SELF_SIGNED_CERT, ERR_TLS_CERT_ALTNAME_INVALID,
    CERT_HAS_EXPIRED); ask: what `NODE_TLS_REJECT_UNAUTHORIZED=0` actually breaks → disables
    verification for the whole process, not just one request; correct scoped fix is
    `NODE_EXTRA_CA_CERTS`
15. **Go debugging** — `x509:` error string examples; ask: "certificate signed by unknown
    authority" only inside Docker, never on the host → minimal base images lack the
    `ca-certificates` package, so Go's `SystemCertPool()` comes back empty; fix by installing
    the package or supplying a custom `RootCAs` pool, never `InsecureSkipVerify: true`
16. **Wrap-up** — four-panel mental model (Crypto / Chain / Identity ≠ trust / **Five** buckets)
    + a callout recommending `openssl s_client -servername` as the first move on any live TLS
    error

## Content/framing decisions

- The user was explicit that crypto foundations and the handshake should stay short and
  non-mathematical — both slides are single-panel/diagram treatments, no formulas, and each
  carries exactly one audience question rather than several.
- Debugging is intentionally the largest section (slides 5–15, 11 of 17 slides) per the user's
  framing of it as "the meat of the course."
- A fifth debugging bucket — **networking issues** — was added after initial authoring; the
  user pointed out that not every TLS-looking failure is actually about the cert or the crypto
  at all (resets/timeouts before the handshake ever gets that far). This bucket was inserted
  between expired/revoked and the OpenSSL tooling slides, and the overview/wrap-up slides were
  updated from "four buckets" to "five" to match.
- Most debugging buckets follow the same shape: a realistic error string first (from a real
  tool — curl, a browser, Java, OpenSSL), then one question asking the audience to reconcile
  "why does this look like X but isn't." Bucket 2 (cipher/protocol mismatch) deliberately
  deviates from this — its question was reworked into a "spot the swapped port/scheme" puzzle
  rather than a legacy-client-vs-hardened-server scenario.
- The OpenSSL content spans three slides now, not two: page 1 (`s_client`, SNI question), page
  2 (reference commands, cheat-sheet, no question), and a dedicated "Verifying the entire
  chain" page (`-showcerts`, chain-order rules) added afterward — the user wanted chain
  ordering (leaf → intermediate → root, and why order can't be scrambled) called out on its
  own rather than folded into the page-2 reference list.
- Several easter eggs were added throughout debugging/tooling slides — same passive,
  no-discussion pattern established on the DNS deck (`.egg`/`.egg-label`, no reveal button):
  `curl -k` for debug only, CA-store location varying by app/runtime, wildcard cert blast
  radius, system clock vs. Roughtime/Date-header time checks, Zscaler re-signing OpenSSL
  output, not needing to *ask* for a live cert, and the private key needing to be shipped
  separately from the cert itself when freshly regenerated.
- Node.js and Go each get one slide with one audience question, both built around the same
  pattern established on the DNS deck's curl/Zscaler slide: a well-intentioned "fix" that's
  actually a much bigger hammer than it looks (`NODE_TLS_REJECT_UNAUTHORIZED=0` for Node,
  `InsecureSkipVerify` for Go) — reinforces a recurring theme across both decks about scoped
  fixes vs. global ones.
- No live timing discussion has happened yet for this deck. Rough shape: 17 slides, most carry
  one discussion question, several also carry one or two passive easter eggs — expect a
  runtime comparable to or somewhat longer than the DNS deck's pre-cut estimate (55–70+
  minutes), before any trims; no cuts have been requested or made yet for this deck.

## Reconstruction notes

If `tls-deck.html` is lost, this section plus the DNS deck's "Design system" section (shared
verbatim) is sufficient to rebuild it: recreate the 17 sections above in order, reusing the same
component classes, and re-derive each slide's examples and question/answer text from the
bullet points above.
