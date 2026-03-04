## General Tips

1. Your 'spec' is your new 'source code’
2. Start Simple and Build Incrementally : Ask AI to "Keep it simple" in your prompt. Ask AI to "Implement the simplest next step I can test.”
3. Use Visual Context: A picture is worth a thousand words to AI
4. Be Specific with Instructions: talk to like your team member, be precise
5. Context is everything - invest in building comprehensive understanding
6. Core Principle: Explore → Plan → Execute
    1. Explore
        - Use prompts like "prepare to discuss how our frontend works"
        - Claude will spend 50k+ tokens over 7 minutes understanding codebase
        - If overview is wrong, escape and restart (don't try to correct)
        - Ask follow-up questions to ensure proper context
    2. Plan Phase
        - Use "think hard" mode for complex planning
        - Break it to smaller tasks
        - Small tasks: Don't overthink, just code
        - Medium-large: Break into testable, deployable PRs
        - High risk: Take 2-3 planning attempts
    3. Execute Phase
        - Execute each task and mark it as complete
7. Claude try to write enterprise grade software, you have to fight with it, first write the basic working condition code and incrementally improve it.
8. Prefer TDD, test driven development
9. Default → Think → Think harder → Think Hardest → Ultra Think, improves LLM thinking capability
10. 

## Claude Code

1.  [CLAUDE.md](http://claude.md/) : Comprehensive Context 
    
    ```jsx
    # Project Guidelines
    - Use TypeScript for all code
    - Follow "Astro way" of doing things
    - Strict type checking required
    - Keep components simple and focused
    
    # Architecture Rules
    - All code goes in `src/` directory
    - Components in `src/components/`
    - Pages in `src/pages/`
    - Utils in `src/utils/`
    
    # Coding Standards
    - Explain what you'll do first and ask for confirmation
    - Do the simple thing first
    - Use modules instead of single files
    - Look for existing solutions before writing new code
    ```
    
2. /Clear the context window when starting new task
3. Leverage multiple Sub agents. 
    
    **SubAgent: Prompt + Tools + Permissions + model**
    
4. Use dangerous mode to for yolo mode, its not that dangerous
5. **Use Multiple agents( 7 Agents)**
    1. Co founder:  Idea discussion
    2. Domain Expert: Subject matter expert
    3. PM: Product manger
    4. Architect
    5. UI Designer
    6. Back end Designer
    7. Code Reviewer
6. Use [Claude.md](http://Claude.md) file on each folder and sub folder
7. Use [CHANGELOG.md](http://CHANGELOG.md) for all the changes
8. Use [Primer.md](http://Primer.md) for loading the entire context
9. keep the user stories or product specs or Arch in multiple files by splitting/sharing if one is too big
10. Version control git commands keep in commands
    1. /commit 
    2. /create-pr
    3. /fix-pr
    4. /husky
    5. ect….
11. /create-command: it should create new command
12. /create-doc : create any document
13. /mermaid: mermaid diagrams generator
14. prompts support Command line arguments
15. The **double escape and resume** technique is one of the most powerful workflow optimizations in Claude Code for managing context and creating efficient branching workflows.

##