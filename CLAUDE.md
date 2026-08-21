# Presentation Series — "In Depth"

This project holds three companion click-through presentations built as Claude Artifacts,
sharing one visual identity (see "Design system" below — all three decks use the identical CSS
token set, fonts, and component classes) so they read as one series: **DNS in Depth** →
**TLS in Depth** → **Connectivity in Depth**. Every inline reference one deck makes to another
is a real hyperlink to that deck's artifact URL, and every deck's wrap-up slide lists the other
two under a "Check out the others in this series" callout — keep both of those in sync whenever
a deck's title, URL, or wrap-up slide changes. See each section below for the deck specific to it.

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
    `cliquidator.info` (split-horizon) with `www` / `gw-qa` records; intro now names the pattern
    explicitly as **split-horizon DNS**; ask was revised from the original "coffee shop
    NXDOMAIN" question to "why would we want the answers to be different inside and outside
    the company?" → performance (don't route local traffic out to a far region and back),
    security (don't put internal traffic on the public internet), security (ability to inspect
    internal traffic); **🥚 easter egg** (passive, no discussion): why different answers in
    different places inside AWS — covers multiple PHZs and how each is associated to specific
    VPCs
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
    Interception) + a debugging checklist callout + a "Check out the others in this series"
    callout linking to the TLS and Connectivity decks

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

If the source file is gone, this document plus the slide-order list above is sufficient to
regenerate the deck from scratch: recreate the 15 sections in the order listed, reusing the
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
3. **PKI** — root → intermediate → leaf chain diagram; the ask-the-room question was removed
   and its content folded directly into a panel below the callout — the slide now states
   outright (no discussion beat) that a missing intermediate produces an untrusted-chain error
   even with a trusted root, and that the server, not the client, is responsible for sending
   the full chain. This was done deliberately so the slide reads as a quick speed bump rather
   than a time-spender, and so "Verifying the entire chain" (slide 13) could become a terse
   reminder of the same leaf → intermediate → root ordering rather than re-teaching it
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
   Not-After date, used for early revocation (key compromise, mis-issuance); two easter eggs:
   a skewed system clock is a real, mundane cause of date errors, but you can't just fake the
   clock to trick a browser — it cross-checks time via Roughtime and the server's `Date`
   header; and an intermediate CA can be cross-signed by two different root CAs — either root
   verifies the same intermediate, but if chain-building resolves the path through the expired
   root, the whole cert gets classified as expired even though a valid path through the other
   root exists
10. **Bucket 5: networking issues** (new bucket) — `curl: (35) ... Connection reset by peer`
    and SSL connection timeout examples. The ask-the-room question here was converted to a
    plain callout (same content: check security groups/firewall rules, LB idle-timeout,
    MTU/fragmentation, or a middlebox before touching cert config — if the client never got a
    ServerHello, the problem is below TLS entirely) plus a passive **🥚 easter egg** pointing
    to the separate, dedicated Connectivity talk for this whole subject — done to shave time
    off this deck's runtime once it ran long in rehearsal
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
    error + a "Check out the others in this series" callout linking to the DNS and
    Connectivity decks

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
- **Time budget**: same 55-minute target as the DNS deck. Initial estimate after full authoring
  was ~80–90 minutes given the deck's density (17 slides, several with two easter eggs). Two
  cuts have been made: the PKI slide's ask-the-room was removed (content folded into a plain
  panel — see slide 3 above), and the Bucket 5 (networking issues) ask was converted to a
  callout + a passive easter egg pointing at the separate Connectivity talk instead of
  re-explaining that whole subject here. **Revised estimate: ~75–80 minutes** — still longer
  than the DNS deck's tuned 55–60 minutes; no live rehearsal timing has been reported back yet,
  so further cuts may follow once the user runs through it live. Reasonable next candidates if
  more time needs to come out: drop one of the two easter eggs on the untrusted-authority or
  expired/revoked buckets (both now carry two), or fold the "Verifying the entire chain" page
  back into the OpenSSL page 2 reference slide.

## Reconstruction notes

