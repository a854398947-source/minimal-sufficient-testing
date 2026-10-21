# Minimal Sufficient Testing

A reusable Codex skill for choosing risk-based verification and stopping when there is enough reliable evidence to deliver a change.

## Problem and intended use

Small changes can trigger unnecessary full test suites, builds, lint runs, repeated inspections, or environment repairs. These checks consume time and can introduce unrelated changes without improving confidence in the requested behavior.

Use this skill when writing, fixing, reviewing, refactoring, or configuring software. Before a check, identify the relevant failure mode it could detect. Choose the smallest appropriate verification for the actual risk, expand only when another distinct risk requires it, and stop once sufficient evidence is available.

Explicit user requirements, repository instructions, required CI/release checks, and material security or data-integrity risks still apply. The skill does not impose a fixed number of checks or guarantee that every task needs only one test.

`SKILL.md` is the complete canonical skill, including its YAML front matter and original stop conditions. This README describes distribution and installation; it does not replace the skill.

## Repository contents

```text
minimal-sufficient-testing/
  SKILL.md
  README.md
```

No scripts, dependencies, test frameworks, personal configuration, or global instruction files are distributed. No LICENSE file is included.

## Install on Windows 11

Use PowerShell. Git is needed only to clone or update this repository; the skill itself is instruction-only.

Clone the repository using its actual GitHub HTTPS URL. If you already have a local checkout, skip this block.

```powershell
$repoUrl = Read-Host 'Paste this repository GitHub HTTPS URL'
$repoPath = Join-Path $env:USERPROFILE 'Documents\GitHub\minimal-sufficient-testing'
New-Item -ItemType Directory -Path (Split-Path -Parent $repoPath) -Force | Out-Null
git clone -- $repoUrl $repoPath
if ($LASTEXITCODE -ne 0) { throw 'Clone failed; do not continue.' }
```

Resolve the installation location. The originating Codex installation uses `$CODEX_HOME/skills`, defaulting to `%USERPROFILE%\.codex\skills` when `CODEX_HOME` is unset:

```powershell
$repoPath = Join-Path $env:USERPROFILE 'Documents\GitHub\minimal-sufficient-testing'
$codexRoot = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $env:USERPROFILE '.codex' }
$skillRoot = Join-Path $codexRoot 'skills'
```

The [current Codex skills documentation](https://learn.chatgpt.com/docs/build-skills) also documents `%USERPROFILE%\.agents\skills` for user skills. If your Codex version uses that location, replace `$skillRoot` before continuing:

```powershell
$skillRoot = Join-Path $env:USERPROFILE '.agents\skills'
```

Choose the location used by your Codex installation; avoid installing duplicate copies in both locations. Then install only the canonical skill file:

```powershell
$sourceFile = Join-Path $repoPath 'SKILL.md'
$skillDir = Join-Path $skillRoot 'minimal-sufficient-testing'
if (-not (Test-Path -LiteralPath $sourceFile -PathType Leaf)) { throw 'Source SKILL.md is missing.' }
if (Test-Path -LiteralPath $skillDir) { throw 'Installation already exists; use the update instructions.' }
New-Item -ItemType Directory -Path $skillDir -Force | Out-Null
Copy-Item -LiteralPath $sourceFile -Destination (Join-Path $skillDir 'SKILL.md')
```

If the skill does not appear in Codex, restart Codex. Invoke it explicitly with `$minimal-sufficient-testing`, or let Codex select it when the task matches its description.

## Update an existing installation

Use the same `$repoPath`, `$skillRoot`, and `$skillDir` values as during installation. Pull without creating a merge commit, preserve a backup of the installed file, and replace only `SKILL.md`:

```powershell
git -C $repoPath pull --ff-only
if ($LASTEXITCODE -ne 0) { throw 'Update failed; resolve the Git issue before copying.' }
$installedFile = Join-Path $skillDir 'SKILL.md'
if (-not (Test-Path -LiteralPath $installedFile -PathType Leaf)) { throw 'Installed SKILL.md is missing; use the installation instructions.' }
$backupFile = $installedFile + '.backup-' + (Get-Date -Format 'yyyyMMdd-HHmmss-fffffff')
Copy-Item -LiteralPath $installedFile -Destination $backupFile
Copy-Item -LiteralPath (Join-Path $repoPath 'SKILL.md') -Destination $installedFile -Force
```

Review and merge any personal skill edits before replacing them. Updating the skill does not update global instructions or other files in the installed skill directory.

## Add a global rule

The skill supplies the full verification workflow and is loaded when selected. A global rule tells Codex to apply that workflow by default across projects. Installing the skill and adding a global rule serve different purposes; a global rule pointing to an unavailable skill is not a replacement for installation.

Global instructions live in the Codex home directory. [Codex uses a non-empty `AGENTS.override.md` there before `AGENTS.md`](https://learn.chatgpt.com/docs/agent-configuration/agents-md). Open the active file with PowerShell:

```powershell
$codexRoot = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $env:USERPROFILE '.codex' }
New-Item -ItemType Directory -Path $codexRoot -Force | Out-Null
$globalFile = Join-Path $codexRoot 'AGENTS.md'
$overrideFile = Join-Path $codexRoot 'AGENTS.override.md'
if (Test-Path -LiteralPath $overrideFile -PathType Leaf) {
    if (-not [string]::IsNullOrWhiteSpace([System.IO.File]::ReadAllText($overrideFile))) {
        $globalFile = $overrideFile
    }
}
if (-not (Test-Path -LiteralPath $globalFile)) { New-Item -ItemType File -Path $globalFile | Out-Null }
notepad.exe $globalFile
```

Preserve existing content and add this section once. Include the absolute path of your installed `SKILL.md` if it helps your setup resolve the skill.

```markdown
## Minimal Sufficient Testing

When writing, fixing, reviewing, refactoring, or configuring software, apply the installed `minimal-sufficient-testing` skill by default. Read its SKILL.md before selecting or running verification commands.

Use risk-based, minimal-sufficient verification. Each additional check must address a distinct, relevant failure mode. Preserve explicit user requirements and mandatory project checks. Stop when sufficient evidence has been obtained, and report only checks actually performed and any material limitations.
```

Start a new Codex session after changing global instructions. Project-specific instructions and explicit user requirements still apply. Keep your personal global instructions outside this repository.

## Skill versus plugin

This repository distributes a standalone skill. A plain GitHub repository is not automatically a Codex plugin: plugin packaging and registration are separate steps. This repository does not include a plugin manifest or register connectors. See [official skill and plugin guidance](https://learn.chatgpt.com/docs/build-skills).
