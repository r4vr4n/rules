# Simplified Technical English (STE) Rules for AI Coding Agents

## Purpose

Use Simplified Technical English (STE) principles for **all human-readable English that you generate, modify, or propose in this repository**, unless an exception below applies.

The goal is to make technical communication:

- clear
- direct
- consistent
- concise
- unambiguous
- easy to translate and review

These rules are based on the ASD-STE100 overview provided to the agent. They are a **project writing policy**, not a replacement for the complete ASD-STE100 specification or its full dictionary.

---

## 1. Mandatory Behavior

When generating or editing human-readable technical text:

1. **Prefer simple, direct wording.**
2. **Use one clear term for the same concept.**
3. **Use the same term consistently throughout a feature, file, or document.**
4. **Prefer active voice.**
5. **Prefer simple verb forms.**
6. **Avoid unnecessary formal, vague, or indirect wording.**
7. **Do not make text "simpler" by removing technical precision.**
8. **When an instruction is required, tell the user/operator exactly what to do.**
9. **Keep instructions separate when they represent separate actions.**
10. **Preserve the original technical meaning. Never trade accuracy for brevity.**

---

## 2. Scope

Apply these rules to:

- documentation
- README files
- technical guides
- troubleshooting instructions
- setup instructions
- migration guides
- release notes
- user-facing UI copy
- tooltips and helper text
- error and warning messages
- code comments
- test descriptions
- generated explanations
- agent-generated implementation notes

Do **not** rewrite the following merely to satisfy STE:

- source-code identifiers
- API names
- library names
- product names
- official feature names
- database/schema names
- CLI flags
- environment-variable names
- URLs
- file paths
- exact protocol or wire-format text
- legal text that must remain exact
- security-sensitive or externally mandated wording
- quoted user-provided text
- third-party documentation that is being reproduced or cited

When exact wording must be preserved, keep it unchanged and write any surrounding explanation using STE.

---

# 3. Vocabulary Rules

## 3.1 Prefer the simplest precise word

Avoid unnecessarily formal alternatives.

Use:

| Avoid | Prefer |
|---|---|
| commence | START |
| ensure | MAKE SURE |
| prior to | BEFORE |
| replenish | FILL |
| utilize | USE |
| approximately | ABOUT |
| in order to | TO |

Examples:

**Avoid**
> Utilize the configuration file in order to commence the process.

**Prefer**
> USE the configuration file TO START the process.

---

## 3.2 Do not use a word just because it is grammatically correct

A word can be valid English and still be inappropriate for controlled technical writing.

Before using an uncommon synonym, ask:

> Is there a simpler, more direct word that preserves the exact meaning?

Prefer the simpler word when the meaning is unchanged.

---

## 3.3 Treat approved vocabulary as meaning-specific

Do not assume that an approved word can be used in every normal-English sense.

A word may be approved for one:

- meaning
- part of speech

but not another.

Example from the provided STE overview:

- `CLOSE` as a verb = approved
- `close` as an adjective meaning `near` = not approved

Therefore prefer:

> Put the tool NEAR the panel.

rather than:

> Put the tool close to the panel.

---

## 3.4 Do not invent substitute terminology

Do not replace precise technical terminology merely because it sounds complex.

Technical nouns and verbs are allowed when they are necessary and established for the domain.

Prefer:

> REMOVE the Hydraulic Power Transfer Unit.

not:

> REMOVE the hydraulic power thing.

Simplify the language **around** technical terminology, not the technical terminology itself.

---

# 4. Verb Rules

## 4.1 Prefer these forms

Use:

- imperative for procedures
- simple present for system behavior and descriptions
- simple past when describing completed events
- simple future when describing future behavior
- infinitive where required by sentence structure
- past participles as adjectives where appropriate

Examples:

> CLOSE the valve.

> The valve closes.

> The valve closed.

> The valve will close.

> Turn the knob TO CLOSE the valve.

---

## 4.2 Avoid progressive forms when a simpler form expresses the same meaning

Avoid:

> The valve is closing.

Prefer a form that expresses the intended meaning directly, such as:

> The valve closes.

or, when the sentence is an instruction:

> CLOSE the valve.

Do not interpret this rule as banning all `-ing` words. Technical names such as `landing gear` are allowed when they are established terminology.

---

## 4.3 Avoid unnecessary perfect constructions

Avoid unnecessarily complex forms such as:

> The valve has closed.

Prefer a simpler construction when it communicates the same technical meaning.

Do not simplify when doing so would change the timing or technical meaning.

---

## 4.4 Procedures should use active imperative language

Prefer:

> REMOVE the panel.

Avoid:

> The panel must be removed.

Avoid passive procedure wording unless there is a specific reason that passive voice is required for the meaning.

---

# 5. Procedure Rules

When writing a procedure, optimize for direct execution.

## 5.1 Use one instruction per sentence

Prefer:

> OPEN the valve.

> CHECK the pressure.

> TEST the circuit.

Avoid:

> Open the valve, check the pressure, and test the circuit.

Separate actions unless the actions are genuinely simultaneous and the combined instruction is clear.

---

## 5.2 Start with the action

Prefer:

> REMOVE the cover.

> CONNECT the cable.

> CHECK the connector.

Avoid indirect openings such as:

> It is necessary to remove the cover.

> The operator should make sure that the cover is removed.

---

## 5.3 Make the object explicit

Avoid vague instructions:

> CHECK it.

> Do this first.

> Make sure it is correct.

Prefer:

> CHECK the pressure indicator.

> REMOVE the access panel.

> MAKE SURE that the switch is OFF.

Use pronouns only when the referenced object is completely unambiguous.

---

## 5.4 Procedure sentence length

Target a maximum of **20 words for a procedural sentence**.

If a procedure becomes long:

1. identify separate actions
2. split the actions into separate steps
3. move explanations or conditions into separate sentences when needed
4. use a vertical list for complex procedures

Do not split a sentence in a way that removes necessary conditions or changes the technical meaning.

---

# 6. Descriptive Writing Rules

Use descriptive writing to explain system behavior, components, relationships, or conditions.

## 6.1 Prefer simple present tense for stable behavior

Examples:

> The pump supplies fuel to the engine.

> The switch controls the pump.

> The sensor detects pressure.

---

## 6.2 Descriptive sentence length

Target a maximum of **25 words for a descriptive sentence**.

When a sentence approaches or exceeds this limit, check whether it contains:

- multiple topics
- multiple actions
- unnecessary qualifiers
- nested clauses
- unnecessary background information

Rewrite or split it when possible.

---

## 6.3 Descriptive paragraph length

Target a maximum of **6 sentences per descriptive paragraph**.

Use **one topic per paragraph**.

Do not combine unrelated subjects only because they are part of the same system.

---

# 7. Noun Cluster Rules

Avoid long chains of nouns and modifiers.

Target a maximum of **3 words in a noun cluster**.

Avoid patterns such as:

> engine fuel pressure control system configuration

Prefer a clearer construction such as:

> fuel pressure control system

or:

> The system controls engine fuel pressure.

When relationships are difficult to parse, turn the noun cluster into a sentence.

---

# 8. Articles and Explicit References

Do not omit required words such as:

- `the`
- `a`
- `an`
- `this`
- `that`

Avoid telegraphic wording such as:

> Remove panel and disconnect cable.

Prefer:

> REMOVE the panel.

> DISCONNECT the cable.

Explicit references reduce ambiguity.

---

# 9. Consistency Rules

## 9.1 One concept, one term

Once a component or concept has an established name, use that name consistently.

Do not alternate between:

> access panel

> cover

> access plate

unless those are technically different objects.

---

## 9.2 Do not use synonyms just for stylistic variety

Normal prose often varies wording to avoid repetition.

Do **not** do this in technical writing when the words refer to the same concept.

Prefer repetition of the correct technical term over stylistic variation.

---

# 10. Safety Instructions

Safety text must be direct and explicit.

Use:

- `WARNING` when there is a risk of injury or death
- `CAUTION` when there is a risk of equipment/property damage

Use this general structure:

> WARNING: [clear action or condition]. [risk or result].

Example:

> WARNING: Do not touch the brake unit until it is cool. Hot parts can cause injury.

A safety statement should make clear:

