# Open risks

Owner: Coordinator. Bootstrap snapshot: 2026-10-03 (Asia/Saigon).

| ID | Risk / affected roles | Current handling |
|---|---|---|
| R01 | No Git/remote at destination; status cannot be published across branches/machines | Local baseline only; Release bootstrap handoff |
| R02 | App project still points to old C: folder; Agents may read the wrong entrypoint | Canonical E: folder in AGENTS/status; verify actual workdir on recovery; change app primary folder through supported UI |
| R03 | No accepted architecture or implementation; agents may mistake target features for built features | Explicit UNKNOWN/absent facts; Architect + Owner design before implementation |
| R04 | Server API/shared contracts/root build and CI paths not mapped | No implicit ownership; Owner approves exact path mapping when actual structure is proposed |
| R05 | No independent review of bootstrap docs | Do not mark Reviewer APPROVED or integrated DONE; Reviewer can inspect before Release publishes baseline |

Do not duplicate review findings here; link their authoritative review record once available.
