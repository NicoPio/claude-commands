# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This repository contains a collection of **custom Claude Code slash commands and agents** designed to enhance development workflows. The repository structure follows a specific pattern:

- **.claude/commands/** - Slash command prompts (e.g., /commit, /epct, /oneshot)
- **.claude/agents/** - Specialized agent prompts for sub-tasks (e.g., explore-codebase, snipper, websearch)

## Architecture Patterns

### Command Structure

Commands are markdown files with YAML frontmatter and follow specific patterns based on their purpose:

1. **Numbered Workflow Commands** - Process-heavy commands (EPCT, commit, run-tasks, debug)
   - Use numbered sections: `1. **ACTION NAME**`, `2. **NEXT PHASE**`
   - Include exact bash commands in backticks
   - Emphasize critical rules with **CRITICAL**, **MUST**, **NEVER**

2. **Reference/Docs Format** - CLI wrapper commands
   - Include allowed-tools in frontmatter: `allowed-tools: Bash(<cli> :*)`
   - Group commands by category with bash code blocks

3. **Section-Based Analysis** - Research/analysis commands (explore, explain-architecture)
   - Use `## [Phase Name]` headers with **Goal**: statements
   - Focus on search and analysis tools

### Agent Structure

Agents use a simpler structure than commands:

```markdown
---
name: [kebab-case-name]
description: [One-line when-to-use statement]
color: [yellow|blue|green|red]
model: [haiku|sonnet] (optional, defaults to sonnet)
---

You are a [specialist role]. [Purpose].

## [Primary Action Phase]
- Direct instructions
- Tool usage patterns

## Output Format
[Exact structure expected]

## Execution Rules
- Constraints and guidelines
```

### YAML Frontmatter Conventions

Commands use:
- `description:` - One-line purpose for command list
- `argument-hint:` - Shows expected args (e.g., `<issue-number|issue-url>`)
- `allowed-tools:` - Restricts available tools (e.g., `Bash(git :*)`, `Read, Edit, Write`)

## Core Workflows

### EPCT Methodology (Explore-Plan-Code-Test)

The primary development workflow, used by `/epct` and `/run-tasks`:

1. **EXPLORE** - Launch parallel subagents to find relevant files
2. **PLAN** - Create detailed implementation strategy, ask user for clarification
3. **CODE** - Implement following existing patterns, stay in scope
4. **TEST** - Run lint/typecheck/tests for modified code only

### OneShot Workflow

Fast implementation via `/oneshot`:
- Quick exploration (1-2 agents max)
- Direct coding without planning phase
- Validation with lint + typecheck only

### Git Operations

Commands like `/commit` and `/create-pull-request`:
- **NEVER** work directly on main/master branch
- Use minimal, conventional commit messages
- Auto-generate PR descriptions from changes

## Command & Agent Development

### Creating New Commands

Use `/prompt-command create <name>` which:
- Chooses appropriate pattern (numbered/reference/section-based)
- Saves to `.claude/commands/<name>.md`
- Follows existing conventions

### Creating New Agents

Use `/prompt-agent create <name>` which:
- Applies agent template with sections
- Saves to `.claude/agents/<name>.md`
- Keeps agents focused and fast

### Refactoring Existing Prompts

- `/prompt-command refactor @commands/file.md` - Enhance command
- `/prompt-agent refactor @agents/file.md` - Enhance agent
- **CRITICAL**: Preserve all existing content and structure

## Common Patterns

### Emphasis Keywords

Use these consistently across all commands/agents:
- **CRITICAL** / **MUST** / **NEVER** - Non-negotiable rules
- **STAY IN SCOPE** - Prevent feature creep
- **BEFORE [action]** / **AFTER [action]** - Prerequisites/follow-ups
- **ULTRA THINK** - Signals need for deep analysis

### Tool Restrictions

Many commands use `allowed-tools:` to limit scope:
- `Bash(git :*)` - Only git commands
- `Bash(gh :*)` - Only GitHub CLI
- `Read, Edit, MultiEdit, Write` - File operations only
- Empty/omitted - All tools available

### Parallel Execution Pattern

Commands emphasize speed through parallel operations:
- Launch multiple search agents simultaneously
- Run multiple bash commands in parallel when independent
- Use `explore-codebase` agent for codebase searches

## EPCT Subcommands

The `/epct` command has modular phases in `.claude/commands/epct/`:

- **explore.md** - Phase 1: Context gathering with agents
- **plan.md** - Phase 2: Implementation strategy creation
- **tasks.md** - Phase 2.5: Break plan into task files
- **code.md** - Phase 3: Implementation with ultra thinking
- **deploy.md** - Phase 4: Build and validate with auto-fixing

Can be run individually via:
- `/epct:explore <description>`
- `/epct:plan <task-folder>`
- `/epct:tasks <task-folder>`
- `/epct:code <task-folder>`
- `/epct:deploy`

## Agent Specializations

- **explore-codebase** (haiku) - Fast codebase searches for feature implementation
- **explore-docs** - Library documentation and online research via Context7/WebFetch
- **snipper** (haiku) - Rapid code modifications with minimal output
- **websearch** - Quick web searches and fetches
- **action** - Conditional execution based on specific conditions
- **fix-grammar** - Grammar/spelling fixes while preserving formatting

## Key Principles

1. **Specificity over generality** - Commands include exact bash commands, file paths, patterns
2. **Speed through parallelism** - Launch multiple agents/tools simultaneously
3. **Stay in scope** - Only modify what's necessary for the task
4. **No verbose output** - Especially for agents (fast execution)
5. **Follow existing patterns** - Match codebase conventions automatically
6. **Minimal comments** - Prefer clear naming over documentation

## Special Commands

- `/claude-memory` - Create/update CLAUDE.md files following best practices
- `/cleanup-context` - Optimize memory bank files, remove duplicates
- `/watch-ci` - Monitor CI pipeline and auto-fix failures
- `/fix-pr-comments` - Fetch PR review comments and implement changes
- `/deep-code-analysis` - Thorough code exploration for complex questions
- `/explain-architecture` - Analyze architectural patterns and design decisions
