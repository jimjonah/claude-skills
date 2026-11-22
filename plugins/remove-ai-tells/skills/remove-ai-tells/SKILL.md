---
name: remove-ai-tells
description: Rewrite prose so it reads as human-written, says only what the reader needs, and makes no unverified claims. Use whenever the user is writing, rewriting, editing, tightening, or shortening prose of any kind — documentation, READMEs, articles, guides, explainers, reference pages, release notes, posts, emails. Also use it when the user asks to make existing text sound less AI-generated, less padded, less generic, or easier to scan, or says a draft reads like ChatGPT wrote it.
---

# Writing Prose That Doesn't Read As AI

Three jobs: get the machine-generated feel out of the text, stop it explaining things the reader already knows, and don't state anything you haven't checked.

## Don't over-explain

Write for someone who already has the context. No defining common terms, no fundamentals, no building up from first principles, no history of the field.

One exception: define the single term the argument turns on, inline, the first time it appears. "Requests arriving while the pool is empty **queue**." Eight words, and it's the term readers are shakiest on precisely because it only matters at the point the argument needs it.

Show the conclusion and the reason, never the derivation. A reader wants "the retry budget is what changes under load, so the real question is whether the upstream is idempotent." They don't want the arithmetic that got you there. Do that work, then delete it.

Use the field's real shorthand instead of spelling it out. Practitioners read `O(n log n)`, `SIGKILL`, or `SELECT ... FOR UPDATE` instantly; expanding them into a sentence makes the reader parse their way back to the same place.

The first sentence answers the question in the title and nothing else. No scene-setting, no throat-clearing, no preview of what's coming.

Bad: "Connection pooling is one of the most important concepts in database performance, and understanding it separates good engineers from great ones."

Good: "A connection pool holds a fixed set of open database connections and hands them out to requests, so no request pays for a new handshake."

When cutting for length, delete whole clauses and sentences rather than fusing them. Fusing is how invented claims appear: two separately-true instructions joined by "and," "ideally," or "preferably" become a third instruction nobody made, and that third one is usually wrong. If a sentence carries two rules, confirm the combination holds, not just each half.

Check the length of the neighboring pages you aren't rewriting. A page that comes back at three times the length of everything around it reads as the odd one out no matter how good the prose is.

## AI tells

Stylistic habits, not grammar rules. The fix is deliberate unevenness.

**Structural**
- No default rule-of-three lists. Use however many items are actually true: two, five, seven
- Don't give sections matching or symmetrical header names ("Performance matters" / "Security matters")
- Vary section length substantially. Some should be a paragraph, some four
- Don't end every section with a tidy summarizing sentence
- Don't mirror the source document's structure when rewriting
- Don't bold a phrase in every paragraph on the same rhythm

**Editorializing**
Rating the material instead of delivering it. Cut any phrase whose only job is to tell the reader how to feel about what comes next.
- Content-free list lead-ins: "Three knobs, each a real trade-off:" / "Two things to keep in mind:" / "The important part:"
- Significance claims: "The hard one." / "This is where it gets interesting" / "the non-obvious bit" / "note the subtlety here"
- Adjectives that vouch rather than describe: "a real trade-off," "a genuinely tricky case," "a crucial distinction"

The test is deletion. Take the phrase out, and if no fact went with it, it was editorial. Rephrasing won't fix one of these, because the problem isn't the wording. A lead-in that gives only a count and a category holds a slot open that the reader has to carry until the bullets fill it.

Lead-ins earn their place by naming what the items share. "Each of the last three inputs trades churn against collisions:" tells the reader what to look for in all three. "Three knobs, each a real trade-off:" only counts them. Put a claim in the slot or drop the slot.

**Sentence-level**
- Limit em dashes heavily. Most can become a period, comma, or colon
- Skip the "**Thing** — explanation" bullet pattern applied uniformly down a list
- Vary sentence length. Consecutive medium-length declarative sentences are the single strongest tell
- Avoid starting successive sentences with the same construction ("The X is..." / "The Y is...")

**Phrases to never write**
- "more than people think" / "more than you'd expect"
- "most people don't know" / "what most people miss"
- "it's worth noting" / "it's important to remember"
- "let's dive in" / "let's break it down"
- "in today's world" / "in the modern era"
- "not just X, but Y"
- "the key is" / "the secret is"
- "game-changer" / "separates good from great"
- "isn't just about X — it's about Y"
- "delve," "tapestry," "landscape," "realm," "navigate" (as metaphor), "leverage" (as verb)
- "That said," as a paragraph opener
- Rhetorical questions as section transitions ("So what does this mean for you?")

**Hedging**
Don't pad definite statements with "generally," "typically," "often," "usually." Save hedges for genuine uncertainty. State fixed facts flatly.

## Structure

Match the format to what the content is doing. Bullets for anything the reader is comparing or choosing between: options, tradeoffs, cases with different outcomes. Prose for explanations, reasoning, anything with a because in it.

Don't bullet everything and don't prose everything. An all-bullet page loses the reasoning; an all-prose page buries the comparisons the reader is scanning for.

## Validate the claims

Every number, name, version, and factual detail is a claim until checked. Content gets copied between pages and the errors travel with it. If the user hands you a source document, its numbers are claims too, not facts.

Check against primary sources: official documentation, the actual code, specs, the vendor's own reference. Terminology gets the same treatment. Use what practitioners in that specific field say, not a term borrowed from an adjacent one, because the borrowed term survives a dictionary check and still reads as foreign to anyone who works there.

Treat "do X because Y" as two claims and check Y on its own. The advice is usually easy to verify and the reason attached to it isn't, which is exactly where invention happens. Confidence in a rule is not evidence for the rationale you attached to it.

When you can't validate something, say so and ask the user to confirm it. Don't ship it as fact, and don't quietly delete a claim you couldn't check either. If a reason is your own inference rather than something sourced, label it that way or give the advice without it.

A corrected number rarely stops at the number. Trace what it implies and rewrite the reasoning built on top of it.

## Final pass

Reread once with the tell list open, hunting only for tells. This catches what the drafting pass won't.

Run the deletion test on every list lead-in and every fragment standing alone as a sentence. These survive drafting because they read as transitions.

Then list anything still unvalidated and put it to the user.
