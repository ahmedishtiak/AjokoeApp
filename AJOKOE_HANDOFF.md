# Ajokoe — Project Handoff

_Last updated: includes the bilingual Course reading section + 146-question course quiz._

A complete reference for resuming the **Ajokoe** build in a new chat. Hand this file to a fresh session and it should have everything it needs to continue without re-deriving context.

---

## 1. What Ajokoe is

A Finnish driving-theory and taxi-licensing **practice app**, built as a **single-file React artifact** plus a **self-contained GitHub Pages site** (`index.html`). Owner: **Ishtiak** (Finland; reviews on mobile, so keep answers concise and the UI mobile-first).

The app covers three things:

1. **Regular driving theory** — multiple-choice questions, traffic-situation scenes, risk identification, and traffic signs.
2. **Professional taxi licensing** — two official Finnish exams, each as its own bilingual (Finnish/English) question bank.
3. **Taxi exam course** — a bilingual **reading** section (6 study lessons) plus a bilingual **practice quiz** (146 questions), both derived from a real Finnish study file (`Taksikoe_fi.pdf`) that two people reportedly passed the driver exam using.

Live site pattern: `ahmedishtiak.github.io/AjokoeApp/` (rename repo to `ahmedishtiak.github.io` for a clean root URL).

---

## 2. The two taxi exams

### Exam 1 — Taksiliikennelupa (entrepreneur / operating-licence exam, "yrittäjäkoe")
- **50 questions**, pass **≥ 35/50**, **2 hours**, an **unanswered question counts as wrong**.
- **10 weighted topics:** commercial management, financial management, vehicle & traffic law, taxation, accounting, employment, wages, social security, pensions, insurance.

### Exam 2 — Taksinkuljettajan ajolupa (driver-permit exam, "kuljettajakoe")
- **50 questions**, pass **≥ 38/50 AND per-topic minimums** (failing one topic fails the whole exam), **45 minutes** (90 min oral for diagnosed dyslexia).
- **4 topics with sub-minimums:** Assisting & safeguarding passengers 15 (≥12), Special groups 15 (≥12), Customer service 10 (≥7), Traffic safety 10 (≥7).
- **Local knowledge is no longer tested** (removed 1 Apr 2019).

> Official exam questions are **not public**. All questions in the app are **original, rule-based**, grounded in research. Never reproduce official items.

---

## 3. Current state (what is DONE)

- ✅ Regular sections complete: MCQ (102), **Traffic situations (105 scenes)**, Risk (12), **Signs (61)**.
- ✅ **Two 300+ bilingual taxi banks built, integrated, validated, and smoke-tested:**
  - **TAXIYRIT = 306** questions (entrepreneur)
  - **TAXIDRIVER = 304** questions (driver)
- ✅ **Bilingual quiz UI** (FI/EN whole-quiz toggle + checkbox single-correct) wired into both the artifact and the website.
- ✅ Self-contained `index.html` rebuilt with the new banks (no CDN, works offline), jsdom smoke-tested with no runtime errors.
- ✅ **Bilingual "Taxi exam course" section added** (derived from `Taksikoe_fi.pdf`):
  - **Reading** — `COURSE` = **6 lessons** (133 sections / items), each item bilingual (`f`/`e`). Finnish parsed verbatim from the file; English is Claude's translation.
  - **Practice quiz** — `COURSEQUIZ` = **146 questions**, bilingual. Finnish questions **and** options taken **verbatim** from the file (verified 146/146 stems + 584/584 options word-for-word); **correct answers + English are authored by Claude** (the file marks no answers).
- ✅ Delivered files: `AjokoeApp.jsx` (artifact, **1,839 lines**, ~600 KB) and `index.html` (**~740 KB**).

