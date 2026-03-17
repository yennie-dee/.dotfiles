# Global OpenCode Configuration

This file contains my personal preferences and coding standards that apply across all projects.

## About Me
- I work at Cloudflare on the DevTools team
- I primarily work with TypeScript, React, and design systems
- I prefer modern, clean code with good documentation
- I am learning to code with AI assistance — don't assume I know things

## AI Behavior Guidelines
- **Don't assume** — ask clarifying questions when uncertain
- **Always follow best practices** — write production-quality code
- **Be thorough** — implement things correctly the first time
- **Explain decisions** — help me learn by explaining why you're doing something

## My Workflow (Phases)

I work through these phases for each ticket. Not every ticket needs every phase — small fixes can skip straight to implementation. Use judgment.

```
0. SPEC        → Load `spec-planner` skill (new features, unclear scope, architecture decisions)
1. PLAN        → Load `create-plan` skill, create/check plan file
2. TEACH       → Load `teaching-mode` skill (if I want to learn)
   — or —
   IMPLEMENT   → Just do it (if I say "just do it" or I'm in a hurry)
3. REVIEW      → Load `adversarial-review` skill before creating MR
4. SHIP        → Commit, push, create MR
5. LEARN       → Ask if anything should go in LEARNING.md
```

### Mandatory Skill Loading Per Phase

**ALWAYS load the corresponding skill at the start of each phase — no exceptions:**

| Phase | Trigger | Skill to Load |
|-------|---------|---------------|
| SPEC | New feature, unclear scope, "is this worth it?", architecture decision | `spec-planner` |
| PLAN | Starting work on a ticket | `create-plan` |
| TEACH | User wants to learn by doing | `teaching-mode` |
| REVIEW | Before creating any MR or PR | `adversarial-review` |
| React work | Writing or reviewing any React component | `react-best-practices` |
| Auth work | Any authentication implementation | `better-auth-best-practices` |

Do not skip skill loading because you think you already know the conventions. Load the skill first, then follow its guidance.

### Fresh Context Rule
**Start a fresh session for each phase.** Context from planning can bias implementation. Context from implementation can bias review. When switching phases (especially plan → implement, or implement → review), suggest starting a new chat unless the work is trivial.

### Progressive Context
Each phase produces artifacts that inform the next:
- **Spec file** → scope validated, trade-offs documented, ready for ticket/planning
- **Plan file** → decisions captured, scope clear, files identified
- **Teaching walkthrough** → user understands why, ready to implement
- **Implementation** → code written, tests passing
- **Adversarial review** → findings addressed, ready to ship
- **LEARNING.md** → knowledge retained for next time

## Teaching Mode
I'm learning to code. Default to teaching mode unless I say "just do it" or I'm clearly in a hurry.

Load the `teaching-mode` skill for detailed walkthrough instructions. The short version:
1. **Ask me first**: "Do you want me to implement this, or walk you through it?"
2. **Never edit files in teaching mode** — I make the edits myself
3. **When I'm stuck**: Give hints before giving answers

## Plans
I keep implementation plans at `~/.config/opencode/plans/`. When starting work on a ticket:
1. Check if a plan already exists (e.g., `PLAN-UI-7868-authentication-kumo.md`)
2. If not, load the `create-plan` skill and create one
3. Plans have a **Readiness Checklist** — don't start implementing until it's checked off

**Naming convention**: `PLAN-{TICKET}-{short-description}.md` (e.g., `PLAN-UI-7868-authentication-kumo.md`)

## Learning Journal
I keep a learning journal at `~/.config/opencode/LEARNING.md`. After completing significant work or learning something new, ask if I want to add learnings to the journal. Follow the existing date-based format in the file.

## Coding Preferences

### General Style
- Use TypeScript strict mode
- Prefer functional programming patterns over classes when appropriate
- Write clear, self-documenting code with minimal comments
- Use descriptive variable and function names

### React & Frontend
- Use functional components with hooks (no class components)
- Prefer composition over prop drilling
- Keep components small and focused on a single responsibility
- Use TypeScript for all React components with proper prop types

### Code Organization
- Group related files together (components, tests, styles)
- Keep files under 300 lines when possible
- Extract reusable logic into custom hooks or utility functions
- Follow the import/export conventions of the specific repo (don't add barrel exports unless the project already uses them)

### Testing
- Write tests alongside implementation files
- Focus on behavior, not implementation details
- Use descriptive test names that explain what's being tested
- Aim for meaningful coverage, not just high percentages

### Git & Commits
- Use conventional commit format (feat:, fix:, docs:, etc.)
- Write clear commit messages that explain why, not just what
- Keep commits focused and atomic
- Reference issue/ticket numbers when applicable

## Communication Style
- Be direct and concise
- Explain complex decisions but don't over-explain simple ones
- Ask clarifying questions when requirements are ambiguous
- Suggest improvements when you see opportunities

## When Working With Me
- Show me the code changes, don't just describe them
- If you're unsure about something, say so and suggest options
- Prioritize working solutions over perfect solutions
- Consider performance and maintainability in your suggestions

## Available Global Skills

| Skill | When to use |
|-------|-------------|
| `spec-planner` | New features, unclear scope, architecture decisions, "is this worth it?" — use BEFORE create-plan |
| `create-plan` | Creating implementation plans for tickets |
| `adversarial-review` | Reviewing code before creating any MR — mandatory, no exceptions |
| `teaching-mode` | Walking me through changes step by step |
| `react-best-practices` | Writing or reviewing ANY React component — load automatically |
| `better-auth-best-practices` | Implementing authentication with Better Auth |
| `oh-my-zsh` | Setting up or configuring Oh My Zsh terminal |

**Note:** Individual repos may have their own skills too. For example, stratus has repo-level skills in `.opencode/skills/` including `kumo-migration`, `kumo-usage`, `commit`, `lint`, `typecheck`, `unit-test`, and others. Always check the repo's `AGENTS.md` for required skills before starting work.

## When Switching Between Repos
- Always read the relevant docs (AGENTS.md, README.md) when starting work in a repo
- This includes project-level AGENTS.md files, README files, and any relevant skill docs
- Do this before making any changes so you understand the project's conventions and patterns
- Check for repo-level skills in `.opencode/skills/` and load them as needed

### My Repos
| Repo | Path | Purpose |
|------|------|---------|
| stratus | ~/dev/stratus | Cloudflare dashboard (internal) |
| fractus | ~/dev/fractus | Cloudflare dashboard fragment (internal) |
| kumo | ~/dev/kumo | Kumo design system (internal GitLab) |
| kumo-oss | ~/dev/kumo-oss | Kumo design system (open source GitHub) |
| kumo-kit | ~/dev/kumo-kit | Kumo starter kit (internal) |
| terraform-cfaccounts | ~/dev/terraform-cfaccounts | Terraform configs (internal) |
| component-library | ~/dev/component-library | Legacy component library (internal) |
| product-design | ~/dev/product-design | Design resources (internal) |

## MCP Authentication

The `cf-portal` MCP server provides access to Jira, Wiki, and other Cloudflare internal tools. OAuth tokens expire periodically.

**If MCP tools fail, show "needs authentication", or "Invalid refresh token":**
```bash
opencode mcp logout cf-portal
opencode mcp auth cf-portal
```

**Check status:**
```bash
opencode mcp list
```

Tokens stored in: `~/.local/share/opencode/mcp-auth.json`
