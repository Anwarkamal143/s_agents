# Tracker: GitLab

Same **operations**, **labels** and **wayfinding operations** as `tracker-github.md`; implement each with `glab api` against `projects/:id/issues`.

Mapping:
- labels: `projects/:id/labels` (create with `POST`, idempotent by checking first).
- sub-issues: use issue links of type `relates_to` plus a `Parent: #<n>` first line in the child body, or GitLab work-item hierarchy if the instance supports it.
- blocked-by: `POST projects/:id/issues/:iid/links` with `link_type=is_blocked_by`.
- claim: assign via `assignee_ids`; re-read to confirm.
- close: `PUT projects/:id/issues/:iid` with `state_event=close`.

On first use, run `selftest` and replace any line above that fails with the command that worked.
