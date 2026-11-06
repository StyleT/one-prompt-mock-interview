You are going to build a practice environment that emulates a real company's coding interview, for me to run solo under time pressure. Work in five phases, in order. Do not skip a phase and do not start a later phase before the earlier one is done.

## PHASE 0 — INTAKE (do this before anything else)

I need to have told you, explicitly, all of the following. If any item is missing from this prompt or from the conversation so far, your FIRST and ONLY response is one short message asking for the missing items. Generate nothing until I answer.

- `{{COMPANY}}`, `{{ROLE}}`, `{{LEVEL}}` — the target company, role and level, e.g. "Meta, Product Generalist SWE (full stack), IC5". A title such as "Senior" or "Staff" counts as the level when that is how the company levels.
- `{{LANGUAGE}}` — the programming language I will use. Optionally a version and a test runner; otherwise use the language's standard ones (see the table in Phase 3).
- Optional: session length (default 60 minutes), and anything I already know about the format.

Do not guess the language from the files in the directory, from my earlier projects, or from the company. Do not default to any language. Example answers that appear in documentation around this prompt are not my answers. If I named all of the above already, do not ask; go straight to Phase 1.

## PHASE 1 — FORMAT RESEARCH

Search the web for the current state of the coding interview at `{{COMPANY}}` for `{{ROLE}}` at `{{LEVEL}}`. Use several queries of this shape, adapted to the company:

- "current state of the coding interview at {{COMPANY}} 2026"
- "{{COMPANY}} {{ROLE}} {{LEVEL}} interview process"
- "top 10 coding challenge interview questions at {{COMPANY}}"
- "{{COMPANY}} AI-enabled coding interview" / "{{COMPANY}} AI-assisted coding round"
- "what does the {{COMPANY}} coding round actually ask you to do with the codebase"

Lists of "top questions" tell you the recurring themes and the difficulty bar; take only that from them.

From what you find, establish the FORMAT FACTS for this interview:

- duration of the coding round
- one multi-part thematic problem, or several independent problems
- whether an AI assistant is available, and whether its use is optional, mandatory, or rated
- whether I start from a blank file or from existing code I did not write
- which dimensions are rated (if the company publishes or candidates widely report them)
- recurring problem themes and the typical level of difficulty

Report the FORMAT FACTS in chat, each with its source and a confidence note (widely reported / single source / inferred). Where the web is silent, fall back to the DEFAULTS in Phase 3 and say so explicitly. Use question *themes* only. Never reproduce an actual leaked interview question, and never build the challenge around a public LeetCode problem.

Then present the FORMAT FACTS together with the proposed file tree for the challenge, and WAIT for my confirmation before building anything.

## PHASE 2 — ENVIRONMENT RESET (only if there is something to reset)

Inspect the current working directory first.

- If it is empty, or contains only environment configuration (see below), say so in one line and skip the rest of this phase. This is the usual case.
- Otherwise, we are replacing a previous challenge, and everything that belongs to it has to go. Before you delete anything:
    - The practice directory is the CURRENT WORKING DIRECTORY. Do not ask me for a path and do not infer one from anywhere else.
    - Print the resolved absolute path of the current directory plus a full recursive file listing, and WAIT for my explicit confirmation before removing anything.
    - Delete only things inside the current directory. Never touch anything above it.
    - STOP and ask me instead if the current directory turns out to be a home directory, a source repository root, or a shared or mounted workspace.
    - Do not run a recursive delete against any path you have not echoed back to me and had confirmed.
    - Leave the directory itself in place; just empty it.

PRESERVE ENVIRONMENT CONFIGURATION — DO NOT DELETE IT

- Environment configuration is anything that configures the practice *environment* rather than the *challenge*: editor or IDE project settings, an interview platform's project config, a devcontainer, toolchain version pins (`.nvmrc`, `.python-version`, `.tool-versions`, `.editorconfig`, and the like).
- Keep every such file or directory in place, then UPDATE it so it matches the new challenge: entry or main file, run command, test command, language, and the file manifest. If it is a directory, preserve the directory and update its contents rather than recreating it.
- If anything inside it references files from the previous challenge, fix those references. Nothing in it may point at a file that no longer exists.
- Tell me explicitly what you changed and why.
- If no environment configuration exists, create none. Everything else in the directory goes.

Once the directory is empty apart from environment configuration, build the new challenge per Phase 3, and verify that it actually runs through whatever run and test commands the configuration and `Instruction.md` define.

## PHASE 3 — BUILD

You are building a practice environment that emulates `{{COMPANY}}`'s coding interview for `{{ROLE}}` at `{{LEVEL}}`, as established in Phase 1. I am a candidate preparing for that loop. Build the artifact, do NOT interview me yet, and do NOT reveal solutions in any file I am meant to read.

### DEFAULTS — apply unless Phase 1 research says otherwise

- 60 minutes, ONE multi-part thematic question with checkpoints of rising difficulty.
- I start from EXISTING code I did not write: 100–500 lines across several files.
- Code EXECUTES. Checkpoints are concrete and machine-verifiable: "this unit test now passes", "this benchmark is 2x faster", "score improved by X".
- A data file is normal: between 10,000 and 1,000,000 entries.
- An AI assistant is available to me and its use is MANDATORY and RATED.

### LANGUAGE

- Language: `{{LANGUAGE}}`, written idiomatically, with the language's usual strictness settings (strict TypeScript, type-checked Python, warnings-as-errors where that is normal, and so on) and its standard test runner unless I named one:

  | Language | Default test runner |
  |---|---|
  | TypeScript / JavaScript (Node) | Jest or Vitest |
  | Python | pytest |
  | Java / Kotlin | JUnit 5 |
  | Go | `go test` |
  | Rust | `cargo test` |
  | C# | xUnit |
  | C++ | GoogleTest or Catch2 |
  | Ruby | RSpec or minitest |
  | Swift | XCTest |

- Configure the strictness tooling (compiler flags, type-checker settings) in the project; run it in Phase 4 only if it is already installed. Do not add third-party dependencies beyond the test runner.
- Run, test and benchmark commands go through the language's native tooling (package scripts, Makefile, pyproject, cargo, go, and so on). Do not assume any particular IDE, editor or hosted platform. `Instruction.md` states the exact commands.

### THREE-BLOCK STRUCTURE (non-negotiable)

- BLOCK I — WARM-UP, exitable in 10–20 min, deliberately low-signal:
    - one deliberately PLANTED BUG causing exactly one failing test
    - one trivially missing test case for me to add
    - one simple data question (e.g. a mean, a count, a max)
    - each of these should be solvable with a single good AI prompt
    - files I should NOT need to touch in Blocks I and II must be split out and annotated at the top:
      `// THIS WORKS EXACTLY AS YOU EXPECT - you should not need to modify this file in Blocks I and II` (use the language's comment syntax). Block III follow-ups may require changing them; that is allowed and Instruction.md says so.
    - minimize boilerplate: file reading and parsing is pre-written for me
- BLOCK II — PRIMARY CHALLENGE, where most of the signal lives:
    - a naive or brute-force approach must be reachable and must PASS an early checkpoint
    - then a trigger must EXPOSE its inadequacy: a larger data file, an extra test, an uncommented function, or a visibly poor score
    - the real checkpoint requires significant greenfield code AND a better algorithm
    - design it so NO SINGLE ALGORITHM CLEARLY DOMINATES; there must be real trade-offs. You can only know this by measuring: implement at least two materially different approaches during ground-truth verification and report the spread in chat.
    - provide a benchmark harness printing a hard number I must beat. Measure the baseline live on a sample and extrapolate rather than hard-coding a machine-specific number; it is fine that the benchmark visibly contains the brute-force comparison, because brute force is the approach I am meant to outgrow.
- BLOCK III — FOLLOW-UPS, optional, for if I finish early. At least four of:
    - increase data size to ruthlessly expose a performance deficiency (only if the larger file can be shipped at a reasonable size, roughly under 50 MB; the generator will be gone)
    - change a rule so my earlier approach is no longer optimal
    - change a requirement forcing a refactor
    - make me implement an approach I likely discarded
    - tighten tests to "only necessary and sufficient" cases
    - add a memory ceiling

### GROUND TRUTH — VERIFY IT, THEN DO NOT SHIP IT

You must still establish real, known-correct answers for every checkpoint, by actually writing and running code. But NOTHING describing a solution may survive anywhere on disk: not in the practice directory, not in a sibling directory, not anywhere.

How to square that:

- Write a reference implementation, run it, confirm the checkpoints are achievable and the numbers are real. Then DELETE it.
- Scoring data is allowed and is not a solution: label files, expected outputs and the like that a checkpoint needs in order to compute recall, precision, a score or an exact match. What may not survive is anything that shows how that data was derived.
- The only other thing that survives is the TARGET: the number I have to beat or reach. Targets are candidate-visible and belong in `Instruction.md`, the benchmark output, or a test assertion, e.g. "beat the baseline wall-clock by 3x", "reach F1 >= 0.92", "improve total score by at least 18%". A target is not a solution.
- Report in your handover message, in chat only, that you verified each target and what you measured. Do not write that report to a file.
- If a target turns out not to be achievable, change the target. Never ship an impossible checkpoint.

### DELIVERABLES — the practice directory contains ONLY what the candidate sees

1. `/Instruction.md` — ALL candidate-facing instructions, in one file, visible from minute 0. Include ALL THREE BLOCKS up front with their checkpoints and targets, plus the exact run, test and benchmark commands. I am running this solo with no interviewer to reveal the next part, so there is no progressive disclosure; I read the whole thing at the start.
    CRITICAL DISTINCTION: state fully WHAT each block requires; never hint at HOW.
    - Allowed: the scenario, the rules, the required behavior, the checkpoint for each block, the numeric target, the constraints, which files matter.
    - Not allowed: the technique, the data structure, the algorithm family, the complexity class of the intended answer, why the naive approach fails, where the planted bug is, or any phrasing like "consider using X" or "a hash map would help here".
    - Block I should read as a normal warm-up task list, not as "here is a planted bug".
2. Starter code, 100–500 lines, idiomatic `{{LANGUAGE}}`, realistically imperfect: plausible naming, a little dead code, light or absent comments. Exactly one planted bug.
3. The generated data file(s), including the larger "Block II trigger" dataset.
4. Test suite: several passing, exactly one failing (the planted bug), plus skipped or stubbed tests for the later checkpoints. "Exactly one failing" must hold under any test order, so a state-leaking bug may be exercised by only one test. Instruction.md tells me exactly how to enable the later checkpoints' tests (a named marker or a single flag).
5. A benchmark script printing the baseline number, my current number, and the target.
6. Environment configuration files, if any existed before: updated, never recreated, pointing at this challenge's entry file, run command, test command and file manifest.

That is the complete file list. There is no solutions file, no rubric file, no interviewer file, no answers directory, and no generator script. If you are about to create a file that a candidate would not be handed, do not create it.

### POST-GENERATION CLEANUP (do this before you hand over)

- Run the data generator, keep its OUTPUT, then DELETE the generator script and any other scaffolding you wrote to build this challenge: seeding scripts, ground-truth computation scripts, scratch files, one-off verification scripts, notes-to-self, logs, temp directories, and any package or build scripts that invoke them.
- The practice directory must end up containing only what a candidate would plausibly be handed: the instructions, the starter code, the data, the tests, the benchmark, and any pre-existing environment configuration.
- Nothing left behind may reveal how the data was constructed.

### NO CLUES — remove anything that could help the candidate

Audit the whole practice directory before handing over and strip:

- solution files, reference implementations, answer keys, rubrics, ground-truth derivations, ANYWHERE on disk, including outside the practice directory
- the generator and any script that derives the correct answer
- TODO / FIXME / HACK / "bug here" / "this is wrong" style comments, and any comment that explains or draws attention to the planted bug
- commented-out correct implementations or earlier working versions
- giveaway names: no variable, function, file, test or directory called anything like `buggy`, `broken`, `naive`, `slow`, `wrong`, `optimal`, `fast`, `fixed`, `expectedAnswer`, `correct`, `planted`, `block2`, `block_ii`, `warmup`, `trigger`, `hint`
- test names that state the algorithm or the fix rather than the behavior
- docstrings or READMEs describing the intended optimal approach or complexity
- git history: if you initialize a repo, use ONE commit. No commit messages describing the bug, the blocks, or the solution. Prefer no repo at all.
- any file whose mere existence signals where to look

The audit covers identifiers, filenames, directory names, test names, comments, docstrings, commit messages and Instruction.md. Ordinary prose that happens to use a word like "correct" is fine; a name or comment that points at the bug or the technique is not. Generated data is out of scope for the grep, but do not let generated vocabularies contain the blacklisted words either.

### THE FIVE RATED FOCUS AREAS — design the challenge so all five are exercisable

These are the default rubric. If Phase 1 surfaced the company's own published or widely reported rating dimensions, use those instead and say so in chat. Do not write them into any file; use them to shape the problem, so that a candidate working through it naturally has the opportunity to demonstrate every one.

1. Problem Solving — clarifies constraints and assumptions, decomposes, generates solutions and trade-offs, judges feasibility.
2. Code Development and Understanding — navigates an existing codebase, writes code that integrates and is maintainable, refactors, optimizes.
3. Debugging and Verification — isolates and resolves errors, writes meaningful tests, verifies that AI-written code is actually correct.
4. Technical Communication — reasoning is logical and concise, articulates ideas, incorporates feedback, explains AI-usage rationale.
5. AI Usage — uses the assistant and effective prompts, AND evaluates the strengths, limitations, errors and quality issues of its output before integrating it. Skepticism counts as much as fluency.

### LANGUAGE-IDIOMATIC AI FAILURE MODE

Plant at least one failure mode that is specifically idiomatic to `{{LANGUAGE}}` and that AI assistants tend to get confidently wrong. It may BE the planted bug, or it may be a latent issue in code that currently passes its tests and only bites at Block II or III volume. It must not add a second failing test. Pick from the list for the language, or choose an equally idiomatic one and say in chat which you chose.

- TypeScript / JavaScript: an unawaited promise, floating async inside a loop, `Promise.all` vs sequential `await` on a hot path, a Map-vs-object performance cliff, numeric precision loss during aggregation, array copying inside a loop, loading a large file into memory instead of streaming it.
- Python: a mutable default argument, float accumulation error, O(n²) string or list building in a loop, loading a whole file instead of iterating it, GIL-bound threads for CPU-bound work, late-binding closures in a loop, relying on dict ordering where it is not guaranteed.
- Java / Kotlin: `==` on boxed numbers, string concatenation in a hot loop, a poor `hashCode` causing collisions, autoboxing in hot paths, silent `int` overflow, shared mutable state across threads.
- Go: loop-variable capture in goroutines or closures, slice aliasing through `append`, writing to a nil map, an unbuffered-channel deadlock, `defer` inside a loop, integer division where a float was meant.
- Rust: `clone()` in a hot loop, `unwrap()` on a recoverable error, `String` allocations where `&str` would do, overflow that differs between debug and release builds.
- C#: `async void`, blocking on `.Result`, multiple enumeration of a LINQ query, unintended struct copies.

### OUT OF SCOPE — do not produce these

- Interviewer instructions. No INTERVIEWER.md, no interview script, no probe question list, no hint ladder, no block-by-block interviewer walkthrough. I am running these solo.
- Any coaching material aimed at me inside the practice directory.

### HARD CONSTRAINTS

- Do not make the whole problem one-shottable by a frontier model. Block II must require my judgment, not just a prompt.
- Do not use a well-known puzzle or a public LeetCode problem.
- Do not put the answer in comments, commit messages, variable names or filenames.
- Everything must actually run. Verify it yourself before handing it to me.

## PHASE 4 — VERIFY THE COMMANDS ACTUALLY WORK (hard requirement)

Do not claim done until you have:

- listed every run, test and benchmark command defined in `Instruction.md` and in any environment configuration
- EXECUTED each one in the practice directory
- confirmed the run command runs the challenge and the test command runs the test suite
- confirmed the test run shows exactly the expected pass/fail split, with the one planted-bug test failing and no unexpected failures
- confirmed every file the environment configuration references exists and nothing it references is from a previous challenge (skip this if no environment configuration exists)

Paste the actual terminal output of each command into your handover message. If a command fails, fix the configuration or the code and re-run. Do not hand me a broken environment.

Start with Phase 0.
