# AI Writing Style Cleanup

A prompt that strips the telltale rhetorical patterns out of AI-generated writing so it reads like natural, spoken-voice prose. Bans the constructions LLMs habitually overuse: antithesis, negative parallelism, rule of three, em dashes, summary beats, hedging, and corporate register.

*Source: @0xPia on X*

---

## The Prompt

```
No antithesis. No corrective negation. No paragraph pinning. No parataxis. No summary beats. No rhetorical crutches. No negative parallelisms. No negative anaphoras. No contrasting pairs. No rule of three. No em dashes. No throat-clearing openers. No landing sentences. No setup/payoff constructions. No parallel sentence structures within a paragraph. Vary sentence length unpredictably. No stacked noun phrases. No filler intensifiers (genuinely, really, truly, actually). No corporate-register verbs (leverage, underscore, reflect). No nominalization. No hedging qualifiers. Write for the spoken voice. No performed enthusiasm.
```

---

## When to Use

- Drafting emails, posts, or articles with AI assistance
- Rewriting AI output that "sounds like AI"
- Setting a system prompt / style guide for any writing-heavy workflow

## Why It Works

Rather than vaguely asking for "natural" writing, it names the specific constructions that make prose feel machine-generated ("It's not just X, it's Y", triads, tidy landing sentences, em-dash asides) and bans them individually, which models follow far more reliably than a general tone instruction.
