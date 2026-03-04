# Coding Practices with AI

General workflows, prompt engineering techniques, and development practices for AI-assisted coding. These are tool-agnostic — they work with any AI coding assistant.

---

## Core Workflow: Explore → Plan → Execute

The most reliable pattern for AI-assisted development:

### 1. Explore

- Use prompts like "prepare to discuss how our frontend works"
- Claude will spend 50k+ tokens over ~7 minutes understanding the codebase
- If the overview is wrong, escape and restart — don't try to correct mid-exploration
- Ask follow-up questions to ensure proper context

### 2. Plan

- Use "think hard" mode for complex planning
- Break work into smaller tasks:
  - **Small tasks:** Don't overthink, just code
  - **Medium-large:** Break into testable, deployable PRs
  - **High risk:** Take 2-3 planning attempts
- A good plan is the single biggest predictor of one-shot success

### 3. Execute

- Execute each task and mark it as complete
- Verify after each step — don't batch and hope

---

## Vibe Coding Decision Tree

When choosing a workflow approach:

```
1. Do you need a full AI development team?
   ├─ YES → Use BMAD (complex systems, enterprise scale)
   └─ NO → Continue...

2. Do you need persistent documentation?
   ├─ NO → Use AskUserQuestion (quick tasks, tactical questions)
   └─ YES → Continue...

3. Is this a brand new project (0→1)?
   ├─ YES → Choose between:
   │   • Spec-Kit (comprehensive, structured)
   │   • BMAD (if need multi-agent team)
   └─ NO → Continue...

4. Are you modifying existing code (1→n)?
   ├─ YES → Use OpenSpec (brownfield, change tracking)
   └─ MAYBE → Evaluate complexity:
       • Simple feature → OpenSpec
       • Complex system → BMAD
       • Need governance → Spec-Kit
```

---

## General Principles

1. **Your spec is your new source code** — invest in writing clear specifications
2. **Start simple, build incrementally** — ask AI to "keep it simple" and "implement the simplest next step I can test"
3. **Use visual context** — a picture is worth a thousand words to AI
4. **Be specific with instructions** — talk to it like a team member, be precise
5. **Context is everything** — invest in building comprehensive understanding upfront
6. **Fight over-engineering** — AI tries to write enterprise-grade software; push for basic working code first, then incrementally improve
7. **Prefer TDD** — test-driven development gives AI a verification loop
8. **Give AI a way to verify its work** — feedback loops 2-3x the quality of final results

---

## Thinking Levels

These keywords map to increasing levels of thinking budget:

| Keyword | Thinking Level |
|---------|---------------|
| `think` | Basic |
| `think hard` | Extended |
| `think harder` | Deep |
| `ultrathink` | Maximum |

Use higher levels for: architectural decisions, complex debugging, multi-file refactors. Use lower levels for: simple edits, straightforward tasks.

---

## Prompt Engineering

### Use XML Tags for Structure

When prompts involve multiple components (context, instructions, examples), XML tags help AI parse them more accurately:

```xml
<context>
  Current codebase uses React with TypeScript...
</context>

<instructions>
  Add authentication to the /api/users endpoint...
</instructions>

<examples>
  See src/api/posts.js for a similar pattern...
</examples>

<use_parallel_tool_calls>
  If you intend to call multiple tools and there are no dependencies
  between the calls, make all independent calls in parallel.
</use_parallel_tool_calls>
```

### Tell AI What To Do (Not What Not To Do)

Positive instructions are clearer and more actionable than negative ones.

### Avoid "Production-Ready" Prompts

Asking for "production-ready" code often leads to over-engineering. Be specific about what you actually need.

### Avoid Backward Compatibility Unless Needed

AI tends to preserve old code unnecessarily. Be explicit when old code should be removed.

---

## Research Tasks

For complex research, use a structured approach:

1. **Define clear success criteria** — what constitutes a successful answer
2. **Encourage source verification** — verify across multiple sources
3. **Track competing hypotheses** — develop several and track confidence levels
4. **Self-critique regularly** — update research notes for transparency

**Sample research prompt:**
> Search for this information in a structured way. As you gather data, develop several competing hypotheses. Track your confidence levels in your progress notes. Regularly self-critique your approach. Update a hypothesis tree or research notes file. Break down this complex research task systematically.

---

## Spec-Driven Development

1. Start from high-level requirements
2. Nail down proper specs with designs before writing code
3. Create tests before starting implementation
4. Track tests in a structured format

### Setup Scripts

Create setup scripts (e.g., `init.sh`) to gracefully start servers, run test suites, and linters. This prevents repeated work when continuing from a fresh context window.

### Progress Tracking

Maintain a structured todo or progress list:

```
// tests.json     — structured test state
// progress.txt   — progress notes
```

---

## The Eval Development Workflow

Testing AI outputs follows an iterative cycle:

1. **Define success criteria** — what makes a good response?
2. **Create test cases** — build a dataset of inputs and expected outputs
3. **Run evaluations** — test prompts systematically
4. **Analyze results** — identify patterns in failures
5. **Refine** — improve prompts based on insights
6. **Repeat** — keep iterating

The best way to improve LLM/agent output: **evaluate the output with another agent and repeat until quality exceeds your threshold.**

---

## Context Engineering (PRP Framework)

The PRP (Product Requirements Prompt) framework — a 3-step strategy:

```bash
# 1. Define requirements with examples and context in INITIAL.md
# 2. Generate a comprehensive PRP
/generate-prp INITIAL.md
# 3. Execute the PRP to implement the feature
/execute-prp PRPs/your-feature-name.md
```

### What to Include in Requirements

```markdown
## FEATURE
Build a user authentication system

## EXAMPLES
- Authentication flow: `examples/auth-flow.js`
- Similar API endpoint: `src/api/users.js`
- Database schema pattern: `src/models/base-model.js`

## DOCUMENTATION
- JWT library docs: https://github.com/auth0/node-jsonwebtoken
- Our API standards: `docs/api-guidelines.md`

## CONSIDERATIONS
- Use existing error handling patterns
- Follow standard response format
- Include rate limiting
```

**The more specific examples you provide, the better AI matches your existing patterns.**

---

## Multi-Agent Workflow

Use specialized agents for different roles:

1. **Co-founder** — idea discussion
2. **Domain Expert** — subject matter expertise
3. **PM** — product management
4. **Architect** — system design
5. **UI Designer** — interface design
6. **Backend Dev** — implementation
7. **Code Reviewer** — quality assurance

### Parallel Development with Git Worktrees

AI coding assistants are non-deterministic. Run multiple attempts to increase success probability:

```bash
# Create worktrees for different features
git worktree add ../project-auth feature/auth
git worktree add ../project-api feature/api

# Launch AI in each worktree
cd ../project-auth && claude  # Terminal 1
cd ../project-api && claude   # Terminal 2
```

Compare implementations, test each one, merge the best.

---

## Safe Experimentation with Dev Containers

Use Docker dev containers for YOLO mode:

1. Open project in VS Code → `F1` → "Dev Containers: Reopen in Container"
2. Run `claude --dangerously-skip-permissions` inside the container
3. Network isolation, no host filesystem access, restricted outbound connections

Test dangerous operations safely without risking your system.
