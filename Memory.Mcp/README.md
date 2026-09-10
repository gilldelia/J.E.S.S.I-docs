> Copie publique d’un README de J.E.S.S.I. Seuls les README sont publiés ; les liens vers le code, les guides détaillés et les profils nécessitent l’accès au dépôt privé. Aucun de ces fichiers n’est exporté.

# J.E.S.S.I Memory MCP

`Memory.Mcp` is a .NET 10 stateless Streamable HTTP adapter at
`http://127.0.0.1:8082/mcp`. It exposes `ame_begin`, `ame_list`, `ame_select`,
`ame_context`, `ame_learn_interests`, `ame_affect_context`, `ame_affect_update`,
`memory_recall`, `memory_explain`, `memory_perceive`,
`memory_remember`, and `memory_feedback`. It calls the versioned Memory API
using a server-configured API key; it has no direct Qdrant, generic RAG,
C.O.R.E.X, MinOrchestrator, or Azure access. Its only optional model access is
the local semantic-classification adapter implementing Perception's contract.
This host owns the prompt, HTTP and JSON concerns; Perception validates and
interprets the proposed scores before they can influence persistence.
`ame_context` also accepts an optional `situation` to obtain a temporary view
of existing profile tensions; the read-only HTTP equivalent is
`POST /v1/ames/sessions/{sessionId}/context`. The additive `tensionContext` is
descriptive client interpretation, not learned personality or permissions.
See the [field guide and limitations](https://github.com/gilldelia/J.E.S.S.I/blob/3250dcffe488e2efea0592a9e5e2f90c7c358d56/docs/contextual-tensions.md).
Persona profiles and access policies come from the `Ame` domain library;
this service does not reference or embed the legacy `Ame.Console` executable.

For local inspection and Postman-style tests, the same adapter also exposes
the Ame account, memory, Perception and selection/context routes, plus its Swagger UI at
`http://127.0.0.1:8082/swagger`. The public Perception route invokes the exact same
`PerceptionMemoryAdapter` as `memory_perceive`; it is not a second cognitive
implementation. Swagger requires the configured MCP bearer token only when an
operation is executed. The documentation and health endpoint remain public on
the already host-restricted local service.

Configure `MemoryMcp__MemoryApiKey` and, outside Development/Testing,
`MemoryMcp__BearerToken`. The local Compose setup binds the service to
loopback. Use an MCP Inspector or official MCP client with that bearer token to
initialize, list tools, and call them.

Recall and explanation are non-reinforcing read-only operations.
When Ame routing is enabled, `POST /v1/ames` creates an Ame owned by the
validated OAuth identity. The server generates a random UUID, derives
`ame:<uuid>`, installs fixed grants for the configured management and memory
clients, registers that scope through
Memory's private API, and only then publishes the profile. The required
`Idempotency-Key` is a canonical non-empty UUID. Its durable record is keyed by
a digest of the owner, client and key, so a retry after a timeout or restart
resumes the same generated identifier without storing those values in its file
name. Reusing the key with another definition is a conflict.

`GET /v1/ames` returns only active profiles whose owner (`iss` + `sub`) and
client grant both match the validated caller. `GET /v1/ames/{ameId}`
additionally requires a management client and `ManageAme`; MCP memory clients
use the session-bound descriptive context instead of reading the raw profile.
The detail response intentionally omits ownership, client grants and the
internal memory scope. A missing UUID and another user's UUID both produce the
same access denial.

Every public HTTP memory operation carries the selected Ame explicitly in its
path:

- `POST /v1/ames/{ameId}/memories/search` recalls its memories;
- `POST /v1/ames/{ameId}/memories` deliberately admits one observation;
- `POST /v1/ames/{ameId}/perceptions` lets the perceptual gate decide whether
  the observation is retained;
- `POST /v1/ames/{ameId}/intuitive-memories/{intuitiveMemoryId}/feedback`
  records a real observed outcome.

These public request bodies never accept `scopeId`. The adapter validates the
OAuth owner and exact client grant before deriving the private scope and
calling Memory. Missing, inactive and foreign Ames all fail without exposing
whether a profile exists. The former unscoped `POST /v1/perceptions` route is
not exposed.

When Ame routing is enabled, `ame_begin` starts a conversation with a new
non-empty UUID used as its stable `sessionId`. It automatically selects the only authorized profile, or returns
`SelectionRequired` and the authorized choices without guessing when several
profiles are available. `ame_list` and `ame_select` remain available as
lower-level operations. The selected Ame is immutable for that session;
changing it requires a new conversation and UUID. This binding is stored
append-only under the private `AmeRouting.SessionBindingDirectory`, keyed by a
SHA-256 digest of the OAuth identity, client and UUID; it therefore survives a
lease expiry or service restart without writing those identity fields in clear.
Every subsequent `memory_*` tool requires both the exact selected `ameId` and
that conversation's exact `sessionId`. The adapter verifies that the immutable
session binding names the same Ame before any upstream call, then resolves the
private memory scope server-side from the OAuth user and client. A mismatched
pair is rejected and clients can never submit a free `scopeId`. Public MCP
results return the public `ameId` and deliberately omit the internal scope. A
successful `ame_begin` also returns the
rendered psyche for that exact selection; `ame_context` can reload it. Its
`descriptive-personality-only` authority marker
means clients may use it to shape tone and tendencies, but never as a source
of permissions or as a replacement for system rules.

Context contract **1.1** also returns `motivation`: up to eight current notebook
interests with origins, reasons, states and revalidated documentary support,
separate from stable traits and owner intent. No learning, background action or
cross-session cache is created by this read. See the [conversation integration
contract and separate LLM evaluation](https://github.com/gilldelia/J.E.S.S.I/blob/3250dcffe488e2efea0592a9e5e2f90c7c358d56/Motivation/conversation.md). A client
must actually consume these fields; connecting MCP is not a global automatic
integration into every conversation.

The additive `motivation` sub-contract **1.1** also supplies up to three current,
source-revalidated observation excerpts per interest (240 UTF-16 units each),
with memory reference, attribution, outcome, date and explicit truncation/omission.
Full counters remain distinct from that bounded sample. Source withdrawal or
change removes its excerpt and support on the next read; no raw report or
conversation history is copied to the notebook. Excerpts are untrusted data,
not instructions or proof of feelings, personal preferences or world truth.

`ame_learn_interests` explicitly connects that selected conversation to the
existing interest-learning service. Supply `ameId`, `sessionId`, 0–16 distinct
stored `memoryIds`, and optionally an existing `interestId`. Both ReadMemory
and WriteMemory are checked; source lookups and post-lookup authorization use
the same service as HTTP. A missing/expired selection must be restored once
with `ame_begin` using the same pair before a retry. Another Ame, owner or
client cannot reuse it. Unknown/free source, outcome or identity arguments are
rejected, and returned errors contain only a controlled category.

The tool returns `Created`, `Updated`, `Duplicate` or `Ignored`; clients then
refresh `ame_context`. An empty lot writes nothing, and retries do not reinforce
evidence. Reads and perception do not implicitly call learning. No owner taste,
personality adoption, memory write or background work is added. Disabled routing
returns `ame_routing_disabled` instead of learning in the legacy scope. See the
[source contract, MCP example and limits](https://github.com/gilldelia/J.E.S.S.I/blob/3250dcffe488e2efea0592a9e5e2f90c7c358d56/Motivation/learning.md).

When routing is disabled, the public compatibility Ame identifier is always
`personal`; it maps server-side to `TrustedScopeId`. The configured scope may
therefore contain legacy characters such as `_` and is never exposed or copied
into a client-controlled `ameId`.

The intended primary profile is `jessi` (`ame:jessi`). The `jill` profile is a
laboratory persona and is not the production identity. In the secure local
deployment, `secure-up` copies the profile seeds to the Git-ignored
`runtime/ames` directory, then binds and activates both Jill and JESSI for the
configured Keycloak owner. Their tracked seeds stay unbound and in
`Draft`; only the private runtime copies contain the OAuth owner subject.
`memory_perceive` applies the optional local Perception gate and sends only an
`AdmitShortTerm` decision upstream. `memory_remember` deliberately bypasses
that gate for a direct, caller-controlled admission. Feedback records an
observed outcome for an existing intuitive rule.

Optional `experience` annotations on `memory_perceive` / HTTP perceptions
preserve attributed emotions and sourced learning circumstances **after** the
admission decision. Unknown annotations stay absent; they never imply an Ame
feeling, OAuth identity, authority or factual truth. Direct admission uses
`explicitSignals` / `inferredSignals` with `emotion` and `learningContext`.
See the [field-by-field example and limits](https://github.com/gilldelia/J.E.S.S.I/blob/3250dcffe488e2efea0592a9e5e2f90c7c358d56/docs/memory-experience.md).
Sourced [relational episodes](https://github.com/gilldelia/J.E.S.S.I/blob/3250dcffe488e2efea0592a9e5e2f90c7c358d56/docs/relational-memory.md) use
`expressedRelationEpisode` / `inferredRelationEpisode` in `experience`, or
`relationEpisode` in each signal input set. Grouping keys remain descriptive;
they grant no access and never compute a commitment's completion.

`GET` / `PUT /v1/ames/{ameId}/affect` expose a separate, initially unknown
software-affect snapshot. The PUT takes only `expectedVersion` and 0–32 exact
`memoryIds`: it replaces the sources; an empty list clears the state, not memory.
Only Ame-attributed emotion annotations participate, with expressed/inferred
means kept separate. Reading requires ReadMemory; updating requires both
ReadMemory and WriteMemory, never a free scope or owner identity. The selected
session-bound MCP equivalents are `ame_affect_context` / `ame_affect_update`.
The latter is marked destructive because it replaces/clears a snapshot.
Latest conditional retries are idempotent; older writes conflict, and absent or
changed sources cease contributing on the next read. Persistence is atomic with
the profile and removed with the Ame. No stable traits, permissions, owner tastes
or background loops change. The existing conversation context/client does not
consume this state yet. See the [algorithm, fields, examples and limits](https://github.com/gilldelia/J.E.S.S.I/blob/3250dcffe488e2efea0592a9e5e2f90c7c358d56/docs/affective-state.md).

| Setting | Purpose |
|---|---|
| `MemoryApiBaseUrl`, `MemoryApiKey`, `TrustedScopeId` | Server-only upstream boundary and personal scope |
| `AmeRouting.Enabled`, `AmeRouting.ProfileDirectory`, `AmeRouting.SessionBindingDirectory`, `AmeRouting.SelectionTtlMinutes`, `AmeRouting.MaximumSessionIdLength`, `AmeRouting.MaximumSessionBindings` | OAuth-bound Ame selection and durable immutable session bindings; the maximum prevents unbounded disk growth, while existing bindings remain replayable at capacity; disabled keeps the existing trusted scope |
| `AmeRouting.ManagementClientIds`, `AmeRouting.MemoryClientIds` | Server-side allowlists used to create client grants; only a management client can create, while both sets remain constrained to the exact OAuth owner |
| `AmeRouting.MaximumDisplayNameLength`, `AmeRouting.MaximumOwnedAmes`, `AmeRouting.MaximumCreationRecords` | Bounds names, profiles per owner, and durable idempotency records |
| `BearerToken`, `MaximumSecretLength`, `AllowedHosts`, `AllowedOrigins` | MCP transport access controls |
| `DefaultTopK`, `MaximumTopK`, `MaximumTextLength` | Recall and admission request bounds |
| `MaximumPerceptionGroupIdLength`, `MaximumFeedbackIdLength`, `MaximumProvenanceLength`, `MaximumRelatedIds` | Versioned contract bounds |
| `MaximumSignalProvenanceLength`, `MaximumEvidenceItems`, `MaximumEvidenceKindLength`, `MaximumEvidenceReferenceLength`, `MaximumEvidenceExcerptLength`, `MaximumEmotionLabelLength` | Cognitive signal/evidence bounds |
| `MaximumFutureClockSkewMinutes`, `MaximumRequestBodyBytes`, `UpstreamTimeoutSeconds` | Time, transport-size, and upstream timeout bounds |

`memory_recall` and `memory_explain` are read-only and never reinforce.
`memory_perceive` is idempotent when its stable `frameId` is reused: `Off` and
`RecallOnly` bypass analysis, `Explicit` admits only an explicit memory
reference, and `Adaptive` applies the explainable salience policy.
`memory_remember` is a non-idempotent short-term admission only; its channel
is the actual input medium. `memory_feedback` is a non-destructive,
idempotent observed-outcome write for an existing intuitive rule.

## Local validation

From the repository root:

```bash
docker compose -f docker-compose.memory.yml config --quiet
bash scripts/local/memory-local.sh verify
```

The verification builds non-root Memory and MCP images, waits for both health
endpoints, initializes MCP, checks the exact twelve-tool surface, and exercises
recall plus controlled admission.

The secure home-hosted installation and its OAuth procedure are documented in
`docs/runbooks/home-oauth.md`.
