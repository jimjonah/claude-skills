---
name: card-rules-rewrite
description: Rewrite or draft card game rules and strategy pages so they read like a knowledgeable player wrote them, open with a search-snippet-friendly definition, and avoid the structural tells that mark text as AI-generated. Use this whenever the user is writing, rewriting, editing, cleaning up, or shortening any page explaining how a card game works — rules, scoring, strategy, when-to-do-X guides — for euchre, cribbage, hearts, spades, pinochle, bridge, poker, or any other card game. Also use it when the user asks to make an existing rules page less obviously AI-written, easier to scan, or better for search.
---

# Card Game Rules Pages

You are writing for people who already play the game. They came to settle a specific question, not to learn the game from scratch. The whole job is answering that question quickly, correctly, and in language they'd actually use at the table.

## Verify before you write

Rules pages get copied from other rules pages, and errors propagate. Scoring numbers are the most common casualty.

Check the actual scoring, terminology, and rule details against real sources before writing a single sentence — official publishers (Bicycle, Hoyle), game federations, and established player communities. If the user hands you a source document, treat its numbers as claims to verify, not facts. Getting a scoring value wrong doesn't just introduce an error; it usually invalidates the strategy reasoning built on top of it.

Terminology has the same problem in reverse. Only use terms real players use. If you find yourself inventing a descriptive label because the concept doesn't have a standard name, say so explicitly rather than presenting your coinage as established jargon.

Watch for terms borrowed from an adjacent game. These are the hard ones, because they survive a dictionary check and still read as foreign at the table. "Ruff" is genuine euchre vocabulary and appears in the World Euchre Federation glossary, but euchre players say "trump in," and a euchre site's own glossary may not define "ruff" at all. When two terms are both defensible, use the one that game's players actually say, and check the site's existing glossary before introducing either.

## The opening sentence

The first sentence has to work as a standalone answer, because that's often all anyone reads — it's what Google pulls into a search snippet, and it's what a reader scanning for their answer sees before deciding whether to keep going.

Requirements:
- Directly answers the question in the page title
- Makes sense with zero surrounding context
- States the actual rule and the key number
- Leans on no term the reader might not know. A snippet has no page around it to define anything
- Runs 120-155 characters

That character range matters because Google truncates snippets by pixel width, roughly 920px on desktop and 680px on mobile. 120-155 characters of ordinary English survives both. Wide letters eat the budget faster, so if the sentence is heavy on capitals and wide characters, aim toward the low end.

Never open with throat-clearing, scene-setting, or a tease about what's coming.

Bad: "Going alone is one of the most exciting plays in euchre, and knowing when to attempt it separates good players from great ones."

Good: "Going alone in euchre means playing without your partner: take all five tricks alone and you score 4 points, double the 2 you score together."

Near miss: "Going alone in euchre means playing without your partner: 4 points for all five tricks solo versus 2 for a partnership march." Correct, well-formed, right length, and still wrong for the job. "March" is load-bearing, and a reader who is fuzzy on it cannot parse the number that follows.

## Voice

Write for someone who knows the basics. Don't define common terms, don't explain fundamentals, don't build up from first principles.

One exception: define the term the page's scoring turns on, inline, the first time it appears in the body. "Taking all five tricks is a **march**." costs six words, and it is the term readers are most likely to be shaky on precisely because it only comes up when points are being counted.

Show the conclusion and the reason, never the derivation. A player wants "only the march line changes, so the real question is whether your partner was going to help." They do not want the expected-value algebra that produced it. Do that work, then delete it.

Use the game's real shorthand rather than spelling everything out. Players read `R + L + A + K + Q of trump` instantly; "both bowers plus the ace, king, and queen of the trump suit" makes them parse a sentence to reach the same place. Every game has this vocabulary — euchre has bowers and off-aces, cribbage has nobs and heels, poker has outs and the nuts. Find and use it.

Plain tone throughout. No jokes, no enthusiasm, no "let's dive in," no motivational framing.

## Structure

Match the format to what the content is doing:

- **Bullets** for anything the reader is comparing or choosing between — hand types, score situations, options with different outcomes
- **Prose** for explanations, reasoning, and anything with a because in it

