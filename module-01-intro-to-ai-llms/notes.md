# Module 1 — Introduction to AI and LLMs

**Duration:** 3 hours · split across 3 online sessions of 1 hour each
**Deck:** `Introduction_to_AI_LLM.pptx` (32 slides)
**Prerequisites:** Day 0 demo session, Python 3.10+, Ollama with `llama3.2:3b` pulled

This module builds the mental model for everything that follows. By the end you will understand what an LLM actually does under the hood — so that when you start writing tests in Module 3 onwards, you are testing something you *understand*, not a black box.

---

## How the 3 sessions are organized

| Session | Slides | Focus | Demos |
|---|---|---|---|
| **Day 1** (60 min) | 1–10 | AI fundamentals · ML · training · loss | 1 (learned behaviour) |
| **Day 2** (60 min) | 11–21 | Neural nets · NLP · tokenization · embeddings · transformers | 1 (token sensitivity) |
| **Day 3** (60 min) | 22–32 | LLM generation · sampling · context · why failures happen | 4 (attention · next-token probs · temperature · context) |

Between sessions: ~30 min of self-paced exercises (see [`exercises/`](exercises/)).

---

## DAY 1 — AI Fundamentals (60 min)

### Learning objectives
By the end of Day 1 you will be able to:
- Explain the difference between rule-based systems and learning systems.
- Distinguish AI / ML / Deep Learning / Generative AI in one sentence each.
- Describe the components of a production AI system (data → training → model → inference → deployment).
- Explain what a loss function does and why it matters.

### Key concepts

**AI is learned behaviour, not programmed logic.**
Traditional software = rules you wrote. AI = patterns extracted from data. That one shift is responsible for almost everything that makes AI testing hard.

**AI is an end-to-end pipeline.**
Dataset → preprocessing → model architecture → loss function → training → inference → deployment. When something goes wrong in production, the bug can live anywhere in that chain.

**The evolution in four steps.**
Rule-based systems → Machine Learning → Deep Learning → Large Language Models. Each step added scale and abstraction while *reducing* the amount of human rule-writing.

**Machine learning = function approximation.**
A model is just a function `f(x) → y` with millions (or billions) of tunable parameters. Training is the process of nudging those parameters so the function's outputs match the training data.

**The training loop.**
Initialize weights → Forward pass → Compute loss → Backpropagate and update → Repeat. Simple idea, but it's the backbone of every modern AI system.

**Loss function.**
A single number that measures how wrong the model is. Training = minimize this number. Common forms: MSE (regression), Cross-Entropy (classification), Perplexity (language).

### Demo you'll see
- **`day1_learned_behavior.py`** — one model handles math, a haiku, a code review, and a weird cooking-style explanation of microservices. No rule-writing. This is what "learned" actually means.

### Try it yourself (between Day 1 and Day 2)
```bash
cd module-01-intro-to-ai-llms
python examples/day1_learned_behavior.py
```
Then swap in your own tasks in `TASKS`. Try to find one the model fails at — and ask yourself why.

### Key takeaways (pin these to your brain)
1. AI systems **learn**, they don't follow rules.
2. AI is a **pipeline**, not a single model file.
3. ML is **function approximation** — `f(x) → y` with tunable parameters.
4. Training **minimizes loss** — a single number that says "how wrong are we?"
5. Same architecture, different data → totally different behaviour.

---

## DAY 2 — Neural Networks, NLP & Transformers (60 min)

### Learning objectives
By the end of Day 2 you will be able to:
- Explain what "parameters" are and why scale matters.
- Describe what a neural network layer does mathematically.
- Explain tokenization and how it affects model output.
- Describe what embeddings are and why "king − man + woman ≈ queen" works.
- Explain self-attention in plain English.

### Key concepts

**Parameters = learned weights.**
Modern LLMs have billions of them. Each one is a floating-point number tuned during training. More parameters = more capacity, but also more compute and more memory.

**Deep learning = stacked layers.**
Each layer learns a slightly more abstract representation of the input. Early layers see edges / characters; late layers see concepts.

**NLP is different from structured-data ML.**
Natural language is unstructured and ambiguous. We solve this with: tokenization → embedding → context modelling → prediction.

**Tokenization (BPE / WordPiece).**
Text is chopped into subword "tokens" before the model sees it. `"Artificial Intelligence"` becomes `["Art", "ificial", " Intelligence"]`. Small wording changes = different tokens = different outputs.

**Embeddings.**
Each token is mapped to a high-dimensional vector (768–4096 dims). Semantic meaning becomes geometry — similar words sit close in vector space.

**Contextual embeddings.**
Modern transformers don't use static word embeddings. "Bank" in "river bank" gets a different vector than "bank" in "bank account." The representation depends on the sentence.

