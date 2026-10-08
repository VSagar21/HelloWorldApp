# HelloWorldApp — Product Requirements

## Problem Statement

Enate developers trialling the SDLC Factory need a product small enough that every stage of the Factory (planning, Spec, Plan, Stories, AFK delivery, review gates) can be walked end to end without the product itself being the hard part. They need something whose correct behaviour is unambiguous and mechanically checkable, so that any friction they hit is friction in the Factory, not in the product.

## Solution

A .NET 10 console application. A Developer starts a Run; the Run writes the Output — the Greeting `Hello World` followed by a single line feed on standard output, nothing on standard error, exit code 0 — and exits. The Output is byte-identical on Windows, macOS and Linux. The Product takes no input and has nothing else to do.

## Requirements

1. As a Developer, I want to start a Run from a terminal, so that I can see the Product work.
2. As a Developer, I want a Run to print the Greeting `Hello World` exactly, so that its correctness is unambiguous.
3. As a Developer, I want the Greeting spelled with a capital H and a capital W, one space between the words and no punctuation, so that there is only one correct Output.
4. As a Developer, I want the Greeting followed by exactly one line feed, so that the terminal prompt returns on a fresh line.
5. As a Developer, I want the Output to end in a line feed and never in a carriage return and line feed pair, so that the Output is the same bytes on every operating system.
6. As a Developer, I want nothing written to standard error, so that a clean Run is visibly clean.
7. As a Developer, I want a Run to exit with code 0, so that scripts and CI treat it as a success.
8. As a Developer, I want a Run to produce exactly one Output and nothing more, so that there are no banners, prompts, colours or log lines to account for.
9. As a Developer, I want a Run to need no input, so that it never waits for me.
10. As a Developer, I want any command-line arguments I pass to be ignored, so that the Output never changes because of how the Run was started.
11. As a Developer, I want the same Output on Windows, macOS and Linux, so that one expectation holds everywhere.
12. As a Developer, I want to start a Run either through the .NET CLI or by running the built executable, so that both ways I normally run .NET code work.
13. As a Developer, I want a Run to work interactively and headless (for example in CI or with its output redirected to a file), so that the Output is the same whether or not a person is watching.
14. As a Developer, I want the Product to depend on nothing beyond the .NET base class library, so that a Run never fails because a package is missing or changed.
15. As a Developer, I want the .NET SDK and test packages pinned exactly, so that a build that passes today still passes on a later install day.
16. As a Developer, I want the build to treat warnings as errors with nullable reference types enabled, so that code quality issues block the build instead of accumulating.
17. As a Developer, I want an automated test that starts a real Run and checks the Output byte for byte, so that a regression in the Greeting, line ending, error stream or exit code is caught.
18. As a Developer, I want an automated test that starts a Run with arguments and checks the Output is unchanged, so that the ignore-arguments rule is enforced.
19. As a Developer, I want those tests to run on Linux in CI, so that a regression is caught without maintaining a multi-platform pipeline.
20. As a Developer, I want the tests to run on every commit, so that `main` never holds a Product whose Output is wrong.

## Implementation Decisions

- **The Output is written as the literal bytes of the Greeting plus a line feed, not via the platform line terminator.** The glossary fixes the Output as identical on every OS; the platform terminator would produce a carriage return and line feed on Windows.
- **Arguments are accepted and ignored, never validated.** A Run takes no input; rejecting arguments would introduce a second, failing kind of Run the domain does not have.
- **An unwritable standard output gets no special handling.** The Output is guaranteed only when standard output can be written; a broken stream is an environment failure, outside the Product's concern, and the runtime's default behaviour applies.
- **No third-party packages in the app; MSTest only in the test project.** Per the Technical Context's Overriding Principles and Packages in use.
- **Toolchain pinned exactly**: SDK feature band via the SDK version file with patch roll-forward only; package versions centrally managed with committed lock files and locked restore in CI. Per Technical Context, Toolchain pinning.
- **No ADRs.** The Product's decisions are captured in the glossary and the Technical Context; none meets the bar of hard to reverse, surprising and the result of a real trade-off.

## Testing Decisions

- **A good test checks only external behaviour**: what a Developer could observe from outside a Run — the bytes on standard output, the bytes on standard error and the exit code. Tests never reach into the app's internals.
- **Tier 1 only.** Each test starts the built app as a real subprocess. There are no unit tests and no mocks: the app has no internal module worth isolating, and the Testing & the ratchet standard requires real I/O at the seam.
- **Two cases:**
  1. A Run with no arguments: standard output is exactly the bytes `Hello World\n`, standard error is empty, exit code is 0.
  2. A Run with arbitrary arguments: the same three assertions hold.
- **Platform**: both cases run on every commit on the Linux CI agent. They are not run per commit on Windows or macOS (decision 2026-10-08).
- **The ratchet**: any defect found outside these tests is fixed together with a Tier-1 test that would have caught it.
- **Prior art**: none — this is the Repo's first code. These tests become the prior art.

## Out of Scope

- Any other Greeting text, localisation, or a configurable name.
- Any input: prompts, flags, options, environment variables or configuration files.
- Logging, metrics, tracing, colours or any output beyond the Output.
- Handling of an unwritable standard output.
- Packaging, publishing, installing or deploying the app.
- Installing the .NET SDK on workstations or CI agents, and building the CI pipeline itself.
- Tier 2 and Tier 3 testing (no LLM, no external substrate, no deployment).

## Further Notes

- This is a single-feature product: the PRD is effectively the whole Roadmap. One Feature work item covers it.
- The Product's purpose is to exercise the Factory. Friction found while delivering it is a signal about the Factory, to be raised separately, not a reason to grow the Product.
