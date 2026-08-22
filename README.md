# 🦅 Team Saqr : AISA-ArabicFC Shared Task Plan

> Arabic Function Calling for Agentic AI Systems · ArabicNLP 2026 
> Shared team file. Anyone on the team can edit.
> Last updated: ✏️ 4 jul.

---

## ⏰ Read This First : Timeline Is Tight

- **Blind test set drops: July 20, 2026** (same day). Everything before that is dev-leaderboard practice.
- **Final results: July 30, 2026.**
- **System description paper (camera-ready): August 22, 2026.**

So the real crunch window is **now → July 20** (improve on dev) and **July 20 → 30** (run best system on blind test). Ranking is decided on the **Track A Overall** score on the blind test.

---

## 🎯 The Idea

Given an Arabic user query (in 5 dialects), the system must:

1. Decide whether a tool call is needed (binary).
2. Select the correct **function** from the candidate tools.
3. Extract the **arguments** as structured JSON.
4. (Track B only) produce an Arabic reasoning trace inside `<think>...</think>` before the call.

Same intent across dialects has to map to the same tool. Example: Cairo "عايز أحجز دكتور" and Riyadh "أبي أحجز موعد عند الدكتور" are both `book_doctor_appointment`.

- **Dataset:** `TuwaiqAcademy/AISA-ArabicFC`
- **Baseline model:** `TuwaiqAcademy/AISA-AR-FunctionCall-Think` (Gemma 3 270M + LoRA)
- **Leaderboard:** `huggingface.co/spaces/TuwaiqAcademy/AISA-ArabicFC-SharedTask-Leaderboard`

---

## 🏁 Tracks

| Track | Name | What it is | Submit? |
|-------|------|-----------|---------|
| **A** | Core | Call decision + function selection + argument extraction | Yes (flagship, decides ranking) |
| **B** | Reasoning | Track A + Arabic `<think>` reasoning trace | Optional |
| **C** | Cross-Dialect | Automatic per-dialect breakdown of your A/B results | Not a separate submission |

**Our focus: Track A first** (it decides the prize ranking). Track B is a bonus if we have time.

---

## 📐 Metrics

- **Track A** = `0.40 · FnAcc + 0.60 · ArgEM`
- **Track B** = `0.30 · FnAcc + 0.50 · ArgEM + 0.20 · ThinkRate`
- **Track C** = per-dialect FnAcc + ArgEM, plus dialect gap (max − min). Diagnostic only, not ranked.

**ArgEM (Argument Exact Match) is the money metric** at 60% weight. It is also the hardest and where everyone is weak. This is where we win or lose.

---

## 📊 The Data

| Split | Samples | Notes |
|-------|---------|-------|
| Train | 10,550 | Available now |
| Dev | 525 | Available now (this is what the leaderboard scores until July 20) |
| Test (blind) | 1,050 | Released July 20 |

- **27 tools** across **8 domains**: Healthcare, Banking, Government, Islamic services, Travel, Weather, E-commerce, Utilities.
- **5 dialects:** MSA 58.3%, Levantine 16.9%, Egyptian 12.2%, Gulf 11.3%, Maghrebi 1.3%.
- Gulf and Levantine are the hardest per the organizers. Maghrebi is rare but easy to ignore at our peril.

---

## 🥊 The Bar to Beat (official baselines, on test set)

| System | FnAcc | ArgEM | Overall A | Overall B |
|--------|-------|-------|-----------|-----------|
| **AISA-Think** (270M + LoRA) | 0.982 | **0.541** | 0.717 | 0.739 |
| GPT-4o (0-shot) | 0.927 | 0.070 | 0.413 | 0.313 |
| GPT-4o (3-shot) | 0.854 | 0.122 | 0.415 | 0.317 |

Key insight: FnAcc is basically solved (0.982). **ArgEM at 0.541 is wide open.** Our whole edge comes from pushing ArgEM higher, mostly through post-processing and argument normalization.

> Our current dev result with the baseline model + post-processing v1 is **~0.766 (Track A, dev)**. Good start, but dev ≠ test, so we need robust fixes, not dev overfitting.

---

## ✅ Milestones

## ✅ Milestones

### Done
- [x] **M1** : Baseline inference + parsing working
- [x] **M2** : Local scoring (Track A + B)
- [x] **M3** : Error analysis on baseline (failure buckets + per-function)
- [x] **M4** : Post-processing v1 (built from M3 buckets) → 0.766 dev
- [x] **M5** : FunctionGemma 270M LoRA fine-tune → 0.8019 dev (2 ep)

