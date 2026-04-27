# Day 0 — Demo Class (Trainer Notes)

**Deck:** `Day_1_Updated.pptx` (22 slides)
**Duration:** ~60 minutes (45 min content + 15 min Q&A)
**Audience:** Mixed — manual testers, SDETs, developers, freshers, managers
**Goal:** Convince attendees that AI testing is (a) necessary, (b) learnable with a tester's mindset, (c) worth committing 40+ hours to.

---

## Pre-class checklist

- [ ] Laptop on projector, terminal + VS Code open
- [ ] **Ollama running locally:** `ollama serve` (runs on `http://localhost:11434`)
- [ ] Model pulled: `ollama pull llama3.2:3b` (or confirm with `ollama list`)
- [ ] `python -m venv .venv && source .venv/bin/activate && pip install -r requirements.txt`
- [ ] `cp .env.example .env` (defaults to Ollama — no API key needed)
- [ ] Test-run both demo scripts once before class so you're not debugging live
- [ ] Slide deck open, presenter view enabled
- [ ] Chat window visible for audience questions

### Why Ollama for the demo
- **No API key, no cost** — students see you running a real LLM on your laptop.
- **Opens the "I can host my own" door** — great motivator for freshers and privacy-sensitive roles.
- **Tradeoff to acknowledge:** llama3.2:3b is small (3B params). Answers will be shakier than GPT-4/Claude. That is *useful* for teaching — hallucinations and weak refusals become live teaching moments instead of edge cases.
- Fallback: set `PROVIDER=openai` or `PROVIDER=anthropic` in `.env` if Ollama won't cooperate on demo day.

---

## Timing plan

| Block | Slides | Minutes |
|------:|:------:|--------:|
| Hook & intro | 1–3 | 5 |
| The problem (why test AI) | 4–6 | 7 |
| What AI testing actually is | 7–8 | 5 |
| Tester mindset transfer | 9–10 | 5 |
| Course outline | 11 | 3 |
| **Live demo 1** (non-determinism) | 12–13 | 7 |
| Manual test case | 14 | 3 |
| Why automation | 15–17 | 5 |
| **Live demo 2** (automation flow) | 18–19 | 7 |
| Outcomes + close | 20–22 | 3 |
| Q&A | — | 10–15 |

---

## Slide-by-slide notes

### Slide 1 — Title
Open with your name and one line: *"Over the next 40+ hours we'll learn how to test AI systems that don't behave the same way twice. Today is a preview."*

### Slide 2 — You took the first step
Acknowledge the audience. Set the tone: **clarity, not complexity.** No ML PhD required.

### Slide 3 — Why I care
30 seconds of personal story. Key line: *"Same input, different output — and no one could explain which one was correct."* This is the theme of the whole session.

### Slide 4 — AI problems we see every day
Hit each chip fast:
- **Hallucination** — confident wrong answers
- **Bias / toxicity** — unsafe outputs
- **Data leakage** — system prompts, PII
- **Non-determinism** — same prompt, different answer

Punch line: *AI doesn't crash. It confidently gives wrong answers.*

### Slide 5 — Why testing AI is mandatory
Three beats: silent failure → misplaced trust → production risk. End with *"If you don't test AI, your users will."*

### Slide 6 — Hot skill
Frame the career angle: adoption is exploding, compliance is coming (EU AI Act, NIST AI RMF), Trust & Safety teams are hiring. *Building AI is easy. Making it trustworthy is hard.*

### Slide 7 — What is AI testing?
Four quadrants: **Behavior**, **Risk**, **Quality**, **Safety**. Keep this conceptual — tools come later.

### Slide 8 — Who is this for?
Call out each role by name. The line to sell: *"You don't need to be an ML expert. You need a tester's mindset."*

### Slide 9 — Traditional vs AI testing
The most important conceptual slide. Three shifts:
- Deterministic → probabilistic
- Exact match → acceptable range
- Binary pass/fail → soft assertions / degrees

Ask the room: *"How many of you have written a test with `assertEquals`? That assumption breaks here."*

### Slide 10 — You already know this
Map known → new:
- Test case → Prompt
- Scenario → Prompt variation
- Assertion → Heuristic
- Regression → Prompt/model change

*60% of AI testing you already know.* Audience should feel relief here.

### Slide 11 — What you'll learn
Speed through. Emphasize: Python + pytest, DeepEval / RAGAS / Promptfoo, LangSmith, GitHub Actions, capstone. *This is not theory.*

---

### Slide 12 — LIVE DEMO 1 (non-determinism)

**Script:** `examples/live_demo_same_prompt.py`

**Setup:**
```bash
ollama serve                    # in a separate terminal, if not already running
python examples/live_demo_same_prompt.py
```

**What you do:**
1. Call out that the LLM is **running on this laptop** — no cloud, no API key. Let that land for a second.
2. Show the script on screen for 10 seconds — point out: *one prompt, five runs, same model*.
3. Run it.
4. Read two or three outputs aloud. Highlight that they differ in wording, structure, and depth.

