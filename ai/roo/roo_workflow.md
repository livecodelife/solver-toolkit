# Roo Code Workflow: An Advanced LLM-Powered Development Setup

This gist outlines a highly effective and cost-optimized workflow for software development using Roo Code, leveraging a multi-model approach. This setup has been successfully used to build working applications, such as Baccarat game simulations with betting strategy analysis, and my personal portfolio [site](https://solverscorner.com).

---

## Core Components & Model Allocation

The power of this setup lies in strategically assigning different Large Language Models (LLMs) to specialized "modes" within Roo Code, optimizing for performance, cost, and specific task requirements.

* **Orchestrator Mode:** The central coordinator, responsible for breaking down complex tasks and delegating to other modes.
    * **LLM:** **Gemini** (via Google AI Studio API Key) - Chosen for its strong reasoning capabilities and cost-effectiveness for the orchestration role. I use 2.5 Pro until the free credits run out and then switch to 2.5 Flash
* **Architect Mode:** Focuses on high-level design, system architecture, and module definitions. Gemini can be a good option for this as well.
    * **LLM:** **DeepSeek R1 0528** (via OpenRouter) - Selected for the thinking mode and free option. I've actually stopped using Architect mode in favor of [Traycer](https://traycer.ai) instead since it integrates very well with Roo and I can trigger the planning first and pass it to the Orchestrator. Traycer has a decvent free tier and it's paid tier is reasonable.
* **Code Mode:** Generates actual code based on the designs and plans.
    * **LLM Pool:** **Kimi K2, DeepSeek V3 0324** (via OpenRouter) - At the time of writing these both have free models via OpenRouter. Kimi K2 is incredibly fast but has a very small context window. That shouldn't be an issue if the tasks passed to it are granular enough. DeepSeek V3 0324 can be a little slow and go rogue sometimes, but I've switched to it now and then if K2 gets stuck and then switched back once it's gotten out of the rut. 
* **Debug Mode:** Identifies and resolves issues in generated code.
     * **LLM:** **DeepSeek R1 0528** (via OpenRouter) Sometimes I'll switch to K2 or even Gemini if the problem is too complicated or I just want to see if another model has better luck.

Add the $10 of credit to OpenRouter to get 1000 free requests per day. It's worth it. I have yet to hit the limit.

---

## Detailed Workflow Breakdown

The workflow is designed to mimic a highly efficient development team, with each "mode" acting as a specialized team member and planning done through Architect or Traycer.

It helps to start by generating a PRD (Project Requirements Document) and then use another LLM to generate a plan for each stage of the project, then feed that plan to the Orchestrator one stage at a time. You can do this with Traycer's new Phases or through another LLM provider like ChatGPT or Claude

1.  **Planning:**
    * I've typically been generating a PRD through ChatGPT in thinking mode and refined it until I like what it has. Even better to generate specific technical milestones if the project is complex
    * You can add the PRD as a Markdown file in your project, or have ChatGPT generate tickets that you copy into GitHub. This would let you use Traycer's automated planning but I would make sure it's only allowed once you manually trigger it or on assignment for the ticket. Otherwise, it will generate plans for each ticket based on the initial state of the project instead of the state once previous tickets are completed
2.  **Initial Task Reception (Orchestrator):**
    * A complex development task is given to the Orchestrator mode (triggered through Traycer or manually).
    * The Orchestrator's primary role is to understand the task and break it down into manageable, logical subtasks.
    * It can be helpful to _slightly_ update the Orchestrator prompt for this. Adding something like "When given a complex task, break it down into granular, logical subtasks that can be delegated to appropriate specialized modes." in addition to the rest of the prompt 

3.  **Informed Delegation (Orchestrator):**
    * The Orchestrator receives and utilizes the detailed reasoning from Traycer or your manual input. This structured plan informs the instructions for the *actual execution* subtask.
    * For each subtask, the Orchestrator uses the `new_task` tool to delegate to the appropriate specialized mode.

4.  **Design & Architecture (Architect):**
    * If the subtask involves system design or architectural considerations, the Orchestrator delegates to the Architect mode.
    * Architect mode provides high-level design documents or structural outlines (I just let this happen if the Orchestrator mode happens to trigger it. If you aren't using Traycer, flip steps 2 and 3 and ask for a plan from Architect mode first)

5.  **Code Generation (Code):**
    * Once a design or specific coding task is ready, the Orchestrator delegates to the Code mode.
    * The Code mode generates the necessary code snippets or full modules.

6.  **Debugging & Refinement (Debug):**
    * If errors or issues arise during testing or integration, the Orchestrator delegates to the Debug mode.
    * Debug mode analyzes the code, identifies problems, and suggests fixes.

7. **Review (Optional but suggested):**
   * I used Traycer auto review during my free Pro trial and it was nice, but for a free option I just use the CodeRabbit extension and have it review my changes and trigger the tasks to fix any suggesions, starting the process over again

---

I run pretty much everything through Orchestrator mode since the goal of this setup is to get the most reliable and accurate performance for no cost, with as little human involvement as possible. It needs to be understood that likely this will work _better_ the more involved the human is in the process though. That being said, with good initial prompts (utilize the enhance prompt tool with Gemini or Deepseek models) and making use of a PRD.md and other Markdown planning files as needed, you can cut down quite a bit on your touch points especially for fairly straightforward projects.

I do all this setup through the Roo Code extension UI. I set up configuration profiles called Gemini, OpenRouter - [Code-Debug-Plan] (For Code, Debug, and Architect modes respectively) and default the modes to use the correct profiles.

**Local Setup**

I do have a local version of this, but I haven't tested it as much. I use LM Studio with: 
  * The model from this [post](https://www.reddit.com/r/LocalLLaMA/comments/1k7kbap/cline_tool_usage_on_rtx_4060ti_16gb_vram/) for Architect and Orchestrator mode. 
  * I use [qwen2.5-coder-7b-instruct-mlx](https://huggingface.co/lmstudio-community/Qwen2.5-Coder-7B-Instruct-MLX-4bit) or [nxcode-cq-7b-orpo-sota](https://huggingface.co/CISCai/Nxcode-CQ-7B-orpo-SOTA-GGUF) for Code and Debug modes. 
  * I use qwen/qwen3-4b for Ask mode.

I currently just have two configuration profiles for local called **Local** (Architect, Think, Code, and Debug) and **Local - Fast** (Ask, sometimes Code if the task is simple). I plan on updating them at some point to be as robust as the OpenRouter/Gemini profiles.
---

## Benefits of This Setup

* **Cost Efficiency:** By using more affordable, specialized models (like those from OpenRouter) for specific tasks (coding, debugging) and leveraging **Gemini** for high-value orchestration and reasoning, overall API costs are significantly reduced.
* **Enhanced Reasoning & Planning:** Creating a PRD beforehand and using something like Traycer ensures that complex problems are thoroughly analyzed and planned before execution, leading to more robust and accurate outputs.
* **Reduced Token Wastage:** Pre-planning minimizes trial-and-error in subsequent coding or debugging phases, leading to more concise and effective prompts and fewer wasted tokens.
* **"Vibe Coding" Experience:** The structured, delegated approach creates a smoother, more intuitive development flow, enhancing the overall coding experience.
* **Flexibility:** The ability to swap out models for Code and Debug modes allows for adaptation to different project needs or to leverage the strengths of various LLMs as they evolve.

---

## Possible optimizations

* Using SPARC - I've heard good things but haven't looked into the best way to set this up
* Using Roo Commander - I used this for a while and loved it but lately it's been less than useful and I've had better luck with the base modes alone.
* Using more MCP tools - I currently use Brave Search, Playwright, and Contex7 regularly but I need to look into other useful tools
