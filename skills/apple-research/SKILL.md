---
name: apple-research
description: Use before planning or implementing any feature that uses Apple frameworks or APIs (iOS, iPadOS, macOS, watchOS, tvOS, visionOS). Researches official Apple documentation, WWDC sessions, the Human Interface Guidelines, and release notes, and writes a sourced research report. Not for quick API questions.
---

# Apple Research Skill

## Objective

Do technical research and guidelines analysis for Apple platform features (iOS, iPadOS, macOS, watchOS, tvOS, visionOS). Write a standalone, evidence-backed research report to `.docs/<feature>-research.md`, so the topic is fully understood before any feature planning or coding starts.

## Core Boundaries & Workflow

1. **Research only.** Do not produce an implementation plan, architecture roadmap, or code in this phase. Search, learn, and document the topic. Short API snippets quoted from Apple sources to show signatures or usage are fine. Solution code is not.
2. **No code modifications.** Do not edit application code, change dependencies, or alter project settings. The only file this skill writes is `.docs/<feature>-research.md`, where `<feature>` is a short lowercase kebab-case name for the feature (for example `.docs/foundation-models-research.md`). Each feature gets its own file, so several researches can live side by side and be picked up later. If a file with that name already exists, ask the user whether to overwrite it, append a new section, or use a different name.
3. **Separate steps.** The user reviews the research document first. Feature planning and implementation happen later, in separate prompts, after the user approves the research. End the turn once the document is delivered. Do not move on to planning.

## Step 1: Find Starting Resources

- Check the user's prompt for attached links, WWDC session references, documentation URLs, articles, or sample project files.
- **If starting resources are provided:** use them as the research anchor. Read them fully, then follow their relevant references (related symbols, "See Also" sections, linked sessions, sample code) to cover the topic more broadly. Non-Apple links the user provides (blog posts, articles, repositories) are valid starting points: read them, then confirm their claims against Apple sources, and label them as **non-Apple sources**.
- **If the user says to find the sources yourself:** skip the question below and start searching.
- **If NO starting resources are provided:** ask the user before running any external search:

  > "Do you have any starting resources or documentation links you would like me to begin with, or should I search and discover the relevant Apple resources on my own? (Note: Providing initial resources to start from helps focus the search on your exact needs and leads to faster, more accurate results.)"

  Wait for the answer before searching.

## Step 2: Establish the Baseline

### Current SDK

Your training data may be older than Apple's latest releases. Before researching, confirm the latest released OS and Xcode versions from Apple, for example the [Xcode release notes](https://developer.apple.com/documentation/xcode-release-notes) or [Apple Developer Releases](https://developer.apple.com/news/releases/). Note any beta that is currently out. Do not assume the version from memory. Record what you found in the report header.

### Project context (optional)

If you are running inside an Xcode project or Swift package, read it (read only) to find:

- the deployment target and supported platforms of each relevant target,
- the Swift language version and strict-concurrency settings,
- the Apple frameworks the project already uses that relate to the topic.

Use these to map availability findings to the project: say which APIs work at the project's deployment target and which need an availability check. Summarize the context in the report header. If there is no project, skip this step and write availability against the OS versions themselves.

## Step 3: Technical Research Scope

Check technical details against primary Apple resources:

- **Apple Developer Documentation** (`developer.apple.com/documentation/...`): public APIs, exact signatures, availability, minimum OS and SDK versions, deprecations, and required entitlements, Info.plist keys, and permissions.
- **WWDC Sessions & Transcripts** (`developer.apple.com/videos/...`): relevant session transcripts, code examples, session titles, and years. Prefer the most recent session and note when older guidance has been superseded.
- **Human Interface Guidelines** (`developer.apple.com/design/human-interface-guidelines/...`): interaction patterns, visual conventions, accessibility requirements, and platform-specific UX guidance.
- **Release Notes (required):** the release notes for the current OS versions and Xcode version that relate to the topic, checked for framework changes, deprecations, and known issues. List which release notes you checked in the report header.
- **Sample Projects:** official Apple sample code for the topic, when it exists.

### Source rules

- Third-party references (blogs, forums, Stack Overflow, open-source repositories) that you find yourself may be used **only** to fill edge-case gaps that Apple sources leave open. Label each one clearly as a **non-Apple source** wherever it is used. Links the user provided are covered in Step 1.
- **Never invent** API signatures, availability, entitlements, or citations. If something can't be verified from a source you actually read, write **Unverified** and say what is missing.
- **Mark your own reasoning.** When a point is your conclusion from a source rather than something the source states, label it **Inference** and cite the source it's based on.
- **When sources disagree** (for example, the documentation and an older WWDC session, or two documentation pages), quote both, cite both, and say which is newer. Don't silently pick one. Add the conflict to Open Questions. Apple documentation generally takes precedence over older sessions, but say so when you apply that.
- Cite every factual claim with a short tag that points to the reference list in section 7 (see Citations below).
- Record the newest SDK and OS versions the sources cover, so the reader knows how current the research is.

### Citations

Give every source you read a short tag, numbered by type:

| Prefix | Source type |
|---|---|
| `A` | Apple Developer Documentation |
| `H` | Human Interface Guidelines |
| `V` | WWDC sessions and other Apple videos |
| `R` | Release notes |
| `S` | Sample code and other Apple pages (App Store Connect, App Review Guidelines, Apple newsroom) |
| `N` | Non-Apple sources |

Put the tag right after the claim, for example: "The on-device model is only available when Apple Intelligence is turned on [A3]." Several sources: [A3][V7]. Every tag used in the report must appear in the reference list, and every entry in the reference list must be used at least once.

### Fetching tips

- Apple documentation and HIG pages render with JavaScript, so a plain fetch can come back nearly empty. Try the JSON data endpoint first by replacing the host path prefix:
  - `https://developer.apple.com/documentation/<path>` → `https://developer.apple.com/tutorials/data/documentation/<path>.json`
  - `https://developer.apple.com/design/human-interface-guidelines/<topic>` → `https://developer.apple.com/tutorials/data/design/human-interface-guidelines/<topic>.json`
- If the JSON endpoint fails and your agent has a browser tool, open the page there and read its text.
- WWDC session pages (`developer.apple.com/videos/play/wwdc<year>/<id>/`) include the transcript and code snippets on the page itself.
- For a topic with several independent parts, run fetches in parallel.
- For a large topic (for example, checking many symbols for one platform's availability), split the work by area if your agent supports subagents or parallel tasks, then merge their findings into one report.

## Step 4: Deliverable

Write a clean, structured Markdown document at `.docs/<feature>-research.md` in the current project root. Create the `.docs/` directory if it doesn't exist. Use the structure below.

If a section or bullet doesn't apply to the topic, write one line saying so and why, for example "Offline: not applicable, the on-device model runs without a network connection [A4]." Don't fill it with general advice.

```markdown
# Research: <Feature / Topic>

> Researched on <YYYY-MM-DD> · Latest released SDK: <e.g. iOS 27.1 / Xcode 27.1, confirmed from <source>> · Beta out: <version, or "none">
> Release notes checked: <list>
> Starting resources: <user-provided links, or "self-discovered">
> Project context: <deployment targets, platforms, Swift version, related frameworks already used, or "none (researched outside a project)">

**How to read this.** Tags in square brackets, like [A1], point to the reference list in section 7. **Unverified** means no source I read confirms the point. **Inference** marks my own reasoning from a cited source, not something a source states.

## 1. Open Questions
- Anything unverified, ambiguous, where sources disagree, or that needs a product decision from the user

## 2. Feature Overview & Platform Scope
- Target OS versions, per platform (iOS, iPadOS, macOS, watchOS, tvOS, visionOS)
- Device capabilities and hardware requirements
- Frameworks required

## 3. API & Technical Breakdown
- Relevant frameworks and their roles
- Key public APIs: exact signatures, availability annotations, deprecations
- Platform constraints
- Entitlements, capabilities, Info.plist keys, and privacy usage strings

## 4. HIG & UX Guidelines
- Recommended user patterns and system integration standards
- Platform-specific UX differences
- Accessibility considerations (VoiceOver, Dynamic Type, Reduce Motion, contrast, etc.)

## 5. Edge Cases & Limitations
- Permission denial and restricted states
- Offline and network-failure handling
- Concurrency considerations (actor isolation, Sendable, main-thread requirements)
- Cross-platform differences and unsupported platforms
- Known issues from release notes

## 6. Key Technical Takeaways
- A short summary of the essential concepts, showing full understanding before feature planning

## 7. References
- [A1] [Title](URL) — Apple Documentation — what it was used for
- [H1] [Title](URL) — HIG — what it was used for
- [V1] [Title](URL) — WWDC<year> session <id> — what it was used for
- [R1] [Title](URL) — Release notes — what it was used for
- [S1] [Title](URL) — Sample code / other Apple page — what it was used for
- Non-Apple sources, if any, in a separate, clearly labeled sub-list: [N1] [Title](URL) — what it was used for
```

## Finishing Up

When the document is written, reply to the user with:

- the path to `.docs/<feature>-research.md`,
- a 3–5 bullet summary of the most important findings,
- any open questions or unverified points.

Then stop. Wait for the user to review and approve the research before any planning or implementation.
