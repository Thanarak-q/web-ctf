# Bounded graph mode for web CTFs

Use this reference only after the baseline investigation identifies independent evidence paths. A web CTF usually has sequential dependencies: recon changes the hypothesis, the hypothesis determines the probe, and the proven primitive determines the exploit chain.

## Valid and invalid fan-out

Valid fan-out starts from the same fixed input and does not create competing target state.

- Review a supplied source bundle for trust boundaries while another worker maps client JavaScript and API calls.
- Inspect a supplied PCAP/log set while another worker maps the live challenge’s normal authenticated flow.
- Analyze a known JWT implementation while another worker reviews an independently supplied backend source archive.

Do not fan out these dependent or conflicting paths:

- One worker must discover routes before another can meaningfully choose which route to exploit.
- Several workers fuzz or mutate the same rate-limited endpoint, account state, cache, or admin bot.
- One worker tries to turn a candidate into a flag while another needs that candidate as input.
- Multiple writers change one exploit script, evidence note, or writeup.

## Topology and limits

```text
Baseline owner
  -> independent read-only discovery workers (maximum 3)
  -> evidence merge owner
  -> one focused hypothesis/probe loop
  -> fresh flag verifier
  -> optional writeup
```

Set the cap before dispatching: maximum three workers, concurrency three, two waves, one retry per worker. Do not add workers merely to cover every vulnerability category. Route a failed probe back only to the hypothesis owner, rerun the affected anchor, and preserve unrelated evidence.

## Worker briefs

Each brief should identify one boundary and one evidence anchor:

- “Map JavaScript-discovered API routes, parameters, and authorization assumptions. Return source locations and one normal request per route; do not fuzz.”
- “Review the supplied source archive for server-side URL fetches, path joins, upload handling, and authorization guards. Return only reachable sinks and exact source locations.”
- “Inspect the supplied PCAP for challenge-host requests, redirects, cookies, and unusual values. Do not interact with any host.”

Every worker returns:

```yaml
claim: ""
evidence: "request/response, source location, command output, or artifact path"
confidence: low|medium|high
recommended_route: "web loop or named companion skill"
next_probe: ""
```

## Merge and verification

The merge owner creates a short table containing only findings that materially change the next hypothesis. Deduplicate matching claims, retain conflicting evidence with provenance, and do not elevate confidence without a real anchor.

After a candidate flag exists, use a verifier that did not produce it. The verifier must reproduce the transformation, read the same challenge artifact, or obtain the challenge-provided success condition from a clean baseline. If verification fails, invalidate the candidate, route the failure to the responsible hypothesis, and rerun only the impacted checks.
