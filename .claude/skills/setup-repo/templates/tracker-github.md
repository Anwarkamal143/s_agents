# Tracker: GitHub

Issues live in GitHub Issues. Skills perform the **operations** below by name; nothing else in the repo assumes GitHub.

Use `gh api` (REST) for everything. `gh issue create --parent/--blocked-by` can hit GraphQL, which some sandboxes block. `<o>/<r>` = `gh repo view --json nameWithOwner -q .nameWithOwner`.

> First use: run the `selftest` operation once. If sub-issues or blocked-by return 404, fall back to the "Text edges" note at the bottom.

## Labels

| Role | Label |
|---|---|
| category | `bug`, `enhancement` |
| state | `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix` |
| planning | `spec`, `wayfinder:map`, `wayfinder:grilling`, `wayfinder:prototype`, `wayfinder:research`, `wayfinder:task` |
| factory-owned | `in-factory`, `needs-human` |

Every issue has at most one category and one state. `in-factory` and `needs-human` are written only by `/factory`.

## Operations

- **ensure-labels**: for each label above, `gh label create "<name>" --force` (idempotent).
- **create-issue** `<title> <body-file> <labels...>`: `gh api repos/<o>/<r>/issues -f title=... -F body=@<file> -f 'labels[]=...'`. Print the number and the numeric `id` (`--jq '.number,.id'`).
- **add-sub-issue** `<parent#> <child-id>`: `gh api -X POST repos/<o>/<r>/issues/<parent#>/sub_issues -F sub_issue_id=<child-id>`.
- **add-blocked-by** `<issue#> <blocker-id>`: `gh api -X POST repos/<o>/<r>/issues/<issue#>/dependencies/blocked_by -F issue_id=<blocker-id>`.
- **list-ready**: `gh api "repos/<o>/<r>/issues?labels=ready-for-agent&state=open&per_page=100"`.
- **frontier**: from list-ready, drop any issue that has an open blocker: `gh api repos/<o>/<r>/issues/<n>/dependencies/blocked_by --jq 'map(select(.state=="open"))|length'` must be 0. Also drop issues labelled `in-factory`, labelled `spec`, or assigned.
- **claim** `<n>`: assign yourself (`gh api -X POST repos/<o>/<r>/issues/<n>/assignees -f 'assignees[]=<login>'`, login from `gh api user -q .login`), then add `in-factory`. If already assigned to someone else, the claim failed: pick another.
- **comment** `<n> <body-file>`: `gh api repos/<o>/<r>/issues/<n>/comments -F body=@<file>`.
- **set-state** `<n> <label>`: remove the old state label, add the new one (`DELETE .../labels/<old>`, `POST .../labels`).
- **close** `<n> <reason>`: `gh api -X PATCH repos/<o>/<r>/issues/<n> -f state=closed -f state_reason=<completed|not_planned>`.
- **read** `<n>`: issue body plus all comments (`.../issues/<n>` and `.../issues/<n>/comments`). Always read comments.
- **selftest**: create a throwaway issue pair, link them with add-sub-issue and add-blocked-by, read back, close both.

## Wayfinding operations

- **map** = one issue labelled `wayfinder:map`. **decision** = a sub-issue of the map labelled `wayfinder:<type>`.
- **map-frontier**: open sub-issues of the map (`gh api repos/<o>/<r>/issues/<map#>/sub_issues`) with no open blocker and no assignee.
- **resolve** `<n>`: comment the resolution, close it (`completed`), then append one line to the map's "Decisions so far".

## Text edges (fallback)

If native edges are unavailable, put `Blocked by: #12, #15` as the last line of the body. frontier then parses that line and checks each issue's state.
