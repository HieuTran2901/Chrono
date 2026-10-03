# Risks

Owner: Coordinator. Updated bootstrap snapshot: 2026-10-03 (Asia/Saigon).

| ID | Risk / affected roles | Current handling |
|---|---|---|
| R01 / resolved for initial publication | No Git/remote at destination | GOV-002: origin configured; initial framework push to developer verified. Standard develop/Coordinator operational branches remain pending |
| R02 / open | App project may still point to old C: folder; Agents could read wrong entrypoint | Canonical E: folder in AGENTS/status; old folder has a forwarder; verify actual workdir on recovery |
| R03 / open | No accepted architecture or implementation; target features may be mistaken for built features | Explicit absent facts; Architect + Owner design before implementation |
| R04 / open | Server API/shared contracts/root build and CI paths not mapped | No implicit ownership; Owner approves exact mapping when actual structure is proposed |
| R05 / open | No independent review of bootstrap docs; standard branch flow not activated | Publication was directly authorized by Owner; no Reviewer APPROVED/develop integration claim. Coordinator/Release establish subsequent normal flow |

Do not duplicate future review findings here; link authoritative review records.