### In progress
- [ ] **M6** : Fix broken notebook cells (`unsloth_ppp.ipynb` cell 1 truncated)
- [ ] **M7** : Scale model → Gemma 3 1B (same pipeline, batch 4/4, max_new_tokens 300)
      → if ArgEM moves +1pt or more, queue 4B (QLoRA, batch 2/8, lr 1e-4)

### Next (priority order)
- [ ] **M8** : Scorer verification — submit same file raw vs canonicalized to dev
      leaderboard. Decides if normalization is our job or the scorer's.
      ⚠️ Also check: does official ArgEM penalize extra keys? Our local scorer doesn't.
- [ ] **M9** : Error analysis v2 — rerun buckets on the FINE-TUNED model (not baseline).
      PP v1 rules came from baseline errors; the fine-tune already learned those
      (that's why PP added +0.000 on the 2-ep model).
- [ ] **M10** : Post-processing v2 (built from M9 buckets — expect dates/times,
      numeric units, dialect value phrasing, not enum canonicalization)
- [ ] **M11** : Parse-failure audit — count dev gens that fail the call regex
      (each = guaranteed zero). Add repair pass if >1%.
- [ ] **M12** : Per-dialect breakdown — if Gulf/Levantine ArgEM lags, augment:
      LLM-paraphrase MSA queries into weak dialects (gold call untouched), mix ~1–2k rows.
      Also augment negatives (only 125 "none" samples in train).
- [ ] **M13** : Track split — submit 2-epoch checkpoint to Track A, 3-epoch to Track B
      (per score log: 2ep best A, 3ep best B)
- [ ] **M14** : (Optional) Self-consistency k=5 majority-vote on args — only if time
- [ ] **M15** : Blind test run + final submission (Jul 20–30)
- [ ] **M16** : System description paper (due Aug 22)

---

## 🗓️ Timeline (official dates)

| Date | Milestone | Team Saqr action |
|------|-----------|------------------|
| now → Jul 20 | Dev leaderboard open | Iterate, push ArgEM up, climb the board |
| **Jul 20** | Registration closes + blind test released | Be registered. Pull test set day one |
| Jul 20 → 30 | Test window | Run best system, submit final predictions |
| **Jul 30** | Final results | Check ranking |
| **Aug 22** | System paper camera-ready | Write-up (cite the 3 required papers) |
| Oct 24-29 | ArabicNLP 2026, Budapest | Present if selected |

---

## 👥 Work Split

| Task | Owner | Status |
|------|-------|--------|
| ''''''''''''' | ✏️ | ⬜ |

---

## 📈 Score Log

| Date | What changed | Track A | Track B | Split |
|------|--------------|---------|---------|-------|
| ✏️ | AISA-Think baseline (reference) | 0.717 | 0.739 | test |
| ✏️ | Baseline + post-processing  | 0.7649 | 0.7828 | dev |
| ✏️ | unsloth FunctionGemma3 270m with  1 ep | 0.7818 | 0.8154 |  dev |
| ✏️ | unsloth FunctionGemma3 270m with  2 ep | 0.8019 |0.818 |  dev |
| ✏️ | unsloth FunctionGemma3 270m with  3 ep | 0.7951 |0.8215|  dev |
| ✏️ | unsloth FunctionGemma3 270m with  2 ep + post-processing | 0.8019 |0.818 |  dev |
| ✏️ | unsloth FunctionGemma3 1b with  2 ep | 0.8721 |0.8931 |  dev |
| ✏️ | unsloth FunctionGemma4 E2B | 0.9112 |0.9165 |  dev |
| ✏️ | unsloth FunctionGemma4 E4B | 0.888 |- |  dev |
| ✏️ | unsloth Qwen3-4B | 0.9253 |0.9207 |  dev |
| ✏️ | unsloth Qwen3-4B + inference pre / post-processing  | 0.9316 |0.9430 |  dev |








---

## 🔗 Key Links

- Dataset: https://huggingface.co/datasets/TuwaiqAcademy/AISA-ArabicFC
- Baseline model: https://huggingface.co/TuwaiqAcademy/AISA-AR-FunctionCall-Think
- Leaderboard: https://huggingface.co/spaces/TuwaiqAcademy/AISA-ArabicFC-SharedTask-Leaderboard
- Organizer contact: trdc@tuwaiq.edu.sa

---


## ✅ Conventions

- Main branch: `main`. Experimental work on a separate branch so we don't overwrite each other.
- Clear commit messages describing what changed.
- Pull before starting new work.
- Don't overfit the dev split. Prefer deterministic, generalizable post-processing fixes.
