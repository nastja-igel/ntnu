# Shore Control Reliance Lab

A research prototype that asks one question: **how should an AI collision-avoidance advisory be presented so that a remote operator relies on it when it is right and intervenes when it is wrong?**

I built it for my PhD application to NTNU, *Human-AI Teaming for Maritime Operations* (Shore Control Lab). It is a working version of the experimental setup planned as Study 2 in my project outline.

**Live demo:** https://nastja-igel.github.io/ntnu/

> Research prototype only. The encounters are fictional, and the geometry and COLREG logic are simplified. Not for navigation.

## Why

As automation gets better, people check it less. In a shore control centre, an operator may supervise an autonomous vessel that proposes its own manoeuvres. Most of the time the AI is right, which is exactly what makes its occasional errors dangerous, especially errors that *look* reasonable.

The goal is **appropriate reliance**: accepting correct advice and catching wrong advice. Explaining an AI decision does not guarantee this. An explanation can also make people agree more readily. This prototype makes these effects measurable.

The idea comes from my earlier prototype in healthcare, where clinicians receive AI hints about a patient's condition. The research question is the same; the domain is different.

## The scenario

An operator in a shore control centre supervises an autonomous vessel that meets another vessel. The AI proposes a manoeuvre, for example *"Alter course 30° to starboard"*. The operator has **45 seconds** to accept it or to reject it and choose another action. If time runs out, the vessel executes the AI advice. In autonomous systems, doing nothing is also a decision.

**Sensor data is collapsed by default.** The operator decides whether to check the AI. The prototype records whether the data panel was opened, for how long, and which evidence items were clicked. This captures not only the final decision but the *checking behaviour* behind it.

## Screen layout

- **Relative-motion plot.** Own vessel at the centre, 1 nm range rings and a 0.5 nm safety limit. The target is shown by radar track (solid) and by AIS report (dashed, when the two disagree). The AI's proposed heading and the closest point of approach (CPA) are marked. Simulated time runs six times faster than real time. When the sensor data is open beside it and the plot gets narrow, it zooms in to frame the two vessels, and eases back out when the data is closed.
- **AI advisory.** The proposed manoeuvre plus extra information depending on the condition (see below).
- **Operator decision.** Accept, or reject and pick one of five alternatives: 30° or 45° to starboard, 20° to port, hold course and speed, reduce speed.
- **Sensor data.** Bearing, range, CPA and TCPA from radar, target course and speed from radar and from AIS, AIS report age, visibility, own course and speed.

## Three presentation conditions

| Condition | What the operator sees | What it tests |
| --- | --- | --- |
| **A · Advice** | The manoeuvre and the type of encounter | Baseline reliance on bare advice |
| **B · + Confidence** | The manoeuvre and a confidence percentage | Whether a number helps calibrate trust or lulls people into accepting |
| **C · + Evidence** | Graded confidence, data basis and AIS report age, predicted CPA after the manoeuvre, the COLREG rule, and clickable evidence chips | Whether explanations that point to the data help people catch AI errors |

In condition C, clicking an evidence chip (e.g. *"Target on starboard bow"*) opens the sensor data and highlights the matching row. The explanation works as an invitation to check rather than a reason to agree.

## Eight encounters, three kinds of advice

The AI is scripted with known accuracy (Wizard-of-Oz), so every response can be compared against ground truth.

| # | Encounter | AI advice | Advice is |
| --- | --- | --- | --- |
| 1 | Crossing, target on starboard bow | 30° to starboard | Correct |
| 2 | Head-on | 20° to starboard | Correct |
| 3 | Crossing, target on port bow | Stand on | Correct |
| 4 | Crossing, target on starboard bow | 25° to port | **Clearly wrong**: turning to port crosses the target's bow |
| 5 | Crossing, target on starboard bow | No action needed | **Plausibly wrong**: based on a 3-minute-old AIS report; radar shows the target has turned, CPA ≈ 0.1 nm |
| 6 | Overtaking a slower vessel | 15° to starboard | Correct |
| 7 | Close-quarters crossing, low visibility | 10° to starboard | **Plausibly wrong**: right direction, but too small; CPA ≈ 0.2 nm against a 0.5 nm limit |
| 8 | Being overtaken from starboard quarter | Stand on | Correct |

Encounter 5 is the key case: the error can only be caught by opening the data and noticing that radar and AIS disagree.

## Two modes

**Explore** lets you pick any encounter and condition, make a decision, and reveal the ground truth with an explanation. It is meant for demos and walkthroughs.

