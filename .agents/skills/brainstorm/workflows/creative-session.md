# Workflow: Structured Creative Session

Follow these steps to conduct a comprehensive brainstorming session.

## Phase 1: Preparation & Context
1.  **Define the Goal**: State the objective in one sentence.
2.  **Expansion vs. New**: Check `docs/` for existing ideas. 
    - *If existing ideas found*: Ask: "I see existing brainstorming docs for [Idea Names]. Do you want to expand one of these or start something new?" **Wait for response.**
    - *If NO existing ideas found*: Automatically proceed to Step 3 and Phase 2 to suggest completely new ideas. Do not ask for permission to start suggesting.
3.  **Context Injection**: Search [Knowledge Items (KIs)](./../resources/context-integration.md) and past logs for relevant project constraints and user preferences.
3.  **List Constraints**: Clearly state what's *not* possible to focus the search space.

## Phase 2: Divergence (Idea Generation)
// turbo
1.  **Rapid Fire**: Spend 2-3 minutes generating raw ideas. List them without judgment.
2.  **Persona Pressure**: Apply [Personas](./../resources/personas.md) (e.g., The Skeptic, The Visionary) to challenge the ideas.
3.  **AI Pitfall Check**: Use [AI Pitfalls](./../resources/ai-pitfalls.md) to replace generic ideas.
4.  **Blue Ocean Lens**: Specifically look for industries with **low tech adaptation** where **high-tech solutions** could be disruptive (e.g., AI in traditional agriculture, Blockchain in logistics).
5.  **Analogous Thinking**: Look at how unrelated industries would solve this.

## Phase 3: Selection & Narrowing (Search Space Management)
1.  **Concise Shortlist**: Select 3-5 of the most promising ideas.
2.  **Human Selection**: **STOP** and present these concise ideas (name + 1-sentence description) to the user.
3.  **Refinement**: Ask the user which ideas they want to explore in depth or if they want to narrow the criteria.

## Phase 4: Output (Deep Dive & Comprehensive Documentation)
1.  **Selection Deep Dive**: Elaborate *only* on the ideas selected by the user in Phase 3.
2.  **Human Review**: Present the detailed findings to the user for final verbal approval.
3.  **Comprehensive Documentation**: Create the fixed folder structure in the root `docs/` folder:
    - **Master Strategy**: `docs/<idea-name>/plan.md`, `docs/<idea-name>/summary.md` etc.
    - **Execution (v1)**: `docs/<idea-name>/v1/features.md`, `v1/plan.md`, `v1/todo.md`, `v1/summary.md` etc.
4.  **Content Depth**: Ensure each file contains detailed explanations, feature breakdowns, and actionable roadmaps using the [Brainstorm Template](./../resources/brainstorm-template.md) as a base. Explain the idea as much as possible in these docs.
5.  **Action Plan**: Define immediate next steps to begin Milestone 1 of the implementation plan.

## Phase 5: Iteration & Pivoting
1.  **Feedback Analysis**: If the user dislikes all options, analyze why.
2.  **Pivot**: Change a core assumption or constraint and **loop back to Phase 2**.
3.  **Refine**: If the user likes an idea but has caveats, adjust the idea and re-present it.

## Failure & Stuck States
- If Phase 2 fails to produce meaningful ideas, **STOP** and ask the user for a new perspective or more context.
- If Phase 3 results in no "Quick Wins", consider "Big Bets". Always present the **easiest-to-build** impactful ideas first to the user.
