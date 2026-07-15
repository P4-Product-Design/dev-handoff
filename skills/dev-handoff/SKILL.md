---
name: dev-handoff
description: Generate a complete dev handoff spec from a Figma design and feature context, write it into Figma as two annotation frames (Requirements and Dev Spec Handoff), then apply native Dev Mode annotations to the design across breakpoints. Use whenever someone provides a Figma link with feature context, a brand name, or KPIs and wants a dev-ready ticket — even if they just say "write up the handoff", "create the dev ticket", "build the spec", "document this for dev", or "write the requirements". Also triggers when someone is preparing to hand off a design to engineers and needs functional requirements and a user story documented. Always use this skill for any design-to-engineer handoff work.
---

## What this skill does

Takes a Figma design link, feature/test context, brand, and KPIs — reads the design thoroughly, produces a complete dev spec, and writes it into Figma as two annotation frames ready for engineers to build from.

**Engineers are the client.** A handoff is done when an engineer can build the feature without coming back with questions.

---

## Step 0: If no context was provided

If the skill was invoked without any inputs, respond with this prompt before doing anything else:

> **To get started, grab the following:**
>
> 1. **Figma link(s)** — link(s) to the dev-ready frame(s). Not the exploration file.
> 2. **Feature/test context** — a plain-language description of what this is and how it works. Include what triggers it, what the user sees, and what they can do.
> 3. **Brand** — which brand is this for?
>    - Internal: AIP, BB, GGR, MC, MN, P4, SO, SA, SWOL
>    - Partner: AARP, AUD, FOR, EAR, MA, MG, NCOA
> 4. **KPIs** — why does this feature matter? What are we measuring?
>    - One line on the business rationale
>    - Primary KPI (Take Rate, Conversion, RPV, etc.)
>    - Secondary KPIs (Rev/Visit, Engagement, etc.)
>
> Drop all of the above and I'll take it from there.

Once inputs are provided, continue to Step 1.

---

## Inputs

Collect any of these that weren't provided upfront. Use `AskUserQuestion` for anything missing before proceeding.

- **Figma link** — link to the dev-ready frame (not the exploration file)
- **Feature/test context** — what this is and how it works
- **Brand** — internal (AIP, BB, GGR, MC, MN, P4, SO, SA, SWOL) or partner (AARP, AUD, FOR, EAR, MA, MG, NCOA)
- **KPIs** — one line on why this matters + primary KPI (Take Rate, Conversion, RPV, etc.) + secondary KPIs

**Context example:**
> "On-page comparison tool that opens in a bottom drawer. Users can select a mattress to compare from the drawer or by clicking 'compare' within a product block. Once ready, they hit compare and an on-page modal opens showing each mattress with: dropdown, image, title, customer score, CTA, best-for tags, mattress details, sleeper type scores, overall score, and individual performance scores."

### Handling missing user story or business context

If the user hasn't provided a user story, business rationale, or KPIs — don't block on it or leave those fields blank. Work through this order:

**1. Ask a scoping question first.** Before drafting, use `AskUserQuestion` to ask one targeted question that surfaces the business context. Keep it focused — one question, not a form.

Good prompts to pull out business context:
- "What's the primary goal of this feature — is this primarily a conversion play, an engagement improvement, or something else?"
- "Is there a hypothesis or test goal behind this? What would make this a win?"
- "Who's the main user this helps, and what problem does it solve for them?"

**2. If no answer is given or the user says to move forward,** use the Figma design and feature context to infer reasonable values. Apply judgment:
- The user story should reflect the actual end user's action and goal (not the team's goal)
- Business impact should map to the closest plausible KPI for the feature type:
  - Comparison tools → Take Rate, RPV
  - CTAs, product blocks → Conversion, Click Rate
  - Navigation, filtering → Engagement, Time on Page
  - Trust signals, reviews → Conversion, Bounce Rate
- Mark any inferred values clearly: `[Inferred — confirm before shipping]`

**3. Never leave User Story or Business Impact blank in the spec.** A placeholder is worse than a reasonable inference — engineers need something to work from. If you're inferring, say so.

---

## Step 1: Read the Figma design

Invoke the `figma:figma-use` skill. Read the entire design — every section, component, and state. Don't proceed until you have a thorough view of:

- All sections and layouts (desktop and mobile)
- All interactive states: hover, click, empty, error, loading
- All variants and conditional displays
- Any annotations or notes already in the file

