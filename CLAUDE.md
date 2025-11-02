# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This repository provides Claude Code configuration templates for Unity game development projects. It contains rules, commands, and settings that enforce best practices for Unity development with C#, including coding standards, testing guidelines, and TDD workflows.

## Repository Structure

```
.
├── .claude/
│   └── commands/        # Project-specific slash commands
│       └── sync-rules-jp.md       # Sync rules/ to rules_jp/ with Japanese translation
├── rules/               # Development guidelines and coding standards (English)
│   ├── 00-code-of-conduct.md      # Collaboration principles and research rules
│   ├── 01-coding.md               # C# coding standards for Unity
│   ├── 02-testing.md              # Testing guidelines (NUnit/Unity Test Framework)
│   ├── 10-unity-project.md        # Unity project structure and dependencies
│   ├── 11-unity-documentation.md  # How to reference Unity documentation
│   └── 12-unity-yaml.md           # Unity YAML file editing guidelines
├── rules_jp/            # Japanese translations of rules/ (for maintainer reference)
├── commands/            # Slash commands for common workflows
│   ├── run-tests.md               # Test execution via MCP
│   ├── create-doc.md              # Generate specification documents
│   ├── create-test-cases.md       # Derive test cases from specs
│   ├── implement-code-by-tdd.md   # TDD implementation workflow
│   └── implement-code.md          # Standard implementation workflow
├── settings.json        # Claude Code permissions and MCP configuration
└── .mcp.json           # MCP server definitions for Unity
```

## Key Development Principles

### Code of Conduct (rules/00-code-of-conduct.md)
- Always clarify unclear requirements during planning phase
- Never implement based on assumptions
- Work iteratively on subdivided functions
- **Test failure rule**: Consult user after 2 consecutive test failures
- **Research rule**: Clone open-source dependencies to `.claude/sandbox/` for reference

### Coding Standards (rules/01-coding.md)
- Follow SOLID principles (especially SRP, ISP, DIP)
- Use PascalCase for public members, camelCase with `_` prefix for private fields
- Static fields: camelCase with `s_` prefix
- Early return pattern preferred over nested conditions
- XML documentation required for all public classes and methods
- Properties with `[field: SerializeField]` pattern for Unity serialization

### Testing Guidelines (rules/02-testing.md)
- Use Unity Test Framework (NUnit3-based)
- Test naming: `MethodName_Condition_ExpectedResult`
- Arrange-Act-Assert pattern with blank line separation
- Use constraint model (`Assert.That`) - single assertion per test
- Test objects: `sut` (system under test), `actual`, `expected`
- Test doubles prefix: `stub`, `spy`, `dummy`, `fake`, `mock`
- Run tests via MCP (`mcp__UnityNaturalMCP__RunEditModeTests` or `RunPlayModeTests`)
- **Filter tests** aggressively: testNames > groupNames > assemblyNames

### Unity Project Structure (rules/10-unity-project.md)

Feature-based assembly structure:
```
Assets/
  FEATURE-NAME/
    Scenes/
    Scripts/
      Editor/              # Editor extensions
        FEATURE.Editor.asmdef
      Runtime/             # Runtime code
        FEATURE.asmdef
    Tests/
      Editor/
        FEATURE.Editor.Tests.asmdef
      Runtime/
        FEATURE.Tests.asmdef
```

Embedded packages:
```
Packages/
  PACKAGE-NAME/
    Editor/
    Runtime/
    Tests/
```

Dependencies:
- UPM packages cached in `./Library/PackageCache/`
- NuGet DLLs placed in `./Assets/Packages/`
- Always commit `.meta` files with new assets

## MCP Servers

Available MCP servers (configured in `.mcp.json`):
- `gitmcp-UnityCsReference`: Unity C# reference source
- `gitmcp-uGUI`: Unity UI source
- `gitmcp-NewInputSystem`: Unity Input System source
- `UnityNaturalMCP`: Unity editor integration (test execution, diagnostics)
- `jetbrains`: Code formatting and diagnostics

