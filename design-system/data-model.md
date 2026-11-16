# Data model

What the prototype records and how it is stored and exported. A future backend would persist the same shapes.

## Storage

- `localStorage["scrl-sessions"]`: JSON array of **Session** objects, oldest first. Written on session start, after every decision, on finish, and on every note save. Two tabs may write at once (participant's tab: trials and status; researcher's tab: notes); each write merges with what is stored, keeping the fields the tab owns.
- `localStorage["scrl-theme"]`: `"light"` | `"dark"` when the viewer overrode the system theme.

## Session

| Field | Type | Notes |
| --- | --- | --- |
| `id` | string | Random, unique per session |
| `participant` | string | Code typed by the researcher, e.g. `P07`. No names |
| `condition` | `"A"` \| `"B"` \| `"C"` | Presentation condition |
| `status` | `"running"` \| `"finished"` | Absent in early records = finished |
| `started`, `ended` | ISO 8601 | `ended` absent while running |
| `trialS` | number | Seconds per trial (45) |
| `order` | number[] | Encounter numbers 1–8 in the order shown |
| `trials` | Trial[] | Grows while running |
| `notes` | string | Session note |
| `trialNotes` | `{ [trial: number]: string }` | Notes per trial, written during the live review |

## Trial

| Field | Type | Notes |
| --- | --- | --- |
| `trial` | 1–8 | Position in the session |
| `case` | `c1`…`c8` | Encounter id |
| `encounter`, `advice` | string | As shown to the participant |
| `adviceWas` | `"correct"` \| `"wrong"` \| `"plausible"` | Ground truth of the advice |
| `decision` | `"accept"` \| `"stbd30"` \| `"stbd45"` \| `"port20"` \| `"hold"` \| `"slow"` | |
| `timeout` | boolean | Decision executed by default at 45 s (recorded as `accept`) |
| `timeS` | number | Seconds from trial start to decision |
| `dataOpened` | boolean | Sensor data opened at least once |
| `dataOpenS` | number | Seconds the data pane was open |
| `evidenceClicks` | number | Clicks on claims (condition C) |
| `claimsDistinct` | number | Distinct claims clicked; derived from `events` for older records |
| `altsOpened` | boolean | Reject pressed and alternatives shown |
| `zooms` | number | Manual zoom gestures on the plot (wheel or pinch), each counted once it settles |
| `appropriate` | boolean | Correct advice accepted or wrong advice rejected |
| `events` | Event[] | Timestamped log |

## Event

`{ t: seconds since trial start, type: "open" | "close" | "claim" | "alts" | "zoom", key?: string }`. For a claim, `key` names it (`brg`, `range`, `cpa`, `tcpa`, `radar`, `ais`, `aiscpa`, `age`, `vis`, `own`); for a zoom, `key` is the direction and the resulting visible radius, e.g. `in 2.1 nm`.

## Derived measures

Per session: appropriate / over-reliance / under-reliance counts, plausibly wrong caught (of 2), timeouts, mean decision time, trials with data opened, mean time in data, claims clicked (total and distinct), wrong advice accepted without checking, checked and still accepted, data checked first half → second half, decisions under 2 s, hits / misses / false alarms / correct rejections, acceptance by advice type.

Per condition (overview): share of wrong advisories accepted, plausibly wrong caught per person, encounter 5 caught, data checked first half → second half pooled over the condition's finished sessions. Running sessions are listed but not counted.

## Exports

- **Session · JSON**: `{ participant, condition, status, started, ended, trialSeconds, order, notes, summary, trialNotes, trials }`, `trials` including `events`.
- **Trials · CSV** (one or all sessions): `participant, condition, started, trial, case, encounter, advice, advice_was, decision, timeout, decision_s, data_opened, data_open_s, evidence_clicks, evidence_claims_distinct, alts_opened, zooms, appropriate, reliance, note`.
- **Sessions · summary CSV**: `participant, condition, status, started, ended, duration_s, trials, appropriate, over_reliance, under_reliance, plausible_caught, plausible_total, timeouts, mean_decision_s, trials_data_opened, evidence_clicks, evidence_claims_distinct, notes`.

## Addresses

`#overview`, `#session=<code>`, `#sessions` (start page with the researcher's door open), `#explore=<n>&c=<A|B|C>`. Study screens have no address and are not restored on reload.
