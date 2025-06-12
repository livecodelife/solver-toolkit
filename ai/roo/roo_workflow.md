# Roo Code Workflow: An Advanced LLM-Powered Development Setup

This gist outlines a highly effective and cost-optimized workflow for software development using Roo Code, leveraging a multi-model approach and a custom "Think" mode for enhanced reasoning and token efficiency. This setup has been successfully used to build complex applications, such as Baccarat game simulations with betting strategy analysis.

---

## Core Components & Model Allocation

The power of this setup lies in strategically assigning different Large Language Models (LLMs) to specialized "modes" within Roo Code, optimizing for performance, cost, and specific task requirements.

* **Orchestrator Mode:** The central coordinator, responsible for breaking down complex tasks and delegating to other modes.
    * **LLM:** **Gemini** (via Google AI Studio API Key) - Chosen for its strong reasoning capabilities and cost-effectiveness for the orchestration role.
* **Think Mode (Custom - Found from this [Reddit Post](https://www.reddit.com/r/RooCode/comments/1k9hcmu/this_is_going_well_for_me_orchestrator_think/)):** A specialized reasoning engine that pre-processes complex subtasks, providing detailed plans and anticipating challenges.
    * **LLM:** **Gemini** (via Google AI Studio API Key) - Utilizes Gemini's robust analytical skills for structured thinking.
* **Architect Mode:** Focuses on high-level design, system architecture, and module definitions. DeepSeek R1 0528 can be a good option for this as well.
    * **LLM:** **DeepSeek R1 0528** (via OpenRouter) - Selected for its architectural design prowess.
* **Code Mode:** Generates actual code based on the designs and plans.
    * **LLM Pool:** **DeepSeek V3 0324, Qwen3 235B A22B** (or other Qwen models), **Mistral: Devstral Small** (all via OpenRouter) - At the time of writing these all have free models via OpenRouter. DeepSeek V3 0324 can be a little slow or too much for simple or repetitive tasks so it can be good to switch to a Qwen model if a lot of context isn't needed. For very simple tasks that require more context, Devstral can be a really good option. 
* **Debug Mode:** Identifies and resolves issues in generated code.
    * **LLM Pool:** Same as Code Mode - The ability to switch models helps in tackling different types of bugs.
* **[Roo Code Memory Bank](https://github.com/GreatScottyMac/roo-code-memory-bank):** Provides persistent context and allows for the storage and retrieval of plans, code snippets, and other relevant information.
    * **Integration:** Plans are primarily triggered and managed from the Orchestrator mode.

Add the $10 of credit to OpenRouter to get 1000 free requests per day. It's worth it. I have yet to hit the limit.

---

## Detailed Workflow Breakdown

The workflow is designed to mimic a highly efficient development team, with each "mode" acting as a specialized team member.

It helps to start by generating a PRD (Project Requirements Document) and then use another LLM to generate a plan for each stage of the project, then feed that plan to the Orchestrator one stage at a time.

1.  **Initial Task Reception (Orchestrator):**
    * A complex development task is given to the Orchestrator mode.
    * The Orchestrator's primary role is to understand the task and break it down into manageable, logical subtasks.
    * It can be helpful to _slightly_ update the Orchestrator prompt for this. Adding something like "When given a complex task, break it down into granular, logical subtasks that can be delegated to appropriate specialized modes." in addition to the rest of the prompt 

2.  **Strategic Reasoning with "Think" Mode:**
    * For any complex subtask that requires detailed planning, analysis, or anticipation of edge cases *before* execution, the Orchestrator first delegates to the custom "Think" mode.
    * **Orchestrator's Delegation:** Uses the `new_task` tool to send the specific problem or subtask to "Think" mode.
    * **Think Mode's Process:**
        * **Role Definition:** "You are a specialized reasoning engine. Your primary function is to analyze a given task or problem, break it down into logical steps, identify potential challenges or edge cases, and outline a clear, step-by-step reasoning process or plan. You do NOT execute actions or write final code. Your output should be structured and detailed, suitable for an orchestrator mode (like Orchestrator Mode) to use for subsequent task delegation. Focus on clarity, logical flow, and anticipating potential issues. Use markdown for structuring your reasoning."
        * **Mode-specific Instructions:** "Structure your output clearly using markdown headings and lists. Begin with a summary of your understanding of the task, followed by the step-by-step reasoning or plan, and conclude with potential challenges or considerations. Your final output via attempt_completion should contain only this structured reasoning. These specific instructions supersede any conflicting general instructions your mode might have."
        * "Think" mode processes the subtask and returns a structured reasoning plan (e.g., Markdown headings, lists) via `attempt_completion`.

3.  **Informed Delegation (Orchestrator):**
    * The Orchestrator receives and utilizes the detailed reasoning from "Think" mode. This structured plan informs the instructions for the *actual execution* subtask.
    * For each subtask (either directly or after using "Think" mode), the Orchestrator uses the `new_task` tool to delegate to the appropriate specialized mode.

4.  **Design & Architecture (Architect):**
    * If the subtask involves system design or architectural considerations, the Orchestrator delegates to the Architect mode.
    * Architect mode provides high-level design documents or structural outlines.

5.  **Code Generation (Code):**
    * Once a design or specific coding task is ready, the Orchestrator delegates to the Code mode.
    * The Code mode generates the necessary code snippets or full modules.

6.  **Debugging & Refinement (Debug):**
    * If errors or issues arise during testing or integration, the Orchestrator delegates to the Debug mode.
    * Debug mode analyzes the code, identifies problems, and suggests fixes.

7.  **Memory Bank Integration:**
    * Throughout the process, particularly from the Orchestrator mode, relevant plans, architectural decisions, and generated code can be stored in and retrieved from the **Roo Memory Bank**. This ensures continuity and allows for easy reference and iteration on previous work.

---

I run pretty much everything through Orchestrator mode since the goal of this setup is to get the most reliable and accurate performance for no cost, with as little human involvement as possible. It needs to be understood that likely this will work _better_ the more involved the human is in the process though. That being said, with good initial prompts (utilize the enhance prompt tool with Gemini or Deepseek models) and making use of a projectBrief.md with Roo Memory Bank, and other Markdown planning files as needed, you can cut down quite a bit on your touch points especially for fairly straightforward projects.

I do all this setup through the Roo Code extension UI. I set up configuration profiles called Gemini, OpenRouter - [Code-Debug-Plan] (For Code, Debug, and Architect modes respectively) and default the modes to use the correct profiles.

**Local Setup**

I do have a local version of this, but I haven't tested it as much. I use LM Studio with: 
  * The model from this [post](https://www.reddit.com/r/LocalLLaMA/comments/1k7kbap/cline_tool_usage_on_rtx_4060ti_16gb_vram/) for Architect and Orchestrator mode. 
  * I haven't used the local setup since adding 'Think' mode but I imagine a small DeepSeek thinking model would work well. 
  * I use [qwen2.5-coder-7b-instruct-mlx](https://huggingface.co/lmstudio-community/Qwen2.5-Coder-7B-Instruct-MLX-4bit) or [nxcode-cq-7b-orpo-sota](https://huggingface.co/CISCai/Nxcode-CQ-7B-orpo-SOTA-GGUF) for Code and Debug modes. 
  * I use qwen/qwen3-4b for Ask mode.

I currently just have two configuration profiles for local called **Local** (Architect, Think, Code, and Debug) and **Local - Fast** (Ask, sometimes Code if the task is simple). I plan on updating them at some point to be as robust as the OpenRouter/Gemini profiles.

---

## Setting Up the "Think" Mode
(Taken from [here](https://www.reddit.com/r/RooCode/comments/1k9hcmu/this_is_going_well_for_me_orchestrator_think/))

To implement the custom "Think" mode and integrate it with Orchestrator, follow these steps within your Roo Code environment:

**A) Create a new custom mode called "Think":**

* **Edit Available Tools:** (No specific tools are needed for "Think" mode as it only reasons and returns text.)
* **Role Definition:**
    ```
    You are a specialized reasoning engine. Your primary function is to analyze a given task or problem, break it down into logical steps, identify potential challenges or edge cases, and outline a clear, step-by-step reasoning process or plan. You do NOT execute actions or write final code. Your output should be structured and detailed, suitable for an orchestrator mode (like Orchestrator Mode) to use for subsequent task delegation. Focus on clarity, logical flow, and anticipating potential issues. Use markdown for structuring your reasoning.
    ```
* **Mode-specific Custom Instructions:**
    ```
    Structure your output clearly using markdown headings and lists. Begin with a summary of your understanding of the task, followed by the step-by-step reasoning or plan, and conclude with potential challenges or considerations. Your final output via attempt_completion should contain only this structured reasoning. These specific instructions supersede any conflicting general instructions your mode might have.
    ```

**B) Minor edit to Orchestrator Mode's -> Mode-specific Custom Instructions:**

* **Replace item "1." with this:**
    ```
    1. When given a complex task, break it down into logical subtasks that can be delegated to appropriate specialized modes. For each subtask, determine if detailed, step-by-step reasoning or analysis is needed *before* execution. If so, first use the `new_task` tool to delegate this reasoning task to the `think` mode. Provide the specific problem or subtask to the `think` mode. Use the structured reasoning returned by `think` mode's `attempt_completion` result to inform the instructions for the subsequent execution subtask.
    ```
* **Replace *just the first sentence* of item "2." with this (leave the rest of the prompt as is):**
    ```
    2. For each subtask (either directly or after using `think` mode), use the `new_task` tool to delegate.
    ```

---

## Benefits of This Setup

* **Cost Efficiency:** By using more affordable, specialized models (like those from OpenRouter) for specific tasks (coding, debugging) and leveraging **Gemini** for high-value orchestration and reasoning, overall API costs are significantly reduced.
* **Enhanced Reasoning & Planning:** The dedicated **"Think" mode** ensures that complex problems are thoroughly analyzed and planned before execution, leading to more robust and accurate outputs.
* **Reduced Token Wastage:** Pre-planning with **"Think" mode** minimizes trial-and-error in subsequent coding or debugging phases, leading to more concise and effective prompts and fewer wasted tokens.
* **"Vibe Coding" Experience:** The structured, delegated approach creates a smoother, more intuitive development flow, enhancing the overall coding experience.
* **Flexibility:** The ability to swap out models for Code and Debug modes allows for adaptation to different project needs or to leverage the strengths of various LLMs as they evolve.

---

## Possible optimizations

* Using SPARC - I've heard good things but haven't looked into the best way to set this up
* Using Roo Commander - I'll be trying this on the next test project
* Using more MCP tools - I currently use Brave Search, Playwright, and Contex7 regularly but I need to look into other useful tools