If `tls-deck.html` is lost, this section plus the DNS deck's "Design system" section (shared
verbatim) is sufficient to rebuild it: recreate the 17 sections above in order, reusing the same
component classes, and re-derive each slide's examples and question/answer text from the
bullet points above.

---

# TCP Presentation — "Connectivity in Depth: It's Not Always DNS"

## What this is

The third talk in the series, closing the loop that the DNS and TLS decks both leave open:
"the name resolved, the TLS handshake worked, but the connection still doesn't work." Covers
plain TCP fundamentals, connectivity tooling (curl, netcat, Test-NetConnection, openssl
s_client), a network layout diagram, Zscaler framed explicitly as a proxy operating at three
layers, and the security layers beyond routability (NACLs, security groups, host firewalls,
k8s NetworkPolicy, app policy). Same "ask the room" format as the other two decks, though this
deck leans more on dense reference tables than discussion questions — several slides that
would carry a question on the other two decks instead carry an error-message table here.

- **Source file**: `/home/ksuquix/scratch/techweek/tcp-deck.html`
- **Published artifact URL**: https://claude.ai/code/artifact/0115afde-05aa-46f2-84e3-f3d800c0f90d
- **Title**: "Connectivity in Depth: It's Not Always DNS"
- **Favicon**: 🔌 (third distinct favicon in the series, alongside 🌐 for DNS and 🔒 for TLS)

To update: edit the HTML file directly, then republish via the `Artifact` tool passing this
file's `url`.

## Design system

Identical token set, fonts, and component classes to the DNS/TLS decks — see "Design system"
under the DNS section above. Reused verbatim: `.path`, `.term`, `table.records`, `.ask`/
`.answer`/`.reveal-btn`, `.callout`, `.egg`/`.egg-label`, the title-slide `.title-list`
button-based topic nav (`goTo(i)`), the fixed navbar with dots/counter, and the three-state
dark-mode token structure. New components specific to this deck's network-layout diagram
(slide 2): `.topo`/`.topo-node`/`.topo-arrow` (an earlier CSS-box attempt at the diagram, now
unused — the shipped diagram is hand-authored inline SVG instead, per the
`artifact-diagramming` skill's guidance that a real mechanism deserves real connecting lines,
not unicode arrows in flex boxes); the SVG itself uses a `<marker id="tcpArrow">` arrowhead
reused throughout, `currentColor`/`var(--accent)`/`var(--danger)` for stroke/fill so it
reads correctly in both themes, and `stroke-dasharray` to distinguish routing boundaries
(dashed) from security-group/NACL-bounded "local traffic" areas (dotted, grey fill) — there is
a small legend for this in the diagram's upper-right corner.

## Slide order (11 total)