If anything is ambiguous after reading, ask follow-up questions using `AskUserQuestion` before drafting. It's better to ask now than to produce a spec with gaps.

---

## Step 2: Draft the Requirements

Write the exhaustive requirements spec. The goal is that a designer, PM, or engineer reading this has a complete picture of how the feature behaves — not just what it looks like.

**Organize by section/component.** For each one, cover:
- Core behavior
- All states: hover, active, empty, error, loading
- Content rules and data sources
- Mobile behavior — treat as equal priority to desktop, not a footnote
- Accessibility: contrast requirements, focus order, ARIA labels for any custom components

**Mark edge cases inline** using `**Edge case:**` so they're visible without breaking the flow of the requirements. Don't pull them into a separate section.

### GPV field mapping (for features involving affiliate products)

GPV (Global Product Vault) is the universal product data system. It stores every affiliate product — mattresses, sleep accessories, fitness equipment, supplements, fitness accessories — organized by category (sleep, fitness, aging). It connects to all brand sites: engineers select a product ID and GPV populates the fields automatically.

When a feature displays affiliate product data, map each Figma UI field to its GPV attribute:

- Use site-specific GPV attributes before falling back to global
- Flag any UI field with no current GPV equivalent: `[Need to make field]`
- Format mappings clearly, e.g.: `Customer Score → Customer Rating` or `Side Sleeper Score (Under 130 lbs) → [Need to make field]`

---

## Step 3: Draft the Dev Spec Handoff

This is the engineer's Notion ticket source of truth. Keep it scannable — not exhaustive like the Requirements frame. Focus only on what the engineer needs to configure and connect.

Use this template exactly:

---

### User Story
As a [user type], I want to [action] so that [outcome].

**Workaround available? (Y/N)**
[Y or N, plus brief note if yes]

### Functional Requirements
[Bullet list covering only:
- GPV field connections and mappings (what UI field connects to which GPV attribute)
- Non-obvious technical requirements (page-level enable/disable, URL substring patterns, configuration logic, fallback behavior)
- Flagged unknowns: `[Need to make field]` where GPV attributes don't exist yet
- Design file link and reference to dev checklist

Do NOT include visual requirements here — engineers see those directly in Figma.]

### Business Impact / Value
[One line on why this matters]

**Primary KPI:** [Take Rate, Conversion, RPV, etc.]
**Secondary KPIs:** [Rev/Visit, Engagement, etc.]

### Design
- Design file: [link to dev-ready frame]
- Related design ticket: [link if known]

### Known Unknowns
[Unknown + who owns resolving it + date needed by]
[If none: "None at time of handoff."]

### Related Tickets
- Design:
- QA:
- Release:

---

## Step 4: Run the clean handoff checklist

Before showing anything to the user, check every item. If any item fails, fix it first.

**Structure**
- [ ] User story is written from the user's POV, not the stakeholder's
- [ ] Functional requirements cover data connections and non-obvious technical constraints only
- [ ] Every interactive state has a defined behavior (hover, click, empty, error, loading)
- [ ] Business impact stated in one line
- [ ] KPIs listed (primary + secondary)
- [ ] Design link points to the dev-ready frame, not the exploration file

**Content**
- [ ] Every affiliate product field mapped to GPV (or flagged as `[Need to make field]`)
- [ ] Every data dependency named
- [ ] Every affected page enumerated — no "and similar pages" language
- [ ] All copy is final, or flagged with an owner and date
- [ ] Known unknowns called out with an owner and a resolution date