Don't bullet everything and don't prose everything. A page that's entirely bullets loses the reasoning; a page that's entirely prose buries the comparisons a player is trying to scan.

Before drafting, check the word count of the sibling pages you are not rewriting. A page that comes back at three times the length of everything around it reads as the odd one out no matter how good the prose is.

## Avoiding AI tells

These patterns are what make text read as machine-generated. They're stylistic habits, not rules of grammar, and the fix is deliberate unevenness.

**Structural**
- No default rule-of-three lists. Use however many items are actually true — two, five, seven
- Don't give sections matching or symmetrical header names ("Position matters" / "Score matters")
- Vary section length substantially. Some should be a paragraph, some four
- Don't end every section with a tidy summarizing sentence
- Don't mirror the source document's structure when rewriting

**Sentence-level**
- Limit em dashes heavily. Most can become a period, comma, or colon
- Skip the "**Thing** — explanation" bullet pattern applied uniformly down a list
- Vary sentence length. Consecutive medium-length declarative sentences are the single strongest tell
- Avoid starting successive sentences with the same construction ("The X is..." / "The Y is...")

**Phrases to never write**
- "more than people think" / "more than you'd expect"
- "most people don't know" / "what most players miss"
- "it's worth noting" / "it's important to remember"
- "let's dive in" / "let's break it down"
- "in today's world" / "in the modern game"
- "not just X, but Y"
- "the key is" / "the secret is"
- "game-changer" / "separates good from great"
- "isn't just about X — it's about Y"
- "delve," "tapestry," "landscape," "realm," "navigate" (as metaphor)
- "That said," as a paragraph opener
- Rhetorical questions as section transitions ("So what does this mean for your game?")

**Hedging**
Don't pad claims with "generally," "typically," "often," "usually" when the rule is actually definite. Save hedges for genuine uncertainty — where a card lands, how an opponent plays. State fixed rules flatly.

## Working method

1. Verify every scoring number and rule detail against real sources
2. Draft the opening sentence first and count its characters
3. Write the body, choosing bullets or prose per section based on what that section does
4. Reread specifically hunting the tells above — this pass catches things the drafting pass won't
5. Check that the reasoning still holds if you corrected any numbers in step 1
6. Verify the because-clauses, both at draft and after any edit. Confidence in a rule is not evidence for the reason you attached to it

## When corrections change the argument

If verification turns up a wrong number, don't just swap the number. Scoring errors usually propagate into the strategy advice — a page built on "the bonus is one extra point" reaches the opposite conclusion from one built on "the bonus doubles your score." Trace what the corrected value implies and rewrite the reasoning to match. Say plainly what changed and why.

## Reasons need sourcing separately from the advice

The advice on a strategy page is easy to check. The reason attached to it is not, and that is where invention happens.

"Hold your off-suit ace back" is standard euchre defense and sources agree on it. "Its job is to stop the loner on the trick where they lead that suit" is a rationale that sounds right, was never checked, and turned out to be the weaker of two available explanations. The real mechanism was that a loner holds three or four trump out of five cards and is therefore short in a side suit by construction, so an ace led early gets trumped. A verification pass that checks scoring, mechanics, and terminology sails straight past this, because those all have something concrete to check against and a causal claim does not.

Treat "do X because Y" as two claims and source Y separately from X. If Y cannot be sourced, either state the advice without the reason or say the reasoning is your own.

The same move manufactures entire rules. Two correct pieces of defense advice, lead your longest side suit and hold your off-suit ace back, compressed into "lead your longest side suit, ideally one with no ace in it," which tells the reader to abandon the length advantage whenever their long suit contains an ace. Nobody says that. Separately, "do not both abandon the same suit" arrived with a reason that does not bear on it at all: a loner's side-card count governs which suits you can attack, not which ones each defender should guard when discarding.

Three habits catch these:

- When one sentence carries two rules joined by "and," "ideally," or "preferably," confirm the combination is sourceable, not just each half.
- When a because-clause explains a rule you are confident in, check the clause anyway.
- When cutting for length, delete whole clauses and sentences rather than fusing them. The because is the first thing to go under compression and the last thing worth losing.
