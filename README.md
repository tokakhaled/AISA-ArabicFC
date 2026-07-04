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

- [x] **M1** : Baseline inference + parsing working
- [x] **M2** : Local scoring (Track A + B)
- [x] **M3** : Post-processing v1 → 0.766 dev
- [x] **M4** : Error analysis on Baseline (failure buckets + per-function)
- [ ] **M5** : Fix broken notebook cells (generation loop + truncated postprocess)
- [ ] **M6** : Post-processing v2 targeting top ArgEM failures (wrong_value / missing_key)
- [ ] **M7** : Verify on dev leaderboard (make sure our local score matches official scorer)
- [ ] **M8** : Per-dialect check (don't let Gulf/Levantine drag us down)
- [ ] **M9** : (Optional) Track B ThinkRate
- [ ] **M10** : Blind test run + final submission (July 20 to 30)
- [ ] **M11** : System description paper (due Aug 22)

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
| ✏️ | unsloth  with  1 epoch | 0.7818 | 0.8154 |  dev |
| ✏️ | unsloth  with  2 epoch | 0.8019 |0.818 |  dev |
| ✏️ | unsloth  with  3 epoch | 0.7951 |0.8215|  dev |
| ✏️ | unsloth  with  2 epoch + post-processing | 0.8019 |0.818 |  dev |



---

## 🔗 Key Links

- Dataset: https://huggingface.co/datasets/TuwaiqAcademy/AISA-ArabicFC
- Baseline model: https://huggingface.co/TuwaiqAcademy/AISA-AR-FunctionCall-Think
- Leaderboard: https://huggingface.co/spaces/TuwaiqAcademy/AISA-ArabicFC-SharedTask-Leaderboard
- Register: https://tuwaiq.edu.sa/form/rL7Bl3wq
- Organizer contact: trdc@tuwaiq.edu.sa

---

## 📝 Decisions Log

- (Write any important decision here with its date so everyone stays in sync.)

---

## ✅ Conventions

- Main branch: `main`. Experimental work on a separate branch so we don't overwrite each other.
- Clear commit messages describing what changed.
- Pull before starting new work.
- Don't overfit the dev split. Prefer deterministic, generalizable post-processing fixes.