## Slash Commands

### /run-tests
Execute tests on Unity editor via `UnityNaturalMCP`.
- Edit Mode: `mcp__UnityNaturalMCP__RunEditModeTests` (for Editor namespace/assembly)
- Play Mode: `mcp__UnityNaturalMCP__RunPlayModeTests` (for Runtime code)
- Filter priority: testNames > groupNames > assemblyNames

### /create-doc
Generate specification document from conversation.
- Validates requirements, specs, and design are clear
- Output: `./Documentation/yyyy-mm-dd-subject.md`
- Sections: Requirements/Problem/Motivation, Specifications, Design
- Language: Japanese
- Commits to git

### /create-test-cases
Derive test cases from specifications.
- Uses equivalence partitioning, boundary value analysis, or state transition testing
- Adds coverage-aware test cases in natural language
- Updates specification file with test cases
- Commits to git

### /implement-code-by-tdd
Kent Beck's Canon TDD workflow.
- Requires test cases in specification file
- Inner loop: Write test → Fail → Implement → Pass → Refactor → Resolve diagnostics → Reformat → Commit
- Uses `mcp__jetbrains__open_file_in_editor` and `get_current_file_errors` for diagnostics
- Commits after each test case passes

### /implement-code
Standard implementation workflow (non-TDD).
- Requires test cases in specification file
- Steps: Skeleton → Tests → Fail → Commit → Implement → Pass → Commit → Refactor → Diagnostics → Reformat → Commit

## Project-Specific Commands

### /sync-rules-jp
Synchronize English documentation from `rules/` to Japanese translations in `rules_jp/`.
- Phase 1: Discover source files and check target directory (parallel Task agents)
- Phase 2: Compare versions and list files requiring updates
- Phase 3: Translate/update files in parallel (parallel Task agents)
- Phase 4: Verify and report summary
- Preserves markdown formatting, code blocks, and technical references
- Uses です・ます調 for Japanese translations

## Unity Documentation

Reference official documentation (replace `6000.0` with version from `./ProjectSettings/ProjectVersion.txt`):
- Manual: `https://docs.unity3d.com/6000.0/Documentation/Manual/UnityManual.html`
- Script Reference: `https://docs.unity3d.com/6000.0/Documentation/ScriptReference/index.html`
- UI/Events/TMPro: `https://docs.unity3d.com/Packages/com.unity.ugui@latest`

For UPM packages:
- Read README.md in `./Library/PackageCache/PACKAGE-NAME/`
- Check `documentationUrl` in package.json

For NuGet packages:
- Listed in `./Assets/packages.config`
- README at `https://www.nuget.org/packages/PACKAGE-NAME`
- Clone source to `./.claude/sandbox/` if needed

## Unity YAML Files

Files with `.meta`, `.asset`, `.prefab`, `.unity` extensions:
- Operate via Unity Editor through MCP when possible
- Direct editing references:
  - .meta: https://docs.unity3d.com/Manual/AssetMetadata.html
  - .asset/.prefab/.unity: https://docs.unity3d.com/Manual/UnityYAML.html

## Permissions

Allowed operations (settings.json):
- Git: add, commit, rm
- Bash: ls, mkdir, rg
- WebFetch: github.com, docs.unity3d.com, forum.unity.com
- WebSearch: unrestricted
- All configured MCP tools

Denied operations:
- Destructive: sudo, rm, git force operations
- Network: curl, wget, nc, npm
- Git: branch, checkout, reset, rebase, amend, push -f
- Sensitive files: .env.*, id_rsa, id_ed25519, **/*token*, **/*key*
- Build artifacts: node_modules/**, ./Library/** (except PackageCache)
- Lock files: ./Packages/packages-lock.json

## Default Mode

Plan mode enabled by default (`"defaultMode": "plan"` in settings.json).
