# Aspen (agent)
### A watered idea blooms

#Aspen

`note: this is a readme from a codebase, so some of the stuff doesn't actually link out to anything. but you get the idea from the content and descriptions`

## Tool Summary
Aspen is a modular, locally running AI agent focused on developer workflows. It uses local models for cost-efficiency, security, and privacy, with optional (and occasional) cloud access for complex task planning, sanity checks, high level reasoning about the codebase, etc. But the core idea is that if a local model is allowed to run for long enough and try enough times in enough different ways, it will eventually solve the problem.

Aspen features a dispatch layer that routes tasks to different models (currently using CrewAI for this). It is designed to be autonomous, handling task design and execution, errors, and retries by itself — including self-correcting behaviors like clearing context when stuck. Our theory here is that there are probably 8-10 categories of problems that leading agents typically get stuck on, and for each of those, there are usually a few common solutions. Aspen will *start* with all of these categories and solutions pre-defined, in addition to **learning from what actually works over time across projects.**

Users can walk away and let Aspen work for hours, checking in only at major milestones. Imagine a user entering a prompt before they leave for work, and by the time they get home, Aspen is way beyond an MVP. Aspen can reach out to the user via Discord or Slack to ask questions if it really gets stuck. The user can reply with text, audio, screenshots, & links to help Aspen figure out how to move forward.

Aspen may (in the future) include an auto-update system that monitors model releases and swaps in better models automatically (if enabled), while keeping rollback options available.

The whole architecture is modular, extensible, and hot-swappable, allowing easy updates without major rebuilds. **The core Aspen agent is open source and will be free forever.** We will introduce optional **paid** features that enhance the user experience as the project develops.

## Technical Details
To make all of this work, there are several components and layers that need to be built.

### Tool Layer
Aspen will be a local program that you run from the terminal and the UI can be opened in a browser. It will need a pretty chunky toolbelt to actually be useful. Some common tools that existing CLI agents have are:

| Tool                | Description                                           | Permission Required |
|---------------------|-------------------------------------------------------|---------------------|
| AgentTool           | Runs a sub-agent to handle complex, multi-step tasks   | No                  |
| BashTool (Bash)     | Executes shell commands in your environment            | Yes                 |
| GlobTool            | Finds files based on pattern matching                  | No                  |
| GrepTool            | Searches for patterns in file contents                 | No                  |
| LSTool              | Lists files and directories                            | No                  |
| FileReadTool (View) | Reads the contents of files                            | No                  |
| FileEditTool (Edit) | Makes targeted edits to specific files                 | Yes                 |
| FileWriteTool (Replace) | Creates or overwrites files                      | Yes                 |
| NotebookEditTool    | Modifies Jupyter notebook cells                        | Yes                 |
| NotebookReadTool    | Reads and displays Jupyter notebook contents           | No                  |
| WebFetchTool        | Fetches content from a specified URL                   | Yes                 |

*source: https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview*

This is a good start, but Aspen will need several more:
- Ability to run a local web server
- Ability to take screenshots and review them
- Ability to use existing unit test frameworks
- Ability to search the web
- Ability to analyze PDF files

Essentially, any tools that a developer uses on a daily basis should be accessible to Aspen. Aspen should not have to constantly be asking for permission to do things - the user should sandbox Aspen as needed. This will be explained to users in the docs. Aspen will be treated with the same level of trust as a human developer, and the user can decide how much access to give Aspen on their own machine.

### Dispatch Layer
This is the core routing layer that decides which model to use for a given task. CrewAI has a lot of helpful code ready to deploy here.

### Model Layer
Testing will dictate the most optimal configuration here, but our first idea is use several small models for specific roles in the development process and one larger model for in depth code generation, project orchestration, etc. Then, the dispatcher can decide to call an API model every now and then to check in on the project, make sure the overall plan is still on track, etc. Again, CrewAI's existing APIs will be used for the time being to get something working up quickly.

### Frontend
Aspen will offer a polished browser-based UI called Aspen Grove with a lot of helpful features and tools. This will be a paid feature after beta.

---

## University
Currently, the codebase is very empty. We have some very helpful research though (not included in git repo):

1. [codex-cli](university/codex): an open source CLI agent that OpenAI released
2. [A Practical Guide to Building Agents](university/practical-guide-to-building-agents.md): an overview of how to build an agent, also by OpenAI
3. **Very valuable**: [System prompts from all leading AI agents](university/system-prompts-and-models-of-ai-tools): a directory containing the current system prompts for all leading AI agents, organized by the tool name or model name. this will be hugely helpful for quickly designing a very effective system prompt(s) for Aspen.
5. [CrewAI Docs](university/crewai-docs): A ton of documentaion from CrewAI to help us implement their framework effectively

We will be using all these resources as a guide to build Aspen.

## Stack
- Model: Models like the Qwen 3 series (just released) and Phi4 for multimodal abilities
- Agent framework: CrewAI (also researching Qwen-Agent; downside is it's less optimized for other models)
- Python manager: uv
- Frontend: Aspen Grove (see overview file)
- Backend: Python server