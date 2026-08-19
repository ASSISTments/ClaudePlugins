---
name: math-standard-unpacker
description: Breaks a math content standard (e.g. a CCSS, state, or district standard, pasted text or a standard code) into teachable, assessable sub-skills with an item map — so it can drive ASSISTments problem creation. Use this any time a teacher, coach, or curriculum writer pastes or names a math standard and asks to "unpack," "break down," "deconstruct," or "analyze" it, asks how to build a skill builder / problem set for it, gives a standard and asks for practice problems or an assessment, or — especially — asks for "a quiz" on a standard, a "mastery quiz," a "mastery check," or a "quick check" for a standard, even if they don't use the word "unpack" and even if no other breakdown has happened yet in the conversation. Do NOT use this for general pedagogy questions unrelated to a specific standard, or for creating problems on a topic that isn't tied to a standard.
---
 
# Math Standard Unpacker
 
Turns one math standard into a structured breakdown that a teacher can teach, assess, and — on request — turn directly into ASSISTments problems. This skill produces the breakdown only. Do not create or save ASSISTments problems unless the teacher separately asks for problems; when they do, use the item map this skill produces (see "Handing off to problem creation" below) together with the `ASSISTments Auth` tools.
 
## Inputs to collect
 
If the teacher just pastes a standard, proceed with sensible defaults (below) rather than asking questions, unless something is genuinely ambiguous (e.g. a standard code you can't resolve to exact wording — ask them to paste the text instead of guessing).
 
Useful but optional context, don't block on it: grade level/course, whether this is for initial instruction or review/intervention, and any local curriculum sequence. Use what's given; don't interrogate the teacher for it.
 
## The breakdown process
 
Work through these steps for every standard. Show your work in the output format below — don't just narrate the process.
 
### 1. Standard Unpacked
Identify the standard's main verb(s) (cognitive demand — solve, interpret, construct, compare, model, prove, etc.) and the noun phrase (content). The verb sets the rigor floor; don't undersell it later.
 
### 2. Split into skills
Most standards bundle 3-6 discrete sub-skills. Split into the smallest independently-teachable, independently-assessable pieces. Good skills are things you could imagine as separate lesson objectives. Watch for standards that silently bundle:
- a procedural case and a conceptual/justification case (e.g. "solve ... and explain why")
- a "nice numbers" case and a "messy numbers" case (whole numbers vs. rational coefficients, fractions vs. decimals)
- a general case and a special/edge case (no solution, infinite solutions, undefined, letter/parameter coefficients)
- a computation and an application/modeling/context case
Also watch for the reverse mistake: some clauses (e.g. "explain your reasoning") are modifiers that apply across every skill, not standalone skills in their own right. Test each candidate skill: could a student demonstrate it detached from the other skills? If not, it's a modifier — fold it into each relevant skill's "I can" statement instead of listing it as its own skill.
 
**Don't confuse a skill with an item-map sub-case.** When a standard names several distinct operations or processes as parallel verbs (e.g. "add, subtract, multiply, and divide"), each named operation is its own skill — a student can genuinely master addition while still struggling with division. But variations in operand type, order, or format *within* one operation (whole ÷ decimal vs. decimal ÷ whole; aligned vs. unaligned decimal places; regrouping vs. not) are the same skill tested through different problem forms — they belong in the item map's Problem Breakdown column (step 4), not as separate skills. The test: if two "skills" differ only in the numbers/format being plugged into the same operation and strategy, they're one skill with multiple sub-cases, not two skills.
 
Write each skill as a concrete, testable "I can" statement rather than a bare label — the statement itself should make it obvious what a problem testing it looks like, so there's no separate learning-target list to keep in sync.
 
**Before moving on, verify the skill list against the standard's actual text.** For each skill drafted, point to the specific word(s) in the standard that require it. If a skill's justification is "this is commonly tested" or "this is a natural extension" rather than a word or clause actually in the standard, cut it — that's manufacturing a case the standard doesn't ask for, even if it's good general practice. (Example: a computation standard that never mentions context, application, or word problems does not get an application skill just because most instructional treatments include one.) This check applies per-skill, not just to the item map's sub-cases in step 4 — the mistake to guard against is adding a whole extra skill, not just an extra problem case.
 
### 3. Note vertical alignment
Briefly state: **Prior Knowledge** — what prior skill(s) does this assume (1-2 bullets) — and **Builds Toward** — what does it lead to next (1-2 bullets). This flags likely prerequisite gaps and clarifies what "beyond this standard" would look like. This isn't just context — it's the direct input to the item map in step 4.
 
### 4. Build the item map
Use the vertical alignment to map the range of cases each skill's problems should sample — not just "routine vs. edge case." Ask: given what students already know (from vertical alignment), what's the actual new demand of this skill, and what distinct forms can that demand take? Build a table: skill | target item count | Problem Breakdown | suggested ASSISTments problem type(s) (CHOOSE_ONE, CHOOSE_N, FILL_IN, DRAG_DROP, SORT, OPEN_RESPONSE).
 
Default item counts: **4-6 problems per skill**, spread across the sub-cases identified above, unless the teacher specifies otherwise (fewer for a quick check, more for a full skill builder).
 
Always include any case the standard's own wording explicitly names (e.g. "no solution," "letter coefficients," "in context," "justify," "or"-clauses), even if vertical alignment alone wouldn't surface it. But don't manufacture a case that neither the standard's wording nor the vertical-alignment analysis implies — that overshoots the standard's actual demand.
 
## Output format
 
Present the full breakdown in this order, using headers, for every standard:
 
1. **Standard** (verbatim, plus source if given)
2. **Standard Unpacked** (1-2 lines)
3. **Skills** (numbered list, each written as an "I can" statement)
4. **Vertical alignment** (Prior Knowledge / Builds Toward — a few bullets)
5. **Item map** (table with a Problem Breakdown column, as in step 4)
Keep this conversational and scannable — headers and short lists, not a formal report. Don't create a file/artifact for this unless the teacher asks to save or share it.
 
After presenting the breakdown, don't ask a generic "want me to turn this into problems?" Ask the same two-path question as in "When a teacher asks for a quiz on a standard" below — "What type of problem set do you want to build?" with the Skill Practice and Standard Mastery Quiz options — even if the teacher never used the word "quiz."
 
## When a teacher asks for a quiz on a standard
 
Treat "give me a quiz on [standard]," "quiz on mastery of [standard]," "mastery check," and "quick check" as requests that route through this skill, not as a plain problem-generation request.
 
1. Run the breakdown first (steps 1-4 above) if it hasn't already been produced for this standard earlier in the conversation — don't skip straight to problems. If the breakdown is already sitting above in the conversation, don't redo it; just reference the existing skills.
2. Before drafting a single problem, ask the teacher to choose between two paths. Use `ask_user_input_v0` if that tool is available so it's a tap, not typing; otherwise ask in plain text. Ask "What type of problem set do you want to build?" and offer:
   - **Skill Practice (4-6 problems on skill)** — a full problem set per skill, following the item map as normal (~4-6 items per skill, spread across sub-cases). For building fluency / assigning practice.
   - **Standard Mastery Quiz (1 problem per skill, diagnostic)** — one short quiz with exactly **one item per skill**, so a quick look at results shows which specific sub-skill each student is stuck on.
3. Wait for their answer before drafting anything — don't default to one path.
**If they choose the mastery quiz:** for each skill, write exactly one item, and make it the *most demanding case* from that skill's item map — not the easiest version. Do not default to the easiest case for the quiz item: a diagnostic quiz built from easy items can't tell the teacher who is actually stuck, since the whole point is to surface where mastery breaks down, not to confirm what students can already do. The resulting quiz should have exactly as many items as there are skills.
 
**If they choose the practice set:** before drafting anything, ask which skill(s) to build practice problems for — offer the full skill list plus an "all skills" option (multi-select if `ask_user_input_v0` is available, otherwise ask in plain text). Wait for their answer, then proceed as in "Handing off to problem creation" below, using the item map's normal counts and case split for only the chosen skill(s).
 
## Handing off to problem creation
 
Once the teacher has approved which path (practice set or mastery quiz) and, for a practice set, which skill(s):
 
1. Draft the problems. For a practice set, match the case split from the item map, at the target item count already set (4-6 per skill by default). For a mastery quiz, draft exactly one item per skill at its most demanding case, per the instructions above.
2. Show the teacher the full draft — question text and all answer choices with the correct one(s) clearly marked — before saving anything.
3. Only after explicit approval, save each approved problem with `ASSISTments Auth:assistments_save_problem`, matching `problem_type` to what the item map suggested (fall back to CHOOSE_ONE for straightforward multiple choice, FILL_IN for a typed numeric/algebraic answer). Remember: FILL_IN/DRAG_DROP need `<ast-r>` placeholders inline in the question; CHOOSE_ONE/CHOOSE_N/SORT/OPEN_RESPONSE must not.
4. After each save, report the Problem ID and the preview link back to the teacher, per that tool's own instructions — don't skip that step.
5. Never call `assistments_save_problem` on a problem the teacher hasn't seen and approved.
