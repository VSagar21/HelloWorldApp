# Technical Context

Engineering-practice contract for code-writing agents in this project. Every implementation must respect what's written here. If a constraint conflicts with the user's request, flag the conflict — don't silently bypass it.

## Operating Context

Every later section of this contract is written for the Operating Context stated here.

- **Users:** Enate developers trialling the Factory end to end; they run the console app to confirm it prints `Hello World`.
- **Operating Scale:** A handful of internal users running it occasionally by hand; no concurrent load. One developer plus Factory agents, delivering a single Feature.
- **Where it runs:** Developer workstations on Windows, macOS and Linux, and the Linux CI agent; launched via `dotnet run` (or the built executable) from a terminal, interactively or headless in CI.
- **Credentials:** None.
- **Alongside it:** None.
- **Not the Product's concern:** Installing the .NET 10 SDK on workstations and CI agents; the CI pipeline itself (Azure DevOps, owned by whoever runs the Factory's pipelines); packaging and distribution — the app is not published, installed or deployed.
- **Last confirmed:** 2026-10-08

## Overriding Principles
1. **No third-party NuGet packages in the app project.** The base class library covers everything the Product does; any new runtime dependency requires an ADR.
2. **The output contract is exact.** The app writes `Hello World` followed by a single line feed (`\n`, never `\r\n`, on every OS) to stdout, writes nothing to stderr, and exits with code 0. Any change to stdout, stderr or the exit code requires an ADR.
3. **Builds treat warnings as errors, with nullable reference types enabled.** Every project sets `<TreatWarningsAsErrors>true</TreatWarningsAsErrors>` and `<Nullable>enable</Nullable>`.
4. **No breaking change to an Overriding Principle without an ADR.** These rules change only through a recorded decision in `docs/adr/`.

## Frameworks
- **.NET 10 (LTS), C#** — the runtime and language of the console app and its test project.

## 3rd-party tech
- **Azure DevOps** — Boards is the work-item tracker (`EnateInternal/HelloWorldApp`, per `.factory.yml`); Pipelines will host the `ci` status check once it is wired.
- **GitHub** — hosts the Repo (`VSagar21/HelloWorldApp`) and its pull requests.

## Packages in use
**App:** none — base class library only (Principle 1).
**Testing:** `MSTest` (the metapackage: `MSTest.TestFramework`, `MSTest.TestAdapter`, `Microsoft.NET.Test.Sdk`).

## Toolchain pinning
- Policy: exact-pinned.
- Pinned tools: the .NET SDK is pinned in `global.json` to an exact feature band (e.g. `10.0.100`) with `"rollForward": "latestPatch"` — patch releases are accepted, newer feature bands are not. NuGet package versions are pinned exactly in `Directory.Packages.props` (central package management), with `packages.lock.json` committed and `RestoreLockedMode` enabled in CI.
- Bumping: one PR changes `global.json` or `Directory.Packages.props`, regenerates the lock files, and must pass `ci`.

## Instrumentation
No logging framework, metrics or tracing. The Output, written with `Console.Out.Write`, is the Product's only output; writing anything else to stdout or stderr breaks the output contract (Principle 2).

## User Feedback Approach
The only channel is the terminal. The single output line is the entire user experience: no prompts, colours, banners or notifications. Agent-generated user-facing copy defaults to nothing beyond the output contract.

## Branching and Environments
- Model: trunk-based; `main` is protected — PR-only, linear history, squash-merge, no force-push.
- Branches: `main`; one `feature/<feature-id>-<slug>` per Feature; Stories on `story/<id>-<slug>` cut from their Feature branch; parentless work on `bug/…` or `story/…` cut from `main`. Full rules: `CONTRIBUTING.md`.
- Environments: none — nothing is deployed. CI (the `ci` check, once wired) is the only place the app runs outside workstations.
- Protected: `main`.

## Git Guardrails
- Never run `git push --force`, `git reset --hard`, `git clean -fd`, `git branch -D`, `git commit --no-verify`, or any direct push to `main` without explicit human confirmation.
- Hooks in place: `.githooks/pre-commit` blocks scratch and diff dump files; enable it once per clone with `git config core.hooksPath .githooks`. No Claude Code git-guardrail hooks are installed.
- **HITL delivery opens and closes its own board state — nothing else will.** No skill in the HITL pipeline (`/factory-to-stories`, `/factory-check-security-design`, `/factory-triage`) sets an assignee, an iteration, or an in-progress state at creation — they deliberately leave a HITL-tagged Story, Bug, or wholly-HITL Feature untouched for a human to clear. So: **before** making any change while hand-delivering one, set `System.AssignedTo` to yourself, `System.IterationPath` to the current sprint, and `System.State` to `Active`. For a Feature, delivery starts with its first Story; while it is planned, the planning skills set its state. **When** the delivering PR merges and the work item is done, set `System.State` to `Resolved`/`Closed` yourself before ending the session. Skipping either half leaves the item unassigned, out of sprint, and looking untouched for the entire time it's actually being worked.

## Testing & the ratchet
> Seeded from the Factory standard and trimmed by recorded decision (below). Full standard: <https://github.com/enateltd/factory-skills/blob/main/docs/testing-and-the-ratchet-standard.md> · the *why*: <https://github.com/enateltd/factory-skills/blob/main/docs/agentic-e2e-testing-principles.md>.

- **Tier 1 only.** One MSTest test runs the built app as a real subprocess and asserts stdout is exactly the bytes `Hello World\n`, stderr is empty, and the exit code is 0. It runs on every commit on the Linux CI agent; it is not run per commit on Windows or macOS (decision 2026-10-08).
- **Assert on observable behaviour, not internal state.**
- **The ratchet.** Any defect found outside the tests is fixed together with a Tier-1 test that would have caught it.
- **Trimmed (decision 2026-10-08):** the Factory standard's LLM, Tier 2, Tier 3 and disposable-substrate rules. This Product has no LLM, no external substrate and no deployment; revisit this trim if any of that changes.