**Transformer architecture.**
The foundation of every modern LLM (GPT, Claude, Llama, Gemini). Two superpowers:
- **Self-attention** — every token can look at every other token.
- **Parallel processing** — whole sequences run at once, not token-by-token.

### Demo you'll see
- **`day2_token_sensitivity.py`** — "Explain Artificial Intelligence in simple terms" vs "Explain AI simply". We view the tokenizer output for both prompts (via `tiktoken`) and then stream both model responses to see how differently the model answers what is, to a human, the same question.

### Try it yourself
```bash
pip install tiktoken
python examples/day2_token_sensitivity.py
```
Then open [https://platform.openai.com/tokenizer](https://platform.openai.com/tokenizer) and paste in your own sentences. Notice how spacing, punctuation, and capitalization change the token count.

### Key takeaways
1. Parameters are the **memory** of everything the model has learned.
2. A neural network is a stack of simple operations: `weight × input + bias → activation`.
3. Tokenization is lossy and inconsistent — your **prompt wording is a test surface**.
4. Embeddings turn meaning into **geometry**.
5. Self-attention is what makes transformers understand **context**.

---

## DAY 3 — LLMs, Generation & Why Things Fail (60 min)

### Learning objectives
By the end of Day 3 you will be able to:
- Describe what an LLM does at inference time (next-token prediction).
- Explain `temperature`, `top_k`, and `top_p` and pick the right one for a task.
- Describe the context window and what happens when you overflow it.
- Explain the root causes of hallucination, bias, and non-determinism.

### Key concepts

**An LLM is a next-token-probability machine.**
Given previous tokens, it outputs a probability distribution over the whole vocabulary for the next token. Sample one, append, repeat. That's the entire generation loop.

**The model doesn't "know" facts.**
`"The capital of France is"` → `Paris (98%)`, `Lyon (0.5%)`, `the (0.01%)`, `...`
The "knowledge" is encoded as probabilities over tokens, not as a database.

**Sampling strategies.**
- **Greedy** — always pick the top token. Deterministic, boring.
- **Top-K** — sample from the K most likely. Bounded creativity.
- **Top-P (nucleus)** — sample from the smallest set of tokens whose cumulative probability exceeds P. Adaptive.
- **Temperature** — scales the logits before softmax. Low = sharper (safer), high = flatter (more creative).

**Context window.**
The hard limit on how many tokens the model can attend to at once. 8K, 32K, 128K, 1M+ depending on the model. When you exceed it, the oldest tokens fall out of the window. The model didn't "forget" — the tokens were never in front of it.

**Why hallucinations happen.**
Architecture: the model generates plausible-sounding continuations. It has no built-in fact checker. When training data is thin on a topic, the model fills the gap with a statistically likely guess. Sometimes that guess is a fabricated case citation, a made-up API, or a non-existent person.

**Why bias happens.**
Training data reflects the internet. The internet reflects humanity. RLHF adds another layer of human annotator preferences. Every step propagates bias — the model is a statistical mirror of its inputs.

**Why responses vary between runs.**
- Sampling randomness (unless temperature=0)
- Floating-point non-associativity in large matrix multiplies
- Parallel execution order across GPU threads
- Occasional upstream provider changes

### Demos you'll see
- **`day3_attention_pronoun.py`** — "The trophy didn't fit in the suitcase because it was too BIG / SMALL." One word flips what "it" means. Self-attention in action.
- **`day3_next_token_probs.py`** — top-5 probability distribution for "The capital of France is ___" using `logprobs`. Makes the probability-not-knowledge idea tangible.
- **`day3_temperature_effect.py`** — same prompt, run at `temp=0.2` three times and `temp=1.2` three times. Watch the spread collapse and expand.
- **`day3_context_window.py`** — plant a fact, bury it in filler, then ask the model to recall it. With `num_ctx=512` the fact falls out; with `num_ctx=4096` it stays.

### Try it yourself
```bash
# Attention demo (works on Ollama)
python examples/day3_attention_pronoun.py

# Temperature demo
python examples/day3_temperature_effect.py

# Context overflow (Ollama)
python examples/day3_context_window.py

# Next-token probabilities — switch to OpenAI for best results
PROVIDER=openai python examples/day3_next_token_probs.py
```

### Key takeaways
1. LLMs are **probability machines**, not knowledge bases.
2. Temperature is a creativity knob — **low for tests, high for brainstorming**.
3. Context is **finite**. The model's "memory" is the window you send.
4. Hallucination and bias are **architectural**, not bugs. They're the price of a generative system.
5. Non-determinism is **the norm** — any test suite has to expect it.

---

## Module 1 → Module 2 bridge

You now know what an LLM is, how it generates text, and why it fails. Module 2 shifts gears — you'll learn the Python, pytest, and automation skills you'll use to actually *test* these systems.

## Module-wide resources
See [`resources.md`](resources.md) for papers, videos, interactive demos, and official docs.