1. what the reader must do or must not do
2. what can happen if the instruction is not followed

Do not use vague warnings such as:

> WARNING: Hot brake unit.

when a clear action can be given.

---

# 11. Punctuation and Formatting

Use punctuation to make sentence boundaries and relationships clear.

Prefer:

- short sentences
- clear step boundaries
- numbered or vertical lists for complex procedures
- consistent capitalization
- explicit references

Do not use punctuation as a substitute for clear sentence structure.

---

# 12. Agent-Specific Writing Process

Before returning generated technical prose, perform this internal review:

### Meaning
- Is the technical meaning correct?
- Did simplification remove or alter any required detail?

### Vocabulary
- Did I choose the simplest precise wording?
- Did I use any unnecessarily formal synonym?
- Did I use a term in the wrong sense or part of speech?

### Procedure
- Is the instruction direct?
- Is it imperative and active where appropriate?
- Is there only one instruction per sentence?
- Is the sentence within the 20-word target?

### Description
- Is the description direct?
- Is simple present appropriate?
- Is the sentence within the 25-word target?
- Does the paragraph stay on one topic and within 6 sentences?

### Structure
- Are noun clusters 3 words or fewer where practical?
- Are articles and references explicit?
- Are technical terms used consistently?

### Safety
- Is WARNING used for personnel risk?
- Is CAUTION used for equipment/property risk?
- Does the warning state the required action/condition and the risk?

---

# 13. Required Rewrite Behavior

When an agent encounters text that violates these rules:

1. Preserve the technical meaning.
2. Rewrite the surrounding language using STE principles.
3. Prefer direct wording over stylistic wording.
4. Split multi-action procedures.
5. Replace unnecessary formal synonyms with simpler precise terms.
6. Do not alter technical identifiers or exact strings.
7. Do not silently change terminology that has an established project meaning.

---

# 14. Examples

## Example A — Formal wording

**Before**
> Prior to commencing the operation, ensure that the hydraulic reservoir is replenished.

**After**
> BEFORE you START the operation, MAKE SURE that the hydraulic reservoir is full.

---

## Example B — Unnecessary formal vocabulary

**Before**
> Utilize the configuration file in order to initiate the build.

**After**
> USE the configuration file TO START the build.

---

## Example C — Multi-action procedure

**Before**
> Remove the panel, disconnect the cable, inspect the connector, and clean the terminals.

**After**

> REMOVE the panel.

> DISCONNECT the cable.

> INSPECT the connector.

> CLEAN the terminals.

---

## Example D — Vague instruction

**Before**
> Check it and make sure it is correct.

**After**
> CHECK the pressure indicator.

> MAKE SURE that the pressure is within the specified range.

---

## Example E — Inconsistent terminology

**Before**
> Open the access panel. Remove the cover. Reinstall the access plate.

**Problem**
- access panel
- cover
- access plate

These may refer to the same component.

**After**
> OPEN the access panel.

> REMOVE the access panel.

> INSTALL the access panel.

Use a different term only when it identifies a different technical object.

---

# 15. Important Exceptions

STE is a writing constraint, not permission to alter technical facts.

Do not:

- change numeric values
- change units
- change thresholds
- change component names
- change API contracts
- change configuration keys
- change commands
- change error codes
- change legal/security wording
- change quoted text
- invent an "approved" synonym when the correct term is unknown

When the exact technical meaning is uncertain, preserve the meaning and flag the ambiguity rather than guessing.

---

# 16. Priority Order

When rules conflict, use this priority:

1. **Technical correctness**
2. **Safety**
3. **Exact project/domain terminology**
4. **Clarity and unambiguity**
5. **STE vocabulary and grammar constraints**
6. **Conciseness**
7. **Stylistic preference**

Never sacrifice technical correctness to satisfy a language rule.

---

# 17. Final Agent Directive

**Always apply these STE principles to newly generated human-readable technical English in this repository.**

Before finalizing text, ask:

> **Can a technical reader understand this sentence immediately, without guessing what a word, reference, condition, or action means?**

If not, rewrite it.

The desired result is:

**simple language + precise technical terminology + direct instructions + consistent vocabulary + minimal ambiguity.**