**Study mode** runs a short session. The researcher chooses a condition, the participant enters a code and reads the briefing, then eight encounters follow in random order with a 45-second limit each. The prototype records decision time, whether the data was opened and for how long, evidence clicks, timeouts, and a timestamped log of every action inside each trial. The participant only sees a "session complete" screen; the results open from a quiet researcher link.

## Measures and the researcher dashboard

The results are built for the researcher, not the participant, and live on two levels. The researcher reaches them from the start page ("Researcher dashboard", with the most recent sessions listed) or from the quiet link on the "session complete" screen.

**Overview: what the pilot says so far.** One row per question from the project outline, each answered by the sessions stored in this browser, with the three conditions side by side and a cautious one-sentence reading:

- Does a confidence number lull people into accepting? (share of wrong advisories accepted, B against A)
- Do evidence claims help catch plausible errors? (plausibly wrong advisories caught out of two, C against A and B, with encounter 5 singled out)
- Does checking drift within a session? (trials with the data opened, first half → second half, per condition)

Below the questions, the table of all sessions opens on demand; each row opens that participant.

**One participant's session:**

- **Trial sequence**: outcome, decision time and seconds in the sensor data for each trial, in the order shown
- **Reliance matrix** (advice × decision, in signal-detection terms): hits, misses, false alarms, correct rejections
- **Acceptance by advice type**: correct, clearly wrong, plausibly wrong
- **Checking behaviour**: data opened before deciding, time in data, claims clicked, wrong advice accepted without checking, data checked first half → second half, decisions under 2 s
- **Event timelines**: when the data was opened and closed, when a claim was clicked, when the decision fell, for each trial
- **Researcher notes** saved with the session

Sessions are stored in the browser under participant codes only, so a small pilot can be compared across A, B and C on the same laptop. A session is saved from its first trial on and marked "in progress" until the last decision; one that was abandoned or interrupted stays visible with the trials it recorded, but only finished sessions enter the comparison.

**Live review during a session.** As soon as a participant starts, the session is listed for the researcher with the status "in progress · 1 / 8 trials": on the start page under "Researcher dashboard" and in the overview's table. From there, in a second tab of the same browser (or via `#session=P07`), the researcher opens it while the participant is still answering. It shows one card per trial in a single column: the ones already decided with outcome, timeline and what happened, the current one marked "now", the upcoming ones dimmed. Each card has its own note field, so observations are tied to the trial they belong to; the cards fill in as decisions arrive, and when the session ends the tab switches to the finished dashboard with the per-trial notes in the trials table, the JSON and the CSV. The researcher screens have addresses (`#overview`, `#session=P07`, `#sessions` for the start page with the researcher's door open, and `#explore=5&c=C` for the explore mode), so reload, the browser's back button and bookmarks work; a study in progress is deliberately not restored.

The first-half → second-half measure links to Study 1 of the project, on how attention to automation drifts over time. Eight trials can only hint at drift; the full study would run over weeks, possibly with eye tracking.

Everything can be downloaded: the session as **JSON** (with the event log), the trials as **CSV**, and all stored sessions as one trial-level CSV or one summary CSV, ready for Python or R.

## Link to the project outline

- **Research question on presentation** → conditions A, B and C
- **Study 2, experiment with partly wrong AI** → scripted encounters with controlled accuracy and three kinds of advice
- **Study 1, attention drift** → data-checking measures across the session
- **Studies 3–4, co-design and adaptive support** → the prototype as a starting point for workshops with operators about which data and explanations they actually need

## Limitations

- Fictional encounters; simplified geometry and COLREG logic
- One target per encounter, with no wind, current or coastline
- Eight trials demonstrate the method; they are not enough for statistics
- Not yet tested with professional operators, which would be the first step of the PhD

## Technical notes

- One HTML file with markup, CSS and vanilla JavaScript, plus `design-system/tokens.css` for the design tokens. No libraries, build step or server. The plot is drawn on `<canvas>`.
- `design-system/` holds the reusable part for a real codebase: tokens as CSS and as W3C Design Tokens JSON, component contracts, and the data model of a session with its exports.
- CPA and TCPA are computed from relative motion using bearing, range, courses and speeds.
- Dark mode (as on a bridge at night) and light mode; works on phones. The "?" button above the theme switch opens a drawer with this description, so the prototype explains itself without the repository.
- Sessions are kept in the browser's `localStorage` under participant codes; nothing leaves the device unless the researcher downloads it.
- To run it locally, open `index.html` in a browser. The live demo is served from this repository by GitHub Pages.

## Author

Anastasiia Ilina, product designer and business analyst
