# Workflow: Structured Creative Session

Follow these steps to conduct a comprehensive brainstorming session.

## Phase 1: Preparation & Context
1.  **Define the Goal**: State the objective in one sentence.
2.  **Context Injection**: Search [Knowledge Items (KIs)](./../resources/context-integration.md) and past logs for relevant project constraints and user preferences.
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

## Phase 4: Output (Deep Dive & Persistence)
1.  **Detailed Proposals**: Elaborate *only* on the ideas selected by the user in Phase 3.
2.  **Human Review**: Present the detailed findings to the user.
3.  **Persist to Disk**: Create a new `.md` file in the root `docs/` folder (e.g., `docs/brainstorming/[project]-plan.md`) using the [Brainstorm Template](./../resources/brainstorm-template.md). This ensures the work is available for future AI sessions and user reference.
4.  **Action Plan**: Define next steps for the selected path.

## Phase 5: Iteration & Pivoting
1.  **Feedback Analysis**: If the user dislikes all options, analyze why.
2.  **Pivot**: Change a core assumption or constraint and **loop back to Phase 2**.
3.  **Refine**: If the user likes an idea but has caveats, adjust the idea and re-present it.

## Failure & Stuck States
- If Phase 2 fails to produce meaningful ideas, **STOP** and ask the user for a new perspective or more context.
- If Phase 3 results in no "Quick Wins", consider "Big Bets". Always present the **easiest-to-build** impactful ideas first to the user.
