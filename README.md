# Rory — a personal AI chief of staff

Rory is the AI operating system I run my working life on: a supervisor persona plus 40+ Claude skills and around twenty scheduled agents covering my job search, a portfolio of small ventures, my calendar and my household. I built it in mid-2026, starting from a single connector and growing it one real need at a time.

This repo shares the architecture and the patterns. The skills themselves are full of personal data, so what's published here is the design, the decisions behind it, and sanitized templates.

## Architecture

```
                         ┌──────────────────────────┐
                         │   Rory · supervisor       │  routes, enforces tiers,
                         │   (chief-of-staff voice)  │  never does a domain's job
                         └────────────┬─────────────┘
          ┌───────────────┬───────────┼───────────────┬────────────────┐
     job search      venture chiefs   life & home     personal       review layer
     (search, apps,  (priority,       (house, meals,  (reading,       (weekly retro,
      outreach prep)  roadmap, build,  calendar,       music, style)   portfolio gates,
                      marketing,       kids' logistics)                monthly flight deck)
                      design, ops)
          └───────────────┴─────────── shared data ───┴────────────────┘
                 Notion (trackers, pipelines) · Gmail · Calendar · Drive · git
```

- **Domain skills own their domain.** Each skill has one job and names the skills it hands off to. Priority-setting, backlog, building, marketing and review are separate owners, so no agent grades its own work.
- **Scheduled checkpoints, not polling.** Two daily checkpoints (morning and evening) plus weekly reviews replaced a swarm of 15–30-minute polling scouts — roughly 150 sessions a day down to a handful — and the runner catches up on wake.
- **Facts live outside the model.** The supervisor reads the trust file, the open-items tracker and the people map at the start of every session. Memory is a cache, never the source of truth.

## Design decisions (and why)

1. **Enforcement lives in permissions, not prompts.** An earlier version said "confirm before sending" in prose — and ran with permission checks disabled. Now every outbound tool (email, chat, calendar writes) is pinned to ask-mode in the harness. The prompt restates the rule, but it is not the gate.
2. **Trust tiers are a file, and promotion is manual.** Red = show the plan and wait. Yellow = do it, then show what you did. Green = silent, reads only — a write can never be green. Outbound is a **permanent floor**, not a tier: if an outbound action ever appears inside a tier, the file is treated as corrupted and everything reverts to red. See [`templates/trust.example.json`](templates/trust.example.json).
3. **A liveness alarm, because silent failure is the real failure.** My first multi-agent setup died and nobody noticed for 23 days. Now every scheduled job stamps a timestamp, a zero-dependency sentinel checks the stamps twice a day, and a missed checkpoint raises a desktop alert. Scheduled runs must fail loudly, with the cause named.
4. **One owner per job.** Two processes doing the same job is how things rot. When a cloud task and a local task overlapped, one was paused and labeled with the reason.
5. **Plans are signed; days inherit.** I sign a two-week plan. The daily runner executes inside it without asking; silence means proceed *at the daily level only*. A named, bounded action in a signed plan is a pre-approval; a standing "do whatever you think" grant never is.
6. **Evidence, not adjectives.** "Deployed" is not a result; a live URL serving the new thing is. Every agent report carries proof, and honest zeros stay on the board.
7. **Depth beats density.** A daily quota of five tasks per agent guaranteed five invented ones. The rule now: every agent's day must produce one thing a stranger could notice.

## Multi-agent runs

For focused pushes, Rory runs a conductor plus parallel lanes — one Claude agent per product or area, each on its own branch:

- The **conductor** runs before each wave: checks a STOP flag and a usage ceiling, verifies the last wave's claims against commits and CI runs, re-plans the next wave from what actually moved, and keeps the written record current.
- **Lanes** plan in a few lines, build, test, deploy, and only count a change once a CI job proves it on the live domain. Every run ends by logging what shipped, what it learned, and new roadmap items with expected outcomes.
- **Lessons compound.** A surprise becomes a line in the repo's `CLAUDE.md` and a cross-lane lessons file, so the next wave is smarter than the last.
- **Hard lines never move:** nothing outbound in my name, no spending, no credentials — drafts wait for me.

## Templates

- [`templates/SKILL.template.md`](templates/SKILL.template.md) — the skeleton every domain skill starts from
- [`templates/trust.example.json`](templates/trust.example.json) — trust tiers with a permanent outbound floor

---

Built by [Shannon Joyce Owens](https://shannonowens.com) · 0→1 Product Leader — Build to Revenue · see also the [Site Factory playbook](https://github.com/shannonjoy/site-factory-playbook)
