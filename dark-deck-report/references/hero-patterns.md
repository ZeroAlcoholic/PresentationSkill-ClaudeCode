# Hero-Slide Patterns

Slide 5 (the hero) is the structural thesis made visible. The deck succeeds or fails on whether *this slide* communicates the central idea at a glance.

Seven patterns. Each section: when to use, when not to, HTML recipe, and the load-bearing visual move. Section names match the `hero_pattern` enum in [../../report-synthesis/references/schema-v0.1.md §9](../../report-synthesis/references/schema-v0.1.md) — synthesis emits the enum value; renderer locates the recipe by that name.

For `hero_pattern` values not in this file (`quadrant-bubble`, `evidence-grid`), see fallback note in schema §9.

---

## 1. `flow-process` (Pipeline — left-to-right flow)

**Use when:** the subject is a sequential process — data pipeline, manufacturing line, request handling, claim adjudication, ML training stages.

**Don't use when:** the stages happen in parallel or out of order — that's hub-spoke or stack.

**The move:** numbered stages + arrow connectors + semantic colour progression. Reader sees direction without reading.

```html
<div class="pipeline">
  <div class="pstage pc">
    <div class="stagenum">1</div>
    <div class="ptitle">S1 · {{name}}</div>
    <div class="pdesc">{{summary}}</div>
  </div>
  <div class="parrow"></div>
  <div class="pstage pg">
    <div class="stagenum">2</div>
    <div class="ptitle">S2 · {{name}}</div>
    <div class="pdesc">{{summary}}</div>
  </div>
  <div class="parrow"></div>
  <div class="pstage pp">…</div>
  <div class="parrow"></div>
  <div class="pstage pr">…</div>
</div>
<div class="plane" style="margin-top:24px">
  <div class="card tint-a">支線 A · {{branch name}}</div>
  <div class="card tint-c">支線 B · {{branch name}}</div>
</div>
<div class="ib" style="margin-top:18px">
  <strong>S5 · 業務應用：</strong>{{output → business outcome}}
</div>
```

CSS classes (`.pipeline`, `.pstage`, `.parrow`, `.plane`) are in deck-full.html template.

**Colour progression suggestion**: cyan (input) → green (processing) → purple (synthesis) → red (validation/output) — or reverse the red/green if "red" semantically means "fail" in your context.

---

## 2. `architecture-layered` (Layered stack — top-to-bottom tiers)

**Use when:** the subject has dependency layers — application stack (UI → API → data → infra), abstraction levels (hardware → kernel → runtime → application), regulatory tiers.

**Don't use when:** layers communicate horizontally — that's a graph, not a stack. Try hub-spoke instead.

**The move:** vertical bands, top layer narrower (most user-visible), bottom layer widest (foundational). Each layer is a single card spanning full width, with a left edge accent showing the layer's role.

```html
<div class="flex-col" style="gap:8px">
  <div class="card tint-c" style="border-left:6px solid var(--cyl);padding-left:18px">
    <div class="ct" style="color:var(--cyl)">Layer 4 · {{Presentation}}</div>
    <p>{{what this layer does, key tech}}</p>
  </div>
  <div class="card tint-g" style="border-left:6px solid var(--gl);padding-left:18px">
    <div class="ct">Layer 3 · {{Application}}</div>
    <p>{{what this layer does, key tech}}</p>
  </div>
  <div class="card tint-p" style="border-left:6px solid var(--pul);padding-left:18px">
    <div class="ct" style="color:var(--pul)">Layer 2 · {{Data}}</div>
    <p>{{what this layer does, key tech}}</p>
  </div>
  <div class="card" style="border-left:6px solid var(--bd2);padding-left:18px">
    <div class="ct" style="color:var(--tm)">Layer 1 · {{Infrastructure}}</div>
    <p>{{what this layer does, key tech}}</p>
  </div>
</div>
```

The thick left border (`6px solid var(--accent)`) is the load-bearing visual — it visually anchors each tier and lets the reader follow the stack without arrows.

---

## 3. `matrix-2x2` (Quadrant / 2×2 matrix)

