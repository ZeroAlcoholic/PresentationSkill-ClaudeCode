# Neutral declarative register

For forensic-analytical genres in MVP scope (investigation, audit, postmortem, strategy, RFC, research, market-analysis), every `action_title` and proof line MUST read as **fact** or **consequence-of-fact**, not as **consultant recommendation** or **emotional appeal**.

Persuasive-prescriptive register is acceptable in pitch / brand / advocacy decks — those are out of scope here. If your genre is in this skill's scope, apply this check.

---

## The two registers

| Register | Where it belongs | Recognised by |
|---|---|---|
| Declarative-factual | this skill's MVP genres | "X happened" / "Y law applies" / "Z metric is N" |
| Persuasive-prescriptive | pitch / brand / advocacy (separate render skill) | "we must" / "should" / "面臨壓力" / "leverage" / "transform" |

---

## Canonical replacements (zh / en, 1:1 parallel)

| # | Persuasive (zh) | Persuasive (en) | Declarative replacement |
|---|---|---|---|
| 1 | 必須 / 不得 / 不能 | must / cannot / should / ought | 受 [法規 X] 規範 — required by [law / policy / contract X] |
| 2 | 面臨壓力 / 時代壓力 / 紅利 | faces pressure to / time has come | 市場現況 — current state per [source] |
| 3 | 遠高於 / 明顯落後 / 大幅領先 | significantly higher / clearly lagging | 數值差為 N — differs by N units (source: Eₖ) |
| 4 | 我們建議 / 我們認為應該 | we recommend / we believe / it is suggested | 根據 [來源]，[事實] — evidence shows [fact] per [source] |
| 5 | 無法也不應 | cannot and should not | 由 [法規 / 合約] 邊界決定 — bounded by [law / contract] |
| 6 | 重新設計 / 重新定義 | redesign / reimagine / transform | 與 X 條款差異在於 ... — changes [metric] from A to B per [source] |
| 7 | 顯著提升 / 大幅改善 | significantly improves / substantially enhances | 提升 N% — improves by N% (source Eₖ) |
| 8 | 危機 / 風險爆發 | crisis / urgent / critical | [指標] 變動 N，超出 [閾值] — [metric] moved N, exceeds [threshold] by date [d] |
| 9 | 賦能 / 引領 / 佈局 | leverage / synergy / robust / holistic | (delete — name the actual mechanism) |

---

## Anti-patterns

- **顧問語氣前置** — "我們認為應該..." → 「根據 [來源]，[事實]」
- **比較形容詞無錨點** — "遠高於 / 明顯不足" → 給數值或刪除
- **情緒動員語** — "壓力 / 時代 / 危機 / 紅利" → 中性事實或刪除
- **強制性顧問判斷** — "不能也不該" / "必須立刻" → 引用具體條文或刪除
- **抽象動詞濫用** — "賦能 / 引領 / 佈局 / leverage / transform" → 用具體動詞（驗證、分類、寫入、刪除）

---

## How this gate runs

Applied during [[action-title-rubric]] scoring as a **register check** layered on criterion #2 (so-what test):

- If a title uses persuasive-prescriptive vocabulary above → score ≤10 on criterion #2 and surface for rewrite.
- If a title states a fact + its load-bearing consequence with neutral verbs → score ≥18.

Applied at body-bullet level via [[core-law]]:

- A bullet asserting "必須 X" or "should Y" without an `evidence_refs` pointing to a regulation / contract / source → mark `[AI-INFERRED]` and surface to user.

Applied at render time via [[ghost-deck-test]]: if every title reads as advocacy rather than fact, the deck fails the test — the reader can't tell findings from opinions.

---

## Why this matters

Senior reviewers in regulated industries (financial supervision, healthcare, legal, government) read persuasive register as a credibility tell. A deck that sounds like advocacy gets dismissed; a deck that reads as fact gets debated.

The rule was derived from real iteration on a financial-sector strategy deck where multiple titles were rewritten from `X 面臨壓力` (advocacy) to `X 符合 Y 法規` (fact) — same factual content, vastly different reception from the executive audience.

The replacement table is not exhaustive. When in doubt: ask *"if this title were on a court exhibit, would it survive cross-examination?"* If the answer is *"no — counsel would object to 'leading'"*, rewrite.
