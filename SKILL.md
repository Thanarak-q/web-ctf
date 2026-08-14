---
name: web-ctf
description: Solve authorized web CTF challenges by orchestrating evidence-driven reconnaissance, exploit-hypothesis testing, companion CTF skills, and independent flag verification. Use for supplied challenge URLs, source bundles, containers, APIs, browser clients, authentication flows, admin bots, uploaders, web3 frontends, and HTTP artifacts when the task needs a structured web investigation rather than a single technique.
---

# Web CTF

Work only on challenge infrastructure, artifacts, accounts, and endpoints explicitly supplied or authorized for the CTF. Keep requests proportionate to the challenge; do not target third parties, seek persistence, exfiltrate unrelated data, or disrupt services.

## Own the investigation loop; route specialists by evidence

Start as the single investigation owner. Run Skill Discovery before selecting any specialist: inspect available CTF skills and select one only when recon or a validated finding establishes its boundary. Companion skills supply domain playbooks; this skill controls the evidence log, graph topology, flag verification, and final handoff.

- Use `ctf-web` for HTTP reconnaissance and confirmed web bug families: injection, authorization, browser/client attacks, server-side parser or execution paths, uploads, JWT, OAuth/OIDC, SAML, CORS, Node/prototype pollution, or Web3 application paths.
- Use `ctf-crypto` for custom cryptography, token/MAC math, PRNG, encoding transformations, or key recovery that blocks a candidate.
- Use `ctf-reverse` for supplied binaries, WASM, obfuscated JavaScript, custom virtual machines, or client code whose behavior must be reconstructed before exploitation.
- Use `ctf-pwn` only when a confirmed native memory-corruption primitive is necessary after a web foothold.
- Use `ctf-forensics` for supplied PCAPs, logs, disk images, memory dumps, or offline artifacts.
- Use `ctf-osint` for challenge-scoped public-source, DNS, hostname, image, or social-media research.
- Use `ctf-ai-ml` only for a model, prompt, or ML component that is the intended challenge boundary.
- Use `ctf-misc` for an evidence-backed cross-category puzzle that no more specific skill covers.
- Use `ctf-writeup` only after independently verifying a flag, when a standardized competition writeup is requested.

Do not select a skill just because it shares a keyword. Record the evidence that selected it, pass that worker a narrow question, and merge its finding back into this loop.

## Start an evidence log

Create a compact working note before active testing. Record challenge name and scope; supplied URL, source, container, or artifacts; allowed accounts; timestamps; request/response captures; headers; routes; JavaScript/source locations; hypotheses; commands; candidate flags; and verification result. Save large captures, scripts, and tool output as files and return paths rather than pasting them into the graph state.

## Follow the evidence loop

1. **Establish scope and baseline.** Confirm target ownership, challenge goal, login state, rate or time constraints, supplied artifacts, and flag format if known. Capture one normal request/response pair for each exposed feature before fuzzing or mutation.
2. **Map the attack surface.** Inspect HTML, inline and bundled JavaScript, source maps, headers, cookies, robots/sitemap, API schemas, routes, alternate methods, content types, redirects, uploads, webhook/OAuth callbacks, browser storage, and supplied source/configuration. Treat discovered behavior as evidence, not a vulnerability claim.
3. **State a falsifiable hypothesis.** Describe the exact trust boundary, input, expected primitive, and disproof condition. Example: “The export endpoint fetches an attacker-controlled URL; a request to an in-scope internal challenge service would prove SSRF.”
4. **Select the smallest safe probe.** First prove a primitive such as one authorization bypass, reflected value, parser discrepancy, internal request, file read, token forgery, or admin-bot action. Preserve the raw request/response or code location that proves it.
5. **Route only if needed.** Use the relevant companion skill for the confirmed boundary, then test its proposed primitive against the target or supplied artifact. Avoid chaining speculative techniques.
6. **Build the minimum exploit chain.** Progress one dependency at a time. For each edge, name the artifact crossing it and why the next step requires it. Stop when the chain reaches a candidate flag or a challenge-provided success condition.
7. **Extract and independently verify.** Use a fresh verifier context to reproduce the result from the raw artifact, request, source path, or deterministic transformation—without trusting the discovery worker’s prose. A flag-shaped string alone is not verified.

## Use bounded graph mode only when recon creates real width

Stay in one loop when probes are sequential. Use a graph only if two or more read-only investigations can start from the same baseline and neither needs another’s output—for example, supplied source review plus JavaScript/API surface mapping, or a PCAP path plus an independent auth-flow path.

Before dispatching, declare a work unit’s input, output, evidence anchor, selected skill and reason, write boundary, stop condition, and verifier. Use at most three discovery workers, concurrency three, two waves, and one retry per worker. Discovery workers should not modify shared exploits, files, target state, or writeups. Keep one merge owner and run one focused hypothesis/probe loop after merging.

Return only this compact contract from each worker:

```yaml
claim: ""
evidence: "request/response, code location, command output, or artifact path"
confidence: low|medium|high
recommended_route: "web loop or named companion skill"
next_probe: ""
```

Use [references/bounded-graph.md](references/bounded-graph.md) when deciding topology, merging evidence, or routing a failed verification.

## High-value evidence checks

- Compare the browser’s requests with server behavior under alternate methods, parameter placement, content types, encodings, and roles.
- Trace every client-discovered endpoint to its authorization, input parsing, state transition, and response boundary.
- Treat application versions, headers, comments, source maps, debug endpoints, and dependency manifests as hypotheses to validate—not automatic exploit paths.
- For browser paths, track the full sink and security context: origin, cookie attributes, CSP, CORS, `postMessage`, redirects, DOM parser, and the exact privileged action needed.
- For server-side paths, identify parser disagreements and egress restrictions before attempting a chain: proxy/app URL parsing, template context, archive extraction, XML, serializer, upload handler, or backend fetcher.
- Preserve bytes and encodings for tokens, signed values, cryptographic transformations, serialized data, and multipart uploads.

## Completion and handoff

Return either a verified flag or evidence-backed next steps. For a solved challenge, include the challenge identity and authorized scope; initial clue; concise attack chain; reproducible requests/commands or artifact paths; flag candidate; independent verification anchor; selected companion skills and why; and one reusable lesson. Never fabricate a flag, report a worker claim as verification, or expose secrets outside the authorized challenge scope.