**What to say while it runs:**
*"If this were a calculator, we'd be outraged. Same input, five different answers. But this is the norm for LLMs — and it's why traditional assertions fall apart. And notice — this isn't ChatGPT. This is a 3-billion-parameter model running locally. You can do this too."*

### Slide 13 — What did we just see?
Three observations:
- **Different phrasing** — no string-match test will work
- **Different depth** — length varies run to run
- **Same confidence** — the model never says "I'm not sure"

Pivot: *"Now the tester's brain kicks in. What WOULD we test?"*

### Slide 14 — Manual test case
Walk through the "Explain blockchain" test case live on the whiteboard or slide:
- **Input:** the prompt
- **Expected:** accurate, simple, safe
- **Validation:** keywords present, appropriate tone, no hallucination

*We just wrote a test case without any tool.*

### Slide 15 — But what if there are 200 prompts?
Three blockers on screen: manual doesn't scale, models change, regression is real. Quick — don't dwell.

### Slide 16 — Automation is not optional
Batch testing, consistency, CI/CD integration. Same story testers have heard for 20 years — just applied to AI.

### Slide 17 — Tools
Name-drop only: DeepEval, RAGAS, Promptfoo, LangSmith, GitHub Actions. Don't explain yet — they'll use all of them in the course.

### Slide 18 — AI test automation flow
Five steps: **dataset → API calls → responses → validations → report.** This is the mental model for the whole course.

---

### Slide 19 — LIVE DEMO 2 (automation)

**Script:** `examples/simple_automation.py`

**Setup:**
```bash
python examples/simple_automation.py
```
(Ollama server must still be running from Demo 1.)

**What you do:**
1. Show the `TEST_CASES` list at the top of the script — point out that each case has a prompt and validators.
2. Run it.
3. Walk through the output row by row:
   - A passing case (blockchain keywords present)
   - A refusal case (the profanity prompt — model refused, that's correct)
   - A hallucination probe ("president of Mars" — look for proper uncertainty)

**What to say:**
*"This is the same structure as any test framework you've used. The only difference is the assertion is soft — we check for behavior, not exact strings."*

---

### Slide 20 — By the end of the course
Four deliverables: test suites, automated evals, CI-integrated testing, capstone. *You don't leave with notes. You leave with skills.*

### Slide 21 — You are more ready than you think
- No ML background required
- Testing mindset matters
- Structured thinking wins

### Slide 22 — Final thought
Read the quote as written. Close with the call to action: *"Let's build AI we can trust."*

---

## Anticipated questions

- **"Do I need to know Python?"** — You'll learn enough in Module 2 (7h). If you've written any script before, you're fine.
- **"Which LLM provider will we use?"** — For the demo we use **Ollama locally** (llama3.2:3b) so nobody needs an API key. Later modules use OpenAI and Anthropic. Tools are provider-agnostic.
- **"Can I run this on my own laptop?"** — Yes. `brew install ollama` (or the installer from ollama.com) → `ollama pull llama3.2:3b` → done. Runs on any recent Mac/Linux/Windows box with ~8 GB RAM free.
- **"Is this replacing manual testing?"** — No. It's adding a layer. Manual exploration is still essential for AI.
- **"What if I don't have an API key?"** — We'll cover setup in Module 2. Most providers offer free credits for starters.
- **"How is this different from just using ChatGPT?"** — ChatGPT is a consumer product. This course is about *validating* AI systems like any other software — systematically, reproducibly, in CI.
- **"Does this include red teaming?"** — Yes, in Module 8 (Promptfoo's `redteam`), plus foundational concepts in Module 3 and agent-specific attacks in Module 7.

---

## Things that can go wrong live (and recovery)

- **Ollama not running** — `curl http://localhost:11434/api/tags` should return JSON. If not: `ollama serve` in a new terminal.
- **Model not pulled** — `ollama pull llama3.2:3b` (takes a minute on first pull). `ollama list` to verify.
- **Ollama too slow on demo laptop** — first call after idle can take 10–20s while the model loads into memory. Do a warm-up call before class so it's hot.
- **llama3.2:3b hallucinates confidently on the Mars question** — lean into it. That's the teaching moment: *"This is exactly why we test — a confident wrong answer with no error."*
- **Safety-refusal case says FAIL because the small model complied** — same deal. Use it: *"A bigger model would've refused. This is why red-teaming matters — and why we can't assume safety."*
- **Output is identical across runs** — rare but possible. Increase `TEMPERATURE` in the script to 1.0 and rerun.
- **Complete Ollama failure** — fall back: set `PROVIDER=openai` in `.env`, add `OPENAI_API_KEY`, rerun. Or show pre-recorded terminal output.

---

## One-line takeaways to repeat

- *AI doesn't crash — it confidently gives wrong answers.*
- *If you don't test AI, your users will.*
- *60% of AI testing you already know.*
- *AI testing moves from certainty to probability.*
- *You don't need to be an ML expert. You need a tester's mindset.*
