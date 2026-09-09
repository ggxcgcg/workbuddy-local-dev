---
name: workbuddy-local-dev
description: Use this skill whenever the user wants WorkBuddy to build, modify, debug, test, or review code in a local Windows project. Route the work through WorkBuddy's installed headless CodeBuddy CLI when available, so WorkBuddy edits the real local directory without GUI automation; Codex owns planning, verification, and final code review. Trigger for requests such as “让 WorkBuddy 写代码”, “用 WorkBuddy 改项目”, “你规划它编码”, “把任务交给桌面端 Agent”, or any request to have another local coding agent implement changes.
---

# WorkBuddy 本地协作开发

Use this workflow when the user wants Codex to plan and review while WorkBuddy performs the implementation in a local Windows project.

## Operating principles

- Keep the user’s requested division of labor: Codex plans, scopes, verifies, and reviews; WorkBuddy implements.
- Prefer WorkBuddy’s installed headless CLI over desktop or browser UI automation. This avoids screenshots, cursor actions, token-heavy polling, and accidental edits in a remote sandbox.
- Never silently switch the target directory. Confirm the project path from the user prompt or the current workspace, and keep all edits inside that path.
- Treat the WorkBuddy response as an implementation attempt, not proof of completion. Inspect the actual files and run proportionate checks locally.
- Do not use destructive cleanup or reset operations unless the user explicitly requests them.

## Workflow

### 1. Plan before delegation

Write a compact implementation brief containing:

1. Goal and user-visible behavior.
2. Existing project path and relevant files.
3. Architecture or UI direction.
4. Acceptance criteria and required checks.
5. Constraints, including whether new dependencies are acceptable.

For frontend work, choose a distinctive visual direction before asking WorkBuddy to code. Include responsive behavior, interaction requirements, accessibility expectations, and a fallback when external assets are unavailable.

### 2. Locate the local CLI

On Windows, first look for the installed WorkBuddy/CodeBuddy CLI. The bundled installation commonly uses:

```text
D:\WorkBuddy\resources\app.asar.unpacked\cli\bin\codebuddy
```

Verify it with:

```powershell
node "D:\WorkBuddy\resources\app.asar.unpacked\cli\bin\codebuddy" --version
```

If that path is absent, search the installed WorkBuddy directory or use an already-available `codebuddy`, `codebuddy-code`, or `cbc` command. Do not launch the desktop UI merely to type a task when the headless CLI is available.

### 3. Delegate through headless mode

Run from the target project directory. Use the local configuration directory inside the workspace so missing global session folders do not block execution:

```powershell
$env:CODEBUDDY_CONFIG_DIR = "<workspace>\.codebuddy-cli"
node "<workbuddy-cli>" -p -y "<implementation brief>"
```

`-p` is the non-interactive mode. `-y` is appropriate only when the user has explicitly asked for WorkBuddy to edit the named local project; keep the prompt narrowly scoped to that project. Do not pass secrets, unrelated personal files, or broad filesystem paths.

For long tasks, allow the process to run asynchronously and inspect the target directory with normal filesystem commands. Do not use repeated screenshots or desktop polling. If the process becomes idle after files and checks are complete, stop it cleanly and proceed with local verification.

### 4. Review the real changes

After WorkBuddy returns or files appear:

- List changed files and inspect the important source files.
- Check that the implementation matches the brief, not merely the model’s summary.
- Run the project’s existing tests, lint, typecheck, build, or self-check scripts.
- For web projects, verify the entry point, responsive behavior, interactions, and static asset paths. Use a browser only for a focused final visual check when useful; do not use browser automation as the coding transport.
- Fix obvious issues yourself or send a focused follow-up to WorkBuddy through the same CLI.

When a generated self-check fails because its assertions are brittle rather than because the product is wrong, update the check to test the intended behavior robustly and rerun it. Do not weaken checks to hide a real defect.

### 5. Report completion

Lead with the outcome. Report:

- What WorkBuddy implemented.
- The exact local project path.
- Checks run and their results.
- Any follow-up limitations or user actions, such as how to start the site.

Link to important local files with absolute paths.

## Fallbacks

- If WorkBuddy’s CLI is unavailable or cannot authenticate, explain the concrete blocker and ask before using desktop UI automation.
- If the user explicitly asks for desktop UI interaction, use the Windows computer-use skill, but keep the interaction minimal: one handoff, then monitor files and processes from the terminal.
- If the user did not actually authorize another agent to edit files, plan and ask for confirmation before invoking WorkBuddy.

## Example delegation brief

```text
Work in <project-path>. Implement <feature>. Preserve existing behavior. Use the project’s current stack. Acceptance criteria: <criteria>. Run <checks> and leave all source changes in the project directory. Do not only describe code; edit the files and report the changed files and check results.
```