**Use when:** comparing options along two orthogonal axes — Eisenhower matrix, market positioning, build-vs-buy + complexity, risk-vs-reward.

**Don't use when:** you have one variable — that's a list. Don't force a 2×2 because it looks strategic.

**The move:** axis labels at edges, quadrants with semantic colour (top-right is usually the "win" quadrant — green).

```html
<div class="g2" style="grid-template-columns:auto 1fr;gap:18px;align-items:center">
  <div style="writing-mode:vertical-rl;transform:rotate(180deg);
              font-family:'IBM Plex Mono',monospace;font-size:12px;
              letter-spacing:0.15em;text-transform:uppercase;color:var(--tm);
              text-align:center">{{Y-axis label →}}</div>
  <div class="g2" style="gap:10px">
    <div class="card tint-a">
      <div class="ct" style="color:var(--aml)">Q2 · {{High Y, Low X}}</div>
      <p><strong>{{example items}}</strong></p>
      <p style="color:var(--tm)">{{interpretation}}</p>
    </div>
    <div class="card tint-g">
      <div class="ct">Q1 · {{High Y, High X}} — Sweet spot</div>
      <p><strong>{{example items}}</strong></p>
      <p style="color:var(--tm)">{{interpretation}}</p>
    </div>
    <div class="card tint-r">
      <div class="ct" style="color:var(--rl)">Q3 · {{Low Y, Low X}}</div>
      <p><strong>{{example items}}</strong></p>
      <p style="color:var(--tm)">{{interpretation}}</p>
    </div>
    <div class="card tint-c">
      <div class="ct" style="color:var(--cyl)">Q4 · {{Low Y, High X}}</div>
      <p><strong>{{example items}}</strong></p>
      <p style="color:var(--tm)">{{interpretation}}</p>
    </div>
  </div>
</div>
<div style="text-align:center;margin-top:8px;
            font-family:'IBM Plex Mono',monospace;font-size:12px;
            letter-spacing:0.15em;text-transform:uppercase;color:var(--tm)">
  {{X-axis label →}}
</div>
```

**Caption rule:** name each quadrant with the meaning, not the coordinates. "Sweet spot" beats "High value, low effort."

---

## 4. `timeline-events` (Timeline — chronological)

**Use when:** events have an order in time — incident timeline, roadmap, history of changes, version evolution.

**Don't use when:** order doesn't matter — that's a list.

**The move:** horizontal axis with timestamped pins, each pin opens to a card with the event detail. Critical events tinted red, recovery green.

```html
<div style="position:relative;padding:32px 0 8px">
  <!-- the axis line -->
  <div style="position:absolute;top:48px;left:0;right:0;height:2px;
              background:linear-gradient(90deg,var(--bd2) 0%,var(--gl) 50%,var(--bd2) 100%);"></div>
  <div class="g4" style="gap:0;position:relative">
    <div style="text-align:center;position:relative">
      <div style="font-family:'IBM Plex Mono',monospace;font-size:11px;
                  color:var(--tm);margin-bottom:8px">14:02</div>
      <div style="width:14px;height:14px;border-radius:50%;background:var(--cyl);
                  border:2px solid var(--bg);margin:0 auto 16px;
                  box-shadow:0 0 12px var(--cyl)"></div>
      <div class="card tint-c"><strong>{{Event 1}}</strong><p>{{detail}}</p></div>
    </div>
    <div style="text-align:center;position:relative">
      <div style="font-family:'IBM Plex Mono',monospace;font-size:11px;
                  color:var(--tm);margin-bottom:8px">14:18</div>
      <div style="width:14px;height:14px;border-radius:50%;background:var(--rl);
                  border:2px solid var(--bg);margin:0 auto 16px;
                  box-shadow:0 0 14px var(--rl)"></div>
      <div class="card tint-r"><strong>{{Event 2 · alert}}</strong><p>{{detail}}</p></div>
    </div>
    <div style="text-align:center;position:relative">
      <div style="font-family:'IBM Plex Mono',monospace;font-size:11px;
                  color:var(--tm);margin-bottom:8px">14:31</div>
      <div style="width:14px;height:14px;border-radius:50%;background:var(--aml);
                  border:2px solid var(--bg);margin:0 auto 16px"></div>
      <div class="card tint-a"><strong>{{Event 3 · diagnosis}}</strong><p>{{detail}}</p></div>
    </div>
    <div style="text-align:center;position:relative">
      <div style="font-family:'IBM Plex Mono',monospace;font-size:11px;
                  color:var(--tm);margin-bottom:8px">15:06</div>
      <div style="width:14px;height:14px;border-radius:50%;background:var(--gl);
                  border:2px solid var(--bg);margin:0 auto 16px;
                  box-shadow:0 0 12px var(--gl)"></div>
      <div class="card tint-g"><strong>{{Event 4 · resolved}}</strong><p>{{detail}}</p></div>
    </div>
  </div>
</div>
```