### Authenticity status of the course quiz (important)
- The source file contains **172 question instances but only 147 distinct** — the revision lesson (Oppitunti 6) repeats 25 earlier questions verbatim. One of the 147 ("Työaika – mitä se tarkoittaa?") is a lesson heading, not an MCQ. That leaves **146 real four-option questions**, all included.
- **Verbatim Finnish, authored answers + English.** Verified every FI stem and option appears word-for-word in `Taksikoe_fi.pdf`. The 25 duplicates were intentionally **not** added (they'd just repeat); can be added on request if the user wants the literal file count.

### How the taxi sections behave
- Home screen shows a **"Professional licence · ammattilupa"** block with two buttons: 🚕 entrepreneur (green) and 🪪 driver permit (blue), each showing its question count.
- Each bank home offers **Mock** (50 random, scored vs. the real pass line), **Practice** (30 random), or **by topic**.
- A **🇫🇮 Suomi / 🇬🇧 English toggle** swaps the *entire* quiz language live (question + all four options together), mid-quiz.
- Each option has a **checkbox** that fills ✓ green (correct) or ✕ red (your wrong pick) on selection, then a **Next** button advances.

### How the taxi exam course behaves
- Home screen has a **📘 "Taxi exam course"** button (purple `#7a4dd1`) below the two licence banks.
- **CourseHome** lists the **6 lessons** (each opens a reader) and, under "Practice — questions from this material", offers **🎯 Practice questions (146)** (whole bank shuffled) and **🔀 30 random questions**. The whole section has the FI/EN toggle.
- **CourseLesson** renders a lesson's sections (heading + paragraphs/bullets) in FI or EN, with **Previous / Next** lesson navigation and the live FI/EN toggle.
- The practice quiz reuses **`BankRun`** (same bilingual checkbox UI as the licence banks), launched via `runBank(COURSEQUIZ, …, mode)` where `mode` is `"all"` (whole bank) or `"random"` (30).

---

## 4. File architecture & build system

Source lives in `/home/claude/ajokoe/` as **concatenated files** (no ES imports between them — they're glued, not module-imported):

| File | Contents |
|---|---|
| `data.js` | All core data as `export const` — questions, scenes, signs, and the two taxi banks (`TAXIYRIT`, `TAXIDRIVER`). |
| `course.js` | `export const COURSE` — the 6 bilingual reading lessons. |
| `course_quiz.js` | `export const COURSEQUIZ` — the 146 bilingual practice questions. |
| `engine.jsx` | Palette `C`, sign colors, `Styles()` (CSS), `SignSVG`, `Scene` renderer, `persp()`. |
| `ui.jsx` | All React screens + `App` + `export default App`. |

### Artifact assembly (exact commands)
```bash
echo 'import React, { useState, useEffect, useRef, useMemo } from "react";' > AjokoeFull.jsx
echo '' >> AjokoeFull.jsx
sed 's/^export const/const/' data.js >> AjokoeFull.jsx        # strips "export"
echo '' >> AjokoeFull.jsx
sed 's/^export const/const/' course.js >> AjokoeFull.jsx
echo '' >> AjokoeFull.jsx
sed 's/^export const/const/' course_quiz.js >> AjokoeFull.jsx
echo '' >> AjokoeFull.jsx
cat engine.jsx >> AjokoeFull.jsx
echo '' >> AjokoeFull.jsx
cat ui.jsx >> AjokoeFull.jsx
```
Validate it compiles:
```bash
node -e "const b=require('@babel/core');const c=require('fs').readFileSync('AjokoeFull.jsx','utf8');b.transformSync(c,{presets:[['@babel/preset-react']]});console.log('ok');"
```
Then deliver: `cp AjokoeFull.jsx /mnt/user-data/outputs/AjokoeApp.jsx` and `present_files`.

### Website (`index.html`) build — `build_site.js` pattern
The **definitive** method (fixes the earlier "stuck on Loading…" hang):
1. Read `AjokoeFull.jsx`, **strip** the `import React...` line and `export default App;`.
2. Transpile with `@babel/preset-env {targets:'>0.25%, not dead', modules:false}` **+** `@babel/preset-react {runtime:'classic'}`.
   - **Classic runtime is mandatory** → emits `React.createElement`, with **no `import` statements** (an ES import in a plain `<script>` throws a silent SyntaxError and the app never mounts — that was the root cause of the loading hang).
3. **Inline** React 18 + ReactDOM 18 **production UMD** into `<script>` tags (no CDN → offline-capable).
4. Wrap the app in an IIFE: `var useState=React.useState, useEffect=React.useEffect, useRef=React.useRef, useMemo=React.useMemo;` then the transpiled code, then `ReactDOM.createRoot(...).render(React.createElement(App))`, all inside a `try/catch` that writes errors to a visible `#err` box.
5. Smoke-test in jsdom (`runScripts:"dangerously"`), confirm no `#err`, root > 2000 chars, both bank buttons present.
6. `cp index.html /mnt/user-data/outputs/index.html`.

### npm gotchas (CRITICAL)
- `--no-save` installs **prune each other** → install all deps in **one** command:
  ```bash
  npm install --no-save @babel/core @babel/preset-env @babel/preset-react jsdom react@18.2.0 react-dom@18.2.0
  ```
- **React 19 is the default install and has NO UMD builds.** You must pin **react@18.2.0 / react-dom@18.2.0** to get `node_modules/react/umd/react.production.min.js`.
- A clean temp install reliably yields the UMD files:
  ```bash
  rm -rf /tmp/rb && mkdir -p /tmp/rb && cd /tmp/rb && npm init -y && npm install react@18.2.0 react-dom@18.2.0
  ```

### jsdom smoke-test gotcha
`window.eval(reactUMD)` needs `new JSDOM(html, { runScripts:"outside-only", pretendToBeVisual:true })`. Without `runScripts`, eval runs in Node's context and the UMD throws `self is not defined`.

---

## 5. Data inventory (current, all validated)

| Const | Count | Notes |
|---|---|---|
| `MCQ` | 102 | multiple choice |
| `SIT` | 105 | traffic-situation scenes (practice = random set of 30, pass ≥ 86% = 26/30) |
| `RISK` | 12 | risk identification |
| `SIGNS` | 61 | gallery + test |
| `TAXI` | 50 | OLD entrepreneur section — **superseded** in Home by TAXIYRIT (components/routes still exist, just no longer linked from Home) |
| `TAXIYRIT` | **306** | entrepreneur bilingual bank |
| `TAXIDRIVER` | **304** | driver bilingual bank |
| `COURSE` | **6 lessons** | bilingual reading lessons (in `course.js`) |
| `COURSEQUIZ` | **146** | bilingual practice quiz from the study file (in `course_quiz.js`) |

`data.js` has **7** `export const` lines; `course.js` and `course_quiz.js` have **1** each.

**Course quiz topic spread (`COURSEQUIZ`):** Kokeen perusteet 10, Ammatti ja säännöt 25, Asiakaspalvelu 24, Erityisryhmät 24, Turvallisuus 25, Kertaus 38. Answer-index spread ≈ 47/32/32/35.

---

## 6. The taxi-bank generator — `/home/claude/ajokoe/gen_taxi.py`

Run: `python3 gen_taxi.py` → writes `taxi_banks.js` containing `export const TAXIYRIT = [...]` and `export const TAXIDRIVER = [...]`.

**Question model:** `{ t: topic, q_fi, q_en, o_fi:[4], o_en:[4], a: correctIndex }`.

**Helper functions:**
- `QY/QD(t, qfi, qen, ofi, oen, a)` — direct question (entrepreneur / driver).
- `FY/FD(t, correct_fi, correct_en, [3 wrong_fi], [3 wrong_en])` — builds a "Mikä väittämä pitää paikkansa? / Which statement is correct?" item from one true + three false statements, with options shuffled (`random.seed(42)`).
- `shuffle_options(bank, seed)` — final pass that permutes **every** question's options (seeds **1234** YRIT / **5678** DRV). This was essential: before it, ~80% of answers sat at index 0; after, the correct index is evenly spread (~25% each).
- `validate(bank, name)` — asserts non-empty question/options, equal FI/EN option lengths, valid answer index, present topic.

**Topic distribution:**
- **TAXIYRIT (306):** Kaupallinen johtaminen 70, Taloudellinen johtaminen 37, Verotus 30, Kirjanpito 27, Eläkkeet 27, Työllistäminen 26, Vakuuttaminen 25, Ajoneuvo- ja liikennelaki 23, Sosiaaliturva 22, Palkka 19.
- **TAXIDRIVER (304):** Liikenneturvallisuus 89, Asiakaspalvelu 58, Ajoluvan perusteet 53, Erityisryhmät 53, Matkustajien avustaminen 51.

After generating: append to `data.js` with `echo "" >> data.js && cat taxi_banks.js >> data.js`.

---

## 6b. The taxi exam course — source, reader & quiz

**Source file:** `/mnt/project/Taksikoe_fi.pdf`. Despite the extension it is **plain UTF-8 text** (a Finnish study course, header begins `OPPIT…`), not a real PDF. It contains 6 lessons (`OPPITUNTI 1–6`) of study prose **plus embedded practice questions**. Structure: a question line starts `N. …?`, then each option follows on its own (hard-wrapped, ~73-char) line with **no blank line between options**; the next `N.` question follows immediately. Lesson headers are ALL-CAPS lines.

### Reading lessons — `course.js`
`COURSE` = 6 lessons, each `{ tf, te, secs:[ { hf, he, it:[ { t:'p'|'li', f, e } ] } ] }` (`f`=Finnish, `e`=English). Finnish parsed verbatim from the file; English translated by Claude. The embedded quiz dumps were stripped from the reading so lessons stay clean prose. Lessons: 1 exam intro · 2 profession (permit, employment, Kela, working/driving/rest time, payment) · 3 customer service · 4 special groups & accessibility · 5 safety, first aid, traffic rules · 6 revision recap.

### Practice quiz — `gen_course_quiz.py` → `course_quiz.js`
Run `python3 gen_course_quiz.py`. It reads **`cq_uniq.json`** (the 146 questions with FI stem + 4 FI options, verbatim) and merges an authored **`AUTH`** dict keyed by index — `AUTH[i] = {a: correctIndex, qe: english_question, oe: [4 english options]}`. Then it shuffles each question's options (`random.Random(2026)`), validates (4 FI + 4 EN options, valid index, non-empty), and writes `export const COURSEQUIZ = [...]` with model `{ t, q_fi, q_en, o_fi:[4], o_en:[4], a }`. **`TLABEL`** maps short topic codes (PER/PROF/ASI/ERIT/TURV/KERT) to Finnish display names.

**Extraction provenance (how the 146 were obtained):**
1. First pass parsed the **markdown** version (blank-line separated) → 170 question blocks, **141 clean 4-option**, 29 garbled by page-break merges.
2. To reach the full set, re-analysed the **PDF**: 172 question instances → **147 distinct** (revision lesson repeats 25). Of the 147, one is a heading (no options). The **6 missing uniques** were recovered directly from the PDF; 5 were real MCQs → **146 total**.
3. **Authenticity re-verified:** all 146 FI stems and all 584 FI options confirmed **word-for-word** in `Taksikoe_fi.pdf`.

**Answer caveats (Claude-authored, not in the file):** a few required judgement where the course text and general Finnish rules interact — e.g. child-restraint questions (the taxi-specific "under-3 must / 3–135 cm belt-only in back" exception vs. the general under-135 cm rule), the drunk-driving disqualification window (used 5 years), and the "permit revocation" item (the course says **police** can revoke, so answers follow the course). Flag these if cross-checking against an official key.

**Saved intermediate files** (in `/home/claude/ajokoe/`): `course_raw.txt`, `course_fi.json` (parsed FI lessons), `embedded_q.json` (all 170 raw md blocks), `cq_clean.json` / `cq_uniq.json` (the 146), `cq_readable.txt`, `pdf_q_raw.json`, `new_stems.json`.

---

## 7. Bilingual quiz UI components (in `ui.jsx`, inserted before `function App()`)

- **`LangToggle({ lang, setLang })`** — pill toggle 🇫🇮 Suomi / 🇬🇧 English.
- **`TaxiBankHome({ bank, title, fiName, pass, accent, lang, setLang, onRandom, onTopic, onMock, back })`** — header + LangToggle, Mock button (50 random), Practice button (30 random), per-topic list. Labels switch via a local `T(fi, en)` helper.
- **`BankRun({ deck, lang, setLang, title, pass, passOf, back, retry })`** — one question at a time; renders `q_fi/o_fi` or `q_en/o_en` by `lang`; in-quiz LangToggle swaps the whole quiz live; checkbox-style option buttons (22 px rounded box with ✓/✕); single-select; reveals correct (green) / wrong (red) on click, disabled after; Next button; ends with the shared `Score` component showing pass/fail (`passOf` mock uses threshold `Math.round(pass/50 * deck.length)`). Uses the `.bar` progress class. **Reused by the course quiz** (with `passOf:false`, so it shows a score without a pass/fail badge).
- **`CourseHome({ lang, setLang, openLesson, onQuiz, back })`** — lists the 6 lessons (each calls `openLesson(i)`) and two practice buttons (`onQuiz("all")`, `onQuiz("random")`); whole-section FI/EN toggle.
- **`CourseLesson({ lesson, idx, total, lang, setLang, prev, next, back })`** — renders one lesson's sections (amber headings, prose `p` / bulleted `li`) in FI or EN, with Previous/Next lesson nav and the live toggle.

**App wiring (in `function App()`):**
- State: `lang='fi'`, `bank`, `bankKey`, `courseLesson`.
- `runBank(src, examName, pass, mode, topic, homeView)` — `mode`: `mock` (50) / `topic` / `random` (30) / **`all`** (whole bank shuffled); sets `bank` incl. `.retry` and `.home`; bumps `bankKey` (forces remount) and routes to `bankRun`.
- Routes: `taxiYritHome` (pass 35, accent `#1f8a5b`), `taxiDrvHome` (pass 38, accent `#2a72c9`), `bankRun` (keyed by `bankKey`), **`courseHome`** (wires `onQuiz` → `runBank(COURSEQUIZ, …, 38, mode, null, "courseHome")`), **`courseLesson`** (keyed by `courseLesson` index, prev/next clamp to range).
- Home: the two-button "Professional licence · ammattilupa" section **plus** the purple 📘 "Taxi exam course" button → `courseHome`.

**CSS:** added `.bar` and `.bar>span` (gradient `#FF6A1A → #ffce29`) progress-bar rules to `Styles()` in `engine.jsx`.

---

## 8. Key learnings & gotchas (don't re-learn the hard way)

1. **Loading hang root cause** = automatic JSX runtime emitting `import { jsxDEV } from "react/jsx-dev-runtime"` in a `<script>`. **Fix:** classic runtime + inlined React 18 UMD, no CDN.
2. **React 19 has no UMD** — always pin 18.2.0 for the website build.
3. **Install all `--no-save` deps in one command** (they prune each other otherwise).
4. **Shuffle answer positions** in generated banks, or the correct option is predictably first.
5. **jsdom needs `runScripts`** for inline-script evaluation.
6. Single-file artifact: **concatenate, no inter-file ES imports**; Tailwind core only / custom CSS in `Styles`; **state is in-memory** (reload resets — disclose this to the user).
7. Watch for stray non-ASCII characters when hand-editing CSS strings (an Arabic char once slipped into a gradient and had to be fixed).
8. Mobile-first, concise. Hard-refresh / fully close the old artifact tab after redeploys to clear cache.

---

## 9. Verified facts reference (2025–2026) — the source of truth for questions

### Entrepreneur exam facts
- **VAT on passenger transport: 13.5% from 1 Jan 2026** (was 14% in 2025, 10% before). The applicable rate follows the **performance date** of the trip.
- General VAT **25.5%**; goods transport **25.5%**; **VAT-registration threshold €20,000** (from 1 Jan 2025; lower-limit relief abolished); **corporate tax 20%**.
- **Taksiliikennelupa valid 10 years**; fee **€290 electronic / €340 paper** (non-refundable). Company-specific, vehicle count not limited.
- Good repute: prison sentences assessed over **5 years**, fines over **2 years**.
- Oy minimum share capital **abolished** (1 Jul 2019); toiminimi = unlimited personal liability; ky needs **≥1 general + ≥1 silent** partner.
- Retention: vouchers **6 yr** / financial statements & books **10 yr** / real-estate **13 yr**.
- **Taksamittari** since 1 Sep 2021 (measures **distance AND time**, stored in a 3rd-party unalterable DB); **taksivalaisin** since 1 May 2021 ("TAKSI/TAXI", black + yellow). A **bus may NOT** be a taxi.
- Example trip **10 km / 15 min, 1–4 passengers**; price **above €47 must be expressly agreed** (provider bears burden of proof).
- Working time **8 h/day, 40 h/week**; leave accrues **2 / 2.5 days per month** (<1 yr / ≥1 yr); holiday-credit year **1 Apr–31 Mar**.
- Employer notice **14 d / 1 mo / 2 mo / 4 mo / 6 mo** (≤1 / 1–4 / 4–8 / 8–12 / >12 yr); employee notice **14 d** (≤5 yr) / **1 mo** (>5 yr). **No statutory minimum wage** (set by TES).
- Employer health-insurance contribution **1.91%** (2026); unemployment employer **0.31%** / employee **0.89%** (2026).
- Incomes Register: earnings report within **5 days** of payment; employer separate report by the **5th** of the following month; withholdings paid by the **12th** of the following month.
- **YEL 24.40%** (2026); YEL income lower limit **€9,423.09** (2026; was €9,208.43 in 2025); new-entrepreneur **22% discount / 48 months**; entrepreneur-unemployment YEL floor **€15,481**; YEL obligation needs **≥4 months** self-employment, take within **6 months**; **ETK** supervises. **TyEL** accrues to age **68**.
- **Liikennevakuutus** (motor TPL) compulsory — covers passengers + outsiders, **NOT your own car**; **kasko** covers your own car; **LVK** collects the neglect fee for an uninsured vehicle.

### Driver exam facts
- Permit valid **5 years** (**2 years** if over 68), fee **€50**. B licence held **≥1 year**, **Group 2** health, criminal (5 yr) / fines (3 yr) check; renewal **≤6 months** before expiry (medical cert if ≥45); police may revoke.
- Exam: 50 q, **≥38/50 + per-topic minimums**, 45 min (90 oral for dyslexia). A passed exam is valid **indefinitely**; retake **next day**; cheating = **6-month ban**. **Local knowledge no longer tested** (removed 1 Apr 2019).
- **Kela** max prices (unchanged 2026): deductible **≤€25/trip**, **€300** annual cap; base fare **€6.56 day / €10.01 night**; **€1.79/km** (1–4 pax) / **€2.30/km** (>4); waiting **€52.33/h**; assistance surcharge **+€17.47 / +€33.47**. Order from the **regional dispatch number**.
- Wheelchair **and** passenger both secured; **guide/assistance dogs carried free**; child seats chosen by **age/height/weight**; emergency number **112**.
- Driver is responsible for the transport and passengers' safety; lost property returned, not kept; customer info kept confidential; equal/non-discriminatory treatment.

> **Caveat to watch:** a 1 Sep 2026 taxi-law reform (proposed compulsory taximeter + coloured plates) is flagged but **not yet in force**. Re-check before adding questions that depend on it.

---

## 10. Remaining / possible next steps

The core request (two 300+ bilingual banks + UI + site) is **complete**. Optional follow-ups if asked:
- Add a **per-topic pass-minimum enforcement** view for the driver mock (currently the mock scores against the overall 38/50; the 4-topic sub-minimums are documented but not separately gated in the mock UI).
- Persist progress/best scores across reloads (would require leaving the in-memory-only constraint — currently intentional).
- Expand banks further or add explanations to each answer.
- Clean up temp files: `smoke2.js`, `smoke3.js`, `sitesmoke.js`, `build_site.js`, `/tmp/rb` (all safe to delete).

---

## 11. Quick resume checklist for a new chat

1. `cd /home/claude/ajokoe` — confirm `data.js`, `course.js`, `course_quiz.js`, `engine.jsx`, `ui.jsx`, `gen_taxi.py`, `gen_course_quiz.py` exist (if the sandbox reset, rebuild from this doc / prior transcript).
2. To regenerate banks: `python3 gen_taxi.py` → append `taxi_banks.js` to `data.js`. To regenerate the course quiz: `python3 gen_course_quiz.py` → writes `course_quiz.js`.
3. Reassemble the artifact with the §4 commands (now **4** data files: data.js + course.js + course_quiz.js, then engine.jsx + ui.jsx); validate it compiles.
4. Rebuild the site with `build_site.js` (§4); jsdom smoke-test; copy to `/mnt/user-data/outputs/`.
5. `present_files` the updated `AjokoeApp.jsx` + `index.html`; remind the user to **hard-refresh** and that **progress is in-memory**.

---

*Tooling: React (artifact + GitHub Pages), Python (`gen_taxi.py` for the licence banks, `gen_course_quiz.py` for the course quiz), React 18 UMD (classic JSX runtime, pre-compiled & inlined — no CDN), jsdom for smoke testing. Course content derived from `/mnt/project/Taksikoe_fi.pdf`. Project path: `/home/claude/ajokoe/`.*