**Fit with existing build**
- [ ] New blocks or components explicitly flagged (don't assume WP block library match)
- [ ] Mobile spec is present and equal to desktop
- [ ] Accessibility requirements stated

**Common engineer questions — all must be answerable before sending**
- "Where does this live?" → dev-ready Figma frame is linked
- "What's the WP/CMS setup?" → editable fields and data connections covered
- "Is this a new block or existing?" → stated explicitly
- "What happens on error / empty / loading?" → every state defined
- "Is the copy final?" → yes, or flagged with owner and date
- "Which pages exactly?" → enumerated, no "and similar"
- "What pulls from GPV?" → all fields mapped or flagged
- "What's the mobile behavior?" → covered with equal depth
- "Any accessibility constraint?" → stated
- "What's the release dependency?" → related tickets linked or noted as TBD

---

## Step 5: Present for confirmation

Show both drafts to the user. Ask for feedback before writing anything to Figma.

If approved with no changes, proceed. If changes are requested, revise and confirm again before touching Figma.

---

## Step 6: Write to Figma

Invoke `figma:figma-use`. Before creating anything:
- Confirm you have the correct **dev-specific** Figma link and page (not the exploration file)
- If unsure which page to write to, ask the user before proceeding
- Scan the page for the rightmost existing frame. Place new frames starting at that x position + 120px gap.

Create two frames in this order: Requirements → Dev Spec Handoff.

---

### Frame styling — apply to all frames

All frames share this visual system. Match it exactly.

**Outer frame**
- Background: `#FFFFFF`
- Width: `1400`px, height: auto (hug contents)
- Auto-layout: vertical, padding `64`px all sides, gap `48`px
- No corner radius, no stroke

**Document header**
- Title: Inter Bold, 28px, color `#1B3A6B`
- Subtitle (brand + date, e.g., "SO Comparison Widget Tool · May 2026"): Inter Regular, 13px, color `#9CA3AF`
- Gap between title and subtitle: `6`px

**Section card**
- Background: `#FFFFFF`
- Border: `1`px solid `#E5E7EB`
- Corner radius: `8`px
- Padding: `32`px all sides
- Auto-layout: vertical, gap `20`px
- Width: fill container

**Section title**
- Inter SemiBold, 17px, color `#1D5C5C`

**Sub-header label** (e.g., CORE REQUIREMENTS, TABLET, EDGE CASES)
- Inter SemiBold, 10px, UPPERCASE, letter-spacing `1.5`px, color `#9CA3AF`

**Body text / bullet items**
- Inter Regular, 14px, color `#374151`

**Edge case callout box**
- Background: `#FFF8F0`
- Left border: `3`px solid `#F59E0B`
- Padding: `16`px
- Label "EDGE CASES": Inter SemiBold, 10px, uppercase, letter-spacing `1.5`px, color `#D97706`
- Body text: color `#92400E`

**Confirmed/decision callout box**
- Background: `#F0FDF4`
- Left border: `3`px solid `#22C55E`
- Padding: `16`px
- Label "CONFIRMED": Inter SemiBold, 10px, uppercase, letter-spacing `1.5`px, color `#16A34A`
- Body text: color `#166534`

**Interaction callout box**
- Background: `#EFF6FF`
- Left border: `3`px solid `#3B82F6`
- Padding: `16`px
- Label "INTERACTION": Inter SemiBold, 10px, uppercase, letter-spacing `1.5`px, color `#2563EB`
- Body text: color `#1E40AF`

---

### Frame 1: Requirements
- Document header title: `Requirements & Edge Cases`
- Full requirements spec organized by section/component
- Edge cases in amber callout boxes (`**EDGE CASES**` label)
- Confirmed decisions in green callout boxes (`**CONFIRMED**` label)
- GPV field mappings included where applicable (green text = existing field, red text = `[Need to make field]`)

---

### Frame 2: Dev Spec Handoff
- Document header title: `Dev Spec Handoff`
- The completed spec template: User Story → Functional Requirements → Business Impact → Design → Known Unknowns → Related Tickets
- This is what the user copies into the Notion dev ticket

---

## Step 7: Prompt for annotation context

After both frames are written, pause and ask the user before doing anything else.

Use `AskUserQuestion` with this question:

> **"The Requirements and Dev Spec frames are in Figma. Ready to add Dev Mode annotations to the design.**
>
> Which frame(s) should I annotate? Point me to the specific breakpoint frames (e.g., 'Mobile, Tablet, Desktop frames in the Comparison Modal section') — I'll skip any frames you don't mention."

Wait for the user's response before proceeding. The file may contain many designs, and only certain frames should receive annotations. Do not assume — annotate only what the user specifies.

If the user provides frame names, use `get_metadata` or `use_figma` to resolve them to node IDs before continuing.

---

## Step 8: Resolve annotation categories

`Development` and `Content` are Figma's built-in annotation categories — do not create them. Just fetch and use their IDs.

```js
const cats = await figma.annotations.getAnnotationCategoriesAsync();
const DEV = cats.find(c => c.label === 'Development').id;
const CONTENT = cats.find(c => c.label === 'Content').id;
```

**Category rules:**
- `Development` — spacing: padding, gap, itemSpacing, width, column count, column gap, layout mode shifts
- `Content` — typography: font size, font weight, font family, line height, letter spacing

---

## Step 9: Apply native annotations

**Mobile is the baseline.** Annotate only what *changes* at tablet and desktop. If a value is identical across all breakpoints, still annotate if the value is non-obvious — note that it's unchanged.

Run **one `use_figma` call per breakpoint** so page context stays clean.

### Helper function

```js
async function annotate(id, label, properties, categoryId) {
  const node = await figma.getNodeByIdAsync(id);
  if (!node) return;
  node.annotations = [{ label, properties, categoryId }];
}
```

### Property type rules — strictly enforced

**FRAME / INSTANCE nodes** — layout properties only:
`padding`, `itemSpacing`, `width`, `height`, `gridColumnCount`, `gridColumnGap`, `gridRowCount`, `gridRowGap`, `layoutMode`, `cornerRadius`, `fills`, `strokes`

**TEXT nodes** — text properties only:
`fontSize`, `fontWeight`, `fontFamily`, `fontStyle`, `lineHeight`, `letterSpacing`, `textAlignHorizontal`, `textStyleId`

Mixing the wrong property type for a node type throws `"Invalid property X for a FRAME node"`. When in doubt, use `width` for frames — it always works.

### Label format

```
[Current value] — [change description relative to previous breakpoint]
```

Examples:
- `"32px / Inter Medium (↑ from 24px mobile)"`
- `"3-column grid, ~343px each. Column gap: 48px (↑ from 16px mobile). Padding: 24px (↑ from 16px)."`
- `"24px / Inter Medium — same as mobile. Increases to 32px at desktop."`

### What to annotate

For each breakpoint frame, target these node types:

| Node type | Properties | Category |
|---|---|---|
| Modal / sheet frame | `width`, `itemSpacing` | Development |
| Container / section frame | `padding`, `itemSpacing` | Development |
| Grid / multi-column container | `padding`, `gridColumnCount`, `gridColumnGap` | Development |
| Heading text | `fontSize`, `fontWeight`, `lineHeight`, `textStyleId` | Content |
| Body / label text | `fontSize`, `fontWeight`, `textStyleId` | Content |
| Dropdown / interactive component | `width` | Development |

**Always annotate regardless of breakpoint:**
- Any spacing value that changes between breakpoints
- Any font size that changes between breakpoints
- Column count shifts
- Components that appear or disappear
- All interactive behaviors: hover states (desktop only), focus states, accordions, drawers, modals, sticky elements — annotate on the node where the interaction originates

### Clearing stale annotations

If re-annotating frames that already have annotations, clear them first:

```js
const staleIds = ['node-id-1', 'node-id-2'];
for (const id of staleIds) {
  const n = await figma.getNodeByIdAsync(id);
  if (n && n.annotations && n.annotations.length > 0) {
    n.annotations = [];
  }
}
```

**Important API rules:**
- `node.annotations` is a `ReadonlyArray` — always assign a full new array, never push
- Each annotation object: `{ label, labelMarkdown?, properties?, categoryId? }`
- Categories are file-scoped — always check for existing ones before creating (Step 8 handles this)
- Annotations appear in **Dev Mode only** — not visible in regular Edit mode or screenshots

---

## Step 10: Verify annotations

After writing, confirm annotations are present and correctly categorized:

```js
const nodeIds = ['id1', 'id2', 'id3']; // replace with annotated node IDs
const results = [];
for (const id of nodeIds) {
  const n = await figma.getNodeByIdAsync(id);
  if (!n) continue;
  results.push({
    id: n.id,
    name: n.name,
    annotationCount: n.annotations?.length ?? 0,
    label: n.annotations?.[0]?.label?.slice(0, 60),
    categoryId: n.annotations?.[0]?.categoryId
  });
}
return results;
```

**Checklist before finishing:**
- [ ] All target nodes have `annotationCount: 1`
- [ ] Spacing-change nodes have `categoryId` matching `Development`
- [ ] Font-change nodes have `categoryId` matching `Content`
- [ ] Labels include specific px values and `↑ from Xpx` callouts where values changed
- [ ] Annotations visible in Figma Dev Mode (toggle in top-right — they do NOT appear in Edit mode)