**Pin colour rule:** cyan = info, red = critical, amber = mitigating, green = resolved. Glow on the dots that matter (start/end of incident).

---

## 5. `hub-and-spoke` (central + branches)

**Use when:** one core concept feeds N parallel things — service mesh, integration map, taxonomy, capabilities radiating from a platform.

**Don't use when:** the relationship is sequential — that's pipeline.

**The move:** centered hub card (larger, tinted), spokes around it as smaller cards with a thin connecting line. Pure CSS using grid + absolute lines is brittle for >4 spokes; for ≤4 spokes use a simple 3×3 grid with the hub in the centre cell.

```html
<div class="g3" style="grid-template-columns:1fr 1.3fr 1fr;
                       grid-template-rows:1fr 1fr 1fr;gap:12px;
                       min-height:420px;align-items:center">
  <div></div>
  <div class="card" style="text-align:center">
    <div class="ct" style="color:var(--cyl)">Spoke N · {{name}}</div>
    <p>{{description}}</p>
  </div>
  <div></div>

  <div class="card" style="text-align:center">
    <div class="ct" style="color:var(--aml)">Spoke W · {{name}}</div>
    <p>{{description}}</p>
  </div>
  <div class="card tint-g" style="text-align:center;padding:24px">
    <div class="ct" style="font-size:14px">CORE</div>
    <h3 style="color:var(--gl);margin:6px 0">{{Hub name}}</h3>
    <p>{{what the hub does}}</p>
  </div>
  <div class="card" style="text-align:center">
    <div class="ct" style="color:var(--pul)">Spoke E · {{name}}</div>
    <p>{{description}}</p>
  </div>

  <div></div>
  <div class="card" style="text-align:center">
    <div class="ct" style="color:var(--rl)">Spoke S · {{name}}</div>
    <p>{{description}}</p>
  </div>
  <div></div>
</div>
```

Use SVG overlay for connecting lines if needed, but the hub-in-centre layout reads as hub-spoke even without lines.

---

## 6. `comparison-table`

**Use when:** evaluating N options across M criteria — vendor selection, before/after, framework comparison, build-vs-buy.

**Don't use when:** you have 2 things and 2 criteria — that's overkill, just write prose.

**The move:** rows are options, columns are criteria. **The recommendation column gets a coloured badge**, not just a star/check. Highlighted row uses `tr.hi` for the chosen option.

```html
<div class="tw">
  <table>
    <thead>
      <tr>
        <th>選項</th>
        <th>初始成本</th>
        <th>維運成本</th>
        <th>風險</th>
        <th>{{key criterion}}</th>
        <th>推薦</th>
      </tr>
    </thead>
    <tbody>
      <tr class="hi">
        <td><strong>{{Option A}}</strong></td>
        <td>{{$X}}</td><td>{{$Y/mo}}</td><td>{{specific risk}}</td><td>{{value}}</td>
        <td><span class="b bg">採納</span></td>
      </tr>
      <tr>
        <td>{{Option B}}</td>
        <td>{{$X}}</td><td>{{$Y/mo}}</td><td>{{risk}}</td><td>{{value}}</td>
        <td><span class="b ba">備選</span></td>
      </tr>
      <tr>
        <td>{{Option C}}</td>
        <td>{{$X}}</td><td>{{$Y/mo}}</td><td>{{risk}}</td><td>{{value}}</td>
        <td><span class="b br">排除</span></td>
      </tr>
    </tbody>
  </table>
</div>
```

