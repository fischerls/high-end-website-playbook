# High-End Website Prompt Playbook

How to prompt Claude to build a website that reads as $100k+, using a specific chain of design skills. Written 2026-08-25 after auditing `impeccable`, `ui-ux-pro-max`, `emil-design-eng`, `scrollcraft`, `scroll-world`, and `brand-elements`.

## The governing fact

`impeccable` v4.1.1 states it outright: **"The brief wins"** and *"Redirecting a clear brief toward your taste is failure."* A vague prompt produces safe, measured, template design. Prompt *length* is not the lever. Prompt *specificity* is. Adjectives like "premium" and "high-end" are unfalsifiable and get ignored.

Three things separate a $100k site from a $3k site, none of which are prompt wording:

1. A committed visual world (one point of view, no hedging)
2. Real assets (custom photography, 3D, type) instead of stock plus Inter
3. Motion and finish craft, the invisible 5 percent

## Skill chain, in order

| Step | Skill | What it buys |
|---|---|---|
| 0 | `brand-extract` or `brand-elements` | Real identity the site inherits. Skipping it guarantees generic. |
| 1 | `$impeccable init` | Writes PRODUCT.md so product truth is durable, not re-asked each session. |
| 2 | `$impeccable shape` into new-work | Locks the visual world. The step that actually decides quality. |
| 3 | `nanobanana` / Higgsfield / `img2threejs` | Asset generation. The real differentiator. |
| 4 | impeccable new-work + 21st MCP | Production build. |
| 5 | `scrollcraft` (only when scroll IS the story) | Forces 4+ device families, never the same device twice, bans the clay-diorama default. |
| 6 | `emil-design-eng` | Motion and micro-interaction pass, returns a Before/After table. |
| 7 | `$impeccable polish` + `bolder` + `delight` | Finish. |
| 8 | `ui-ux-pro-max --domain ux` + axe-core | WCAG 2.2 AA, non-negotiable. See [WCAG checklist](wcag-aa-checklist.md). |
| 9 | Ship | Deploy, verify the live URL, hand over the link. |

## The seven-slot prompt

Every empty slot is a slot the model fills with its default, and defaults are what make a site look templated.

```
$impeccable shape — new marketing site for {company}.

MODE: Persuade

1. WORLD (3-5 words + 3 refs from any medium, NOT websites)
2. POV — the one opinion this site has that competitors won't say
3. THE PEAK — one engineered moment worth screenshotting
4. ASSETS I own or will generate
5. TYPE COMMITMENT — {display} + {text}. No Inter, no system stack.
6. BANNED — what would make it look like every other site in this category
7. CONSTRAINTS — WCAG 2.2 AA, perf budget, stack

Do not split the difference. Commit to the world. Ship complete.
```

Reference slot 1 with films, album covers, shops, magazines, games. Naming websites is how a page ends up looking like an existing website, which is `scrollcraft`'s own warning.

## Standing banned list for slot 6

- Inter or the system font stack
- Purple-to-blue gradients and glassmorphism cards
- Three-icon feature grids on lucide icons
- Stock photos of people at laptops
- Emoji used as icons (`ui-ux-pro-max` flags this as an anti-pattern)
- The centered hero into three cards into testimonial slider into FAQ accordion into footer skeleton
- `transition: all 300ms`, which `emil-design-eng` bans by name
- Any section that behaves the same as the section before it

## The two force multipliers

**The peak (slot 3).** Award-winning sites have exactly one moment people screenshot. Naming it up front makes the build concentrate effort there instead of spreading it flat across every section.

**The banned list (slot 6).** Higher leverage than any positive instruction, because "premium" cannot be checked and "no gradient meshes" can.

## Escalation after v1

| Reads as | Run |
|---|---|
| Safe | `$impeccable bolder` |
| Loud or overstimulating | `$impeccable quieter` |
| Static | `$impeccable animate`, then `emil-design-eng` for the Before/After table |
| Generic | Go back to slot 1. That is a world problem, not a polish problem. Polishing a discarded look is the failure mode impeccable warns about. |
| Want the ceiling | `$impeccable overdrive` |

## First run: a landscape company in Gainesville, 2026-08-25

Ran the full chain unattended on a 45-page rebuild for a local landscape company. What the run taught:

- **The concept roll is the whole point.** Left alone the build would have shipped the dusk-patio "blue hour" site (candidate 1). The dice assigned candidate 3, the nursery plant tag, which turned out to solve the brand-orange contrast problem and gave every page a component the competitors cannot copy. Run `concept-seed.mjs` from the resolved real path of the impeccable skill's `scripts/` folder. A symlinked path prints nothing.
- `detect.mjs` silently degrades to regex unless `htmlparser2 css-select css-tree domutils` are installed in the skill directory. Install once, then trust it.
- Full-page Playwright screenshots do not fire lazy-loaded images below ~2 viewports. Scroll the page to the bottom in steps before capturing, or the review round "fixes" sections that were never broken.
- Unattended runs cannot hold the decision page. Write PRODUCT.md from what you know, label inferences, run the seed, build the assigned direction, and disclose the substitution in the first reply.
- Real client photography was the differentiator exactly as the playbook says. 99 photos at 2560px were sitting on the incumbent WordPress site, unused above the fold.

## Skills referenced

| Skill | Source |
|---|---|
| impeccable | https://github.com/pbakaus/impeccable |
| ui-ux-pro-max | https://github.com/nextlevelbuilder/ui-ux-pro-max-skill |
| emil-design-eng | Emil Kowalski's design engineering skill |
| scrollcraft | Scroll-as-timeline landing pages |
| brand-elements | Allan Peters' logo method as production SVG |