0. **Title** — topic button nav (`goTo(i)`), frames the whole series ("DNS resolved. TLS setup
   seems right. The connection still doesn't work..."), same presenter-bio callout as the other
   two decks
1. **TCP in one slide** — SYN/SYN-ACK/ACK handshake diagram; two callouts (the handshake is the
   whole "connection," DNS/TLS both sit on top of it; a RST tells whoever receives it the other
   side is done, tear down/hang up); ask: "Common question: DNS resolves, ping succeeds — how
   many of you do this check?" → ping is ICMP, not TCP; a firewall/SG/NACL can allow one and
   block the other, or nothing may even be listening on the port
2. **The layout: a map before troubleshooting** — a hand-authored inline SVG network diagram
   (not CSS boxes): Internet ↔ IGW (inside VPC A) ↔ a wide public subnet holding an ALB
   (labeled "proxying," has an SG tag), an NLB (labeled "forwarding," no SG — NLBs don't
   support them in real AWS), and a NAT Gateway (which arrows directly to Internet); the ALB
   and NLB both arrow down into an instance in the private subnet below, which arrows back up
   into the NAT Gateway for outbound; VPC A peers with VPC B; a VPN client outside a dotted
   "AWS" boundary tunnels in. A legend in the upper-right corner explains the dashed
   routing-boundary lines and the dotted grey local-traffic subnet fills. No ask-the-room on
   this slide — it's meant to be referenced, not discussed.
3. **Reference: the terms from that diagram** — a glance-only table, one term per line: Local,
   Routing, Forwarding, Proxying, VPC, Subnet, Route table, IGW/NAT GW, Peering, VPN, SG/NACL;
   plus a warning callout that "route" is also an application-layer term (HTTP method/path →
   handler mapping, API gateway path proxying) — same word, different layer entirely
4. **curl, netcat, Test-NetConnection, openssl s_client** — four panels, each restructured to
   lead with the command line first and a single one-line description after (no tool-name
   header — the command itself carries that); ask: the curl-errors slide's refused-vs-timed-out
   question, deliberately duplicated here as well as on slide 5
5. **Reading curl's connection errors** — the densest table in the deck: Could not resolve host
   (DNS problem, linked to the DNS talk), Failed to connect / Couldn't connect to server
   (curl's generic unhelpful wrapper — use `nc -zv` to get the real reason), Connection reset
   by peer (app crashed, a next-gen firewall didn't like the traffic, or one side lost internet
   outright), Connection timed out (hangs, doesn't error instantly), No route to host, Empty
   reply from server, SSL certificate problem (past TCP entirely — if `-k` fixes it, it's a
   cert/trust issue, linked to the TLS talk), HTTP 500 (app-layer — the app is down/broken or
   an LB can't route to it), SSL routines::wrong version number (hit an HTTP port with HTTPS),
   400 "The plain HTTP request..." (the mirror image — hit an HTTPS port with plain HTTP). No
   ask-the-room here — it was removed once the tools-overview slide absorbed the same question.
6. **Reading netcat's connection errors** — mirrors curl's table shape: succeeded, refused (the
   "box is up, nobody's answering the door" framing — moved here from curl's own row, since
   curl's version was removed to avoid duplicating it), timed out, no route to host, network
   unreachable, and "Name or service not known" (DNS problem, linked); ask: `nc -zv` hangs for
   30+ seconds instead of failing instantly — a hang means silently dropped (SG/firewall deny
   with no response), instant refusal means something actually answered
7. **Reading Test-NetConnection's connection messages** — same table shape again: "No such host
   is known" (DNS, linked), `TcpTestSucceeded : True`, `TcpTestSucceeded : False` with a real
   `RemoteAddress` (DNS worked, something's blocking the port — PowerShell won't say what,
   unlike curl/nc), `PingSucceeded : False` (ICMP-specific, doesn't mean the TCP port is
   unreachable); framed explicitly as "the move when you're desperate for info on a Windows box
   and can't install netcat"; callout on `-InformationLevel Detailed` for the route, "if the
   network supports it." No ask-the-room — reference table only.
8. **Zscaler: it's essentially a proxy** — reframed (previously "connected doesn't mean
   connected") around three specific layers it intercepts: DNS (points to its own IPs, same
   mechanism as the DNS talk's hijacking slide, linked), TLS (opens its own connection to the
   real backend, hands you a connection signed with its own internal cert), Connection (builds
   a proxied TCP connection to the backend on your behalf); keeps the original diagram and ask
   (`nc -zv` succeeds instantly on a Zscaler machine, but the app still times out — you
   connected to Zscaler's proxy, not the real backend)
9. **Beyond "is it routable": specific security layers** — six panels (Zscaler, NACLs,
   Security Groups, host firewall, Kubernetes NetworkPolicy, app policy), replacing what was
   originally just "security groups vs. NACLs"; ask: "how do you figure out which one it is?"
   → a five-bullet isolation strategy (local vs. remote split test; try from a different
   vantage point/pod if you can spin one up; try different ports; let the specific connection
   error point you toward the right layer; and a fifth bullet noting Claude itself is
   genuinely good at poking around and finding where things stop matching up, given access)
10. **The debugging ladder** (wrap-up) — four rungs: does the name resolve (`dig`/`nslookup`,
    linked to DNS in Depth), does the port accept a connection (`nc -zv`/`Test-NetConnection`),
    does the TLS handshake succeed (`openssl s_client`, linked to TLS in Depth), does the app
    actually respond (`curl` with the real request); callout that you can start anywhere on the
    ladder but let the errors/messages guide the next move; + a "Check out the others in this
    series" callout linking to the DNS and TLS decks

## Content/framing decisions

- Originally included two more slides between Test-NetConnection and Zscaler — "VPC routing &
  peering" and "VPN: what actually gets routed to you," each with its own ask. Both were cut
  outright (not merged, not converted to eggs) once the user decided the network-layout
  diagram on slide 2 and the reference table on slide 3 already covered that ground
  sufficiently for this deck's purposes; a dedicated "is this local, or is this routing?"
  framework slide was cut for the same reason. None of that content survives elsewhere in this
  deck — if it's ever wanted back, it would need to be re-authored, not recovered from a
  leftover egg.
- The network-layout diagram (slide 2) went through several iterations before landing on inline
  SVG: first a two-panel set of disconnected `.path` rows, then a single connected CSS-box
  "topology" (`.topo`/`.vpcbox`/`.subnetbox`), before the user pointed out Artifacts support
  real SVG lines and arrows, not just flexbox — at which point it was rebuilt as hand-authored
  SVG. Within that SVG version it went through further passes: the IGW moved from sitting
  outside the AWS boundary to living inside VPC A (IGWs are VPC-attached resources); the public
  subnet was widened and the ALB/NLB/NAT GW went from stacked to side-by-side; a standalone
  "Proxy (e.g. Zscaler)" box was replaced with a real ALB (proxying) once the user pointed out
  Zscaler isn't the only or best example of proxying available; an NLB (forwarding) was added
  alongside it once the user clarified they wanted both, not just the ALB; and a legend plus
  dotted "local traffic" subnet borders were added last. If touching this diagram again, treat
  the SVG coordinates as load-bearing — arrows are routed through specific elbow points to
  avoid crossing box borders at the wrong angle, and moving one box (e.g. NAT GW) requires
  re-routing whichever arrows terminate on it.
- curl/netcat/Test-NetConnection's error tables were deliberately built to mirror each other
  row-for-row where the underlying cause is identical (DNS failure, refused, timed out) so the
  audience sees the same failure surfaced in three tools' different wording — this was an
  explicit "make the netcat/Test-NetConnection page just like curl" request, not an incidental
  similarity.
- The four-tool overview slide (slide 4) was reworked twice: first to add a sample command line
  per tool, then to restructure each panel as command-first / one-line-description-second with
  no tool-name header, after the two-column grid layout caused command lines to overflow their
  boxes — the fix was switching to a single-column vertical stack, not shrinking the text.
- Several DNS/TLS talk references started as plain text ("see the DNS talk") and were converted
  to real anchor-tag links to the other decks' artifact URLs, matching the same treatment
  applied to the DNS and TLS decks' own cross-references and wrap-up "others in this series"
  callouts — see the top-level series note at the head of this document.
- **Time budget**: same 55-minute target as the other two decks. Because most slides here
  carry either a dense reference table or a multi-panel breakdown rather than a full discussion
  question (curl errors, netcat, and Test-NetConnection all have no ask, or had theirs removed/
  merged), the estimate has stayed close to target from early on: roughly **45–55 minutes**,
  no live rehearsal timing reported yet. The two slides most likely to run long if the room
  engages heavily: Zscaler (three-layer breakdown + ask) and the security-layers slide (six
  panels + the five-bullet isolation-strategy answer).

## Reconstruction notes

If `tcp-deck.html` is lost, this section plus the DNS deck's "Design system" section (shared
verbatim) is sufficient to rebuild most of it — the one piece that can't be fully reconstructed
from prose alone is the network-layout diagram's exact SVG coordinates (slide 2); re-derive its
shape from the description above (Internet/IGW column, wide public subnet with three
side-by-side boxes, private subnet below, VPC B peered to the right, VPN client below with a
tunnel line, AWS dotted boundary, upper-right legend) rather than trying to guess pixel values.
