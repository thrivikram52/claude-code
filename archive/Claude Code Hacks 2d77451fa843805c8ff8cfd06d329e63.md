# Claude Code Hacks

### Concepts

1. Prompt: We know
2. **Slash commands** like /security-scan, frequently used prompts
3. Tools: Bash commands, git commands…
4. MCP : MCP operation
5. Skills : Claude Skills are reusable instruction sets (stored as markdown files) that extend Claude's capabilities with specialized knowledge, workflows. When a skill includes a script file (like a Python or shell script), the SKILL.md should instruct Claude to **execute the script using bash_tool** rather than just reading its contents with the view tool 
    
    pdf/ Skill.  **Description which is in [Skill.md](http://Skill.md) is loaded at system prompt. It’s part of every message.** 
    ├── [SKILL.md](http://skill.md/)              # Main instructions **(loaded when triggered),** 
    ├── [FORMS.md](http://forms.md/)              # Form-filling guide **(loaded as needed)**
    ├── [reference.md](http://reference.md/)          # API reference (loaded as needed)
    ├── [examples.md](http://examples.md/)           # Usage examples (loaded as needed)
    └── scripts/
    ├── analyze_form.py   # Utility script **(executed, not loaded)**
    ├── fill_form.py      # Form filling script
    └── [validate.py](http://validate.py/)       # Validation script
    
6. Agent: Prompt + Tools + MCP + Skills
7. Subagent: Runs its own context window. It can has it’s own Prompt + Tools + MCP + Skills. Main Agent delegate the task to subagent. Main Agent can trigger upto 4 sub agents
8. Hooks: hook events that run at different points in the workflow, example: before running a tool or MCP
    1. use /**hookify** command to create hooks with natural conversation without directly writing json
9. Plugins : Custom collections of slash commands, agents, MCP servers, skills and hooks that install with a single command, **Practical Example**
    - A security plugin might contain:
    - A **security-auditor subagent** (specialized AI for security reviews)
    - **MCP servers** to connect to vulnerability databases
    - So plugins are the **packaging system**, while agents are one of the **components** that can be inside a plugin!
    - They can be shared easily and publish to market place and easy to share it with teams..
10. Marketplace: where you can view or list your own plugins. Which can be installed by anyone
11. Agent SDK: Run Claude Code programmatically
12. Scopes: 
    
    5 different scope: When the same setting is configured in multiple scopes, more specific scopes take precedence:
    
    - **Enterprise** (highest) - can’t be overridden by anything, System-level managed-settings.json, All users on the machine will have the access
    - **Command line arguments** - temporary session overrides,
    - **Local** - overrides project and user settings. Store on .claude/*.local.* files, You, in this repository only has the access and its not pushed to git
    - **Project** - overrides user settings, and stored on .claude/ in repository,All collaborators on this repository, committed to git
    - **User** (lowest) - applies when nothing else specifies the setting, ~/.claude/ directory, You, across all projects
    - For example, if a permission is allowed in user settings but denied in project settings, the project setting takes precedence and the permission is blocked.
13. LSP, language server protocal 
14. Async operations with subagents
15.  Claude code on web or mobile or slack: super powerful, connect with GitHub apps. Code runs on antropic cloud and push the code to GH
16. Sandbox :  More controlled bash commands are allowed and no need to give approval for each request, Cannot download malicious scripts from unauthorized domains, Cannot contact any domains not explicitly allowed. When Claude Code attempts to access network resources outside the sandbox, The operation is blocked at the OS level

### Tips

1. Use keyword “**ultrathink**” for extended thinking
2. Keep your plugins in GitHub, and add it to Marketplace on claude
3. [https://claudemarketplaces.com/](https://claudemarketplaces.com/) discover market places
4. Global ~/.claude settings and ./claude/ project level settings, Local scope(current session), project scope , global scope., priority order.
5. Name each session, using **/rename session-name**, so that it can be resumed using **/resume session-name** when needed with that name.
6. Git worktree, create different folders, parallel coding sessions
7. Enterprise claude, deployed on cloud like AWS, GCP, Azure…. Monitor the requests and usage
8. Type / to see all available commands, including those from MCP servers. MCP prompts appear with the format /mcp__servername__promptname
9. Run 5 Claudes in parallel in my terminal. I number my tabs 1-5, and use system notifications to know when a Claude needs input. Simple ones run on web mobile app.
10. I use Opus 4.5 with thinking for everything. It's the best coding model I've ever used, and even though it's bigger & slower than Sonnet, since you have to steer it less and it's better at tool use, it is almost always faster than using a smaller model in the end.
11. Claude.md
    1. Push Claude.md file to git so that teams can use
    2. **Single [CLAUDE.md](https://claude.md/)** - Everything in one file (user or project level)
    3. **Rules folder -** Modular `.md` files grouped by concern
12. Automate github workflow and during code review on git tab @claude
13. Most sessions start in Plan mode (shift+tab twice). If my goal is to write a Pull Request, I will use Plan mode, and go back and forth with Claude until I like its plan. From there, I switch into auto-accept edits mode and Claude can usually 1-shot it. A good plan is really important!
14. After plan mode, keep it in auto accept mode.
15. Keep frequently used slash commands and sub agents
16. We use a PostToolUse hook to format Claude's code. Claude usually generates well-formatted code out of the box, and the hook handles the last 10% to avoid formatting errors in CI later.
17. Give access to all the tools and MCPs
18. Long running jobs run in background
19. the most important thing to get great results out of Claude Code -- give Claude a way to verify its work. If Claude has that feedback loop, it will 2-3x the quality of the final result.
20. Create a skill /reflect its used to summarise the current session conversations and improve the used skills(Ex code review), instead of you update the skills, this will update and push to git.
21. The repo also includes a `/learn` command you can run mid-session when you've just solved something non-trivial. It prompts you to extract the pattern right then, drafts a skill file, and asks for confirmation before saving

### Commands

1. /Init reads the current directory and create [CLAUDE.md](http://CLAUDE.md) file for this repo.
2. Ctrl+0 : To view Claude thinking verbose 
3. shift + tab : Plan mode 
4. claude - p : Claude headless mode. Ex: claude -p "Summarize this project" --output-format json , CLI and output as json
5. /settings : claude settings
6. claude --chrome , claude --dangerously-skip-permissions
7. claude --resume abc123 --fork-session, resume with forked session
8. claude --system-prompt "You are a Python expert"
9. claude --append-system-prompt "Always use TypeScript and include JSDoc comments"
10. npx skills add [https://github.com/vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills) --skill vercel-react-best-practices add any skills directly
11. 

### Claude code SDK

1. **Memory tool** enables Claude to store and retrieve information outside the context window
2. **Prompt caching:** Reduces the money and latency. It is used when the input prompt is identical and need to use several times.
3. **Context editing:** You can manage the context window by removing older messages..thinking blocks or …other strategies to select which part of the context has to be removed. You can even summarise at client side to compact the conversation too.
4. **Extended thinking:** Extended thinking gives Claude enhanced reasoning capabilities for complex tasks
5. **Batch processing:** If not real time and cost is important. Send the batch request and poll the results after an hr.
6. **structured output :**You can define the structured output schema, claude follows it when it sends the response.
7. **Tools**:  Tool calling (also called "function calling") is Claude's ability to call a function(your defined function) and get the result. 
8. **Tool Runner:** The tool runner provides an out-of-the-box solution for executing tools with Claude. Instead of manually handling tool calls, tool results, and conversation management, the tool runner automatically:
9. **Fine-grained tool streaming:** Get partial results of the tools execution using streams
10. **Tool search tool** enables Claude to work with hundreds or thousands of tools by dynamically discovering and loading them on-demand. Instead of loading all tool definitions into the context window upfront. Context bloat from tool definitions → Tool Search Tool
11. **Bash tool :** 
    - **You maintain the bash process** - You're responsible for creating and managing an actual bash subprocess
    - **Claude sends commands** - Claude decides what command to run and returns it in a tool_use block
    - **You execute and return output** - You run the command in your bash session and send stdout/stderr back to Claude
    - Unlike claude code mangest the terminal sessions, in this case you have to write the code.
12. **Code Execution Tool:**The code execution tool is a **managed, sandboxed environment** that Anthropic hosts. Unlike the bash tool where you maintain the session, here **Anthropic runs everything** for you.
13. **Code Execution Adv:** The agent can write code to interact with MCP servers. Agents can load only the tools they need and process data in the execution environment before passing results back to the model. **LLM —> Agent —>MCP Server**
14. **Programatic tool:**  Programmatic tool calling allows Claude to write code that calls your tools programmatically within a [code execution](https://platform.claude.com/docs/en/agents-and-tools/tool-use/code-execution-tool) container, rather than requiring round trips through the model for each tool invocation. 
Large intermediate results polluting context → Programmatic Tool Calling
    
     When working with large datasets, agents can filter and transform results in code before returning them.
    
     The agent harness can tokenize sensitive data automatically. For example, imagine you need to import customer contact details from a spreadsheet into Salesforce. The agent writes the code and the MCP client intercepts the data and tokenizes PII before it reaches the model:
    
15. Parameter errors and malformed calls → Tool Use Examples, you give examples API usage on the tool. Ex: Date is in which format…Instead of claude assuming with variations, you can give examples to claude to use the same.
    
    ![image.png](Claude%20Code%20Hacks/image.png)
    
16. **Computer Tool: THE COMPLETE FLOW DIAGRAM**

```
┌─────────────────────┐
│  YOUR COMPUTER      │
│  invoice_automation │ ──API call──> ┌──────────────┐
│  .py running        │               │ CLAUDE API   │
└─────────────────────┘               │ (Anthropic)  │
         ↓ HTTP                        └──────────────┘
         ↓                                     ↑
┌─────────────────────┐                       │
│  DOCKER CONTAINER   │                       │
│  ┌───────────────┐  │                       │
│  │ Tool Executor │  │ ──sends screenshots───┘
│  │ (computer.py) │  │
│  └───────────────┘  │ ←─receives tool requests
│         ↓           │
│   Uses Xlib to:     │
│   - Click mouse     │
│   - Type keyboard   │
│   - Capture screen  │
│         ↓           │
│  ┌───────────────┐  │
│  │ X11 Server    │  │
│  │ (Xvfb :1)     │  │
│  └───────────────┘  │
│         ↓           │
│  ┌───────────────┐  │
│  │ Virtual       │  │
│  │ Desktop       │  │
│  │ - Firefox     │  │ ← Gmail loads here
│  │ - QuickBooks  │  │ ← Data entered here
│  │ - PDF Viewer  │  │ ← PDFs open here
│  └───────────────┘  │
└─────────────────────┘
```

1. **Progressive disclosure:** Read tool definitions on-demand, rather than reading them all up-front.

### General Coding Guidlines / prompt suggestions

1. Ask Claude to create tests before starting work and keep track of them in a structured format
2.  Claude to create setup scripts (e.g., `init.sh`) to gracefully start servers, run test suites, and linters. This prevents repeated work when continuing from a fresh context window.
3. Clearly maintain todo or progress list in a new file.  
    
    Ex: `// Structured state file (tests.json)` // Progress notes (progress.txt)
    
4. Tell Claude what to do instead of what not to do
5. **Research tasks**
    1. **Provide clear success criteria**: Define what constitutes a successful answer to your research question
    2. **Encourage source verification**: Ask Claude to verify information across multiple sources
    3. **For complex research tasks, use a structured approach**: 
    4. **Sample Prompt:** Search for this information in a structured way. As you gather data, develop several competing hypotheses. Track your confidence levels in your progress notes to improve calibration. Regularly self-critique your approach and plan. Update a hypothesis tree or research notes file to persist information and provide transparency. Break down this complex research task systematically.
        
        <use_parallel_tool_calls>
        If you intend to call multiple tools and there are no dependencies between the tool calls, make all of the independent tool calls in parallel. Prioritize calling tools simultaneously whenever the actions can be done in parallel rather than sequentially. For example, when reading 3 files, run 3 tool calls in parallel to read all 3 files into context at the same time. Maximize use of parallel tool calls where possible to increase speed and efficiency. However, if some tool calls depend on previous calls to inform dependent values like the parameters, do NOT call these tools in parallel and instead call them sequentially. Never use placeholders or guess missing parameters in tool calls.
        </use_parallel_tool_calls>
        
    
    6. Claude has tendency to over engineer by creating extra files, adding unnecessary abstractions, or building in flexibility that wasn't requested. Make sure to write the requirements clearly on the prompt
    
    1. When your prompts involve multiple components like context, instructions, and examples, XML tags can be a game-changer. They help Claude parse your prompts more accurately, leading to higher-quality outputs.
    2. Reflection: Ask Claude to verify its work with a simple test before declaring a task complete, evaluating with success criteria is important
    3. Secs Driven development: Based on the high level requirements, nail down the proper specs with designs.
    4. **The Eval Development Workflow. Claude consoles platform has this feature**
        
        Testing follows an iterative cycle:
        
        1. **Define success criteria** - What makes a good response?
        2. **Create test cases** - Build a dataset of inputs and expected outputs
        3. **Run evaluations** - Test your prompts systematically
        4. **Analyze results** - Identify patterns in failures
        5. **Refine** - Improve prompts based on insights
        6. **Repeat** - Keep iterating

11. The best way to improve the output of a LLM/agent is evaluate the output with other agent and repeat the process till you get the probability(quality) more than certain threashold.

1. These specific phrases are mapped directly to increasing levels of thinking budget in the system: "think" < "think hard" < "think harder" < "ultrathink." Each level allocates progressively more thinking budget for Claude to use.

### Architectures

1. Research with multi Agent 

![image.png](Claude%20Code%20Hacks/image%201.png)

[Vibe Coding techniques](https://www.notion.so/Vibe-Coding-techniques-2e07451fa843804b8925ce9534dc50fb?pvs=21)