**Specifics, not hedges.** "$3,400 initial / $180 mo" beats "moderate cost." Numbers carry the argument.

---

## 7. `funnel-conversion` (Funnel — top-to-bottom narrowing)

**Use when:** showing conversion, qualification, attrition — sales funnel, MLOps validation gates, hiring pipeline, refining N options to 1.

**Don't use when:** the stages don't actually narrow — that's just a pipeline.

**The move:** trapezoidal cards, each narrower than the one above. Numbers shown at every stage to make the narrowing literal.

```html
<div class="flex-col" style="gap:10px;align-items:center">
  <div class="card tint-c" style="width:100%;text-align:center;padding:18px">
    <div class="ct" style="color:var(--cyl)">Stage 1 · {{name}}</div>
    <div style="display:flex;justify-content:space-between;align-items:center;margin-top:6px">
      <div><strong style="font-size:24px">{{N}}</strong>
        <span style="color:var(--tm);font-size:13px;margin-left:6px">{{units}}</span></div>
      <div style="color:var(--tm);font-size:13px">100%</div>
    </div>
  </div>
  <div class="card tint-g" style="width:82%;text-align:center;padding:16px">
    <div class="ct">Stage 2 · {{name}}</div>
    <div style="display:flex;justify-content:space-between;align-items:center;margin-top:6px">
      <div><strong style="font-size:22px">{{N}}</strong></div>
      <div style="color:var(--gl);font-size:13px">{{X%}} pass</div>
    </div>
  </div>
  <div class="card tint-a" style="width:64%;text-align:center;padding:14px">
    <div class="ct" style="color:var(--aml)">Stage 3 · {{name}}</div>
    <div style="display:flex;justify-content:space-between;align-items:center;margin-top:6px">
      <div><strong style="font-size:20px">{{N}}</strong></div>
      <div style="color:var(--aml);font-size:13px">{{X%}} pass</div>
    </div>
  </div>
  <div class="card tint-p" style="width:44%;text-align:center;padding:14px">
    <div class="ct" style="color:var(--pul)">Final · {{name}}</div>
    <div style="margin-top:6px">
      <strong style="font-size:26px;color:var(--pul)">{{N}}</strong>
      <span style="color:var(--tm);font-size:13px;margin-left:8px">{{X%}} overall</span>
    </div>
  </div>
</div>
```

**Always show conversion rate**, not just absolute numbers — that's where the story lives.

---

## Combining patterns

A single deck can use **two hero patterns** — usually one in slide 5 and another in slide 10. Don't go beyond two; the deck loses identity.

| Combo | Works for |
|-------|-----------|
| `flow-process` (S5) + `matrix-2x2` (S10) | Architecture brief with build-vs-buy decision |
| `architecture-layered` (S5) + `comparison-table` (S10) | RFC with vendor selection |
| `timeline-events` (S5) + `funnel-conversion` (S10) | Postmortem with response-quality analysis |
| `hub-and-spoke` (S5) + `architecture-layered` (S10) | Platform pitch with technical depth |
| `matrix-2x2` (S5) + `funnel-conversion` (S10) | Strategy doc with priority matrix and conversion model |

---

## Anti-patterns

Avoid these hero-slide shapes — they appear smart but communicate poorly:

- **Box-and-arrow soup** — boxes with arrows pointing in every direction. Use pipeline or hub-spoke instead, both have a clear focal pattern.
- **Wireframe stack** — placeholder UI mockups in a frame. Not relevant to a decision deck.
- **Decorative SVG flourishes** — abstract organic blobs / geometric ornaments. Generic AI aesthetic.
- **Mind-map** — radial expanding tree. Cute for brainstorms; meaningless in a forensic deck.
- **Cartoon icons** — sketchy isometric scenes. Reads as Slidesgo template.
