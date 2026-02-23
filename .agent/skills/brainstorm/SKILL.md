---
name: brainstorm
description: A modular skill for structured brainstorming, ideation, and creative problem-solving.
---

# Brainstorm Skill

This skill provides a structured framework for generating new ideas, expanding features, and solving complex problems through creative thinking.

## Purpose
Use this skill when the user asks for:
- New product, feature, or business ideas (regardless of the user's existing domain).
- Creative solutions to complex problems in any industry.
- Exploring high-tech opportunities with low current adaptation.
- Structured brainstorming sessions.

## Skill Structure
The skill is modularized to provide specific guidance and examples:

- **Workflows**: Step-by-step guides for creative processes.
  - [Creative Session](./workflows/creative-session.md): A full structured brainstorming workflow.
- **Resources**: Tools and frameworks to aid ideation.
  - [Brainstorming Frameworks](./resources/frameworks.md): SCAMPER, First Principles, etc.
  - [Evaluation Frameworks](./resources/evaluation.md): SWOT, Six Thinking Hats, Impact/Effort Matrix.
  - [Overcoming Creative Blocks](./resources/creative-blocks.md): Reverse brainstorming, perspective shifts.
  - [Brainstorm Template](./resources/brainstorm-template.md): Standardized format for outcomes.
  - [Ideation Personas](./resources/personas.md): Perspectives like The Skeptic, The Novice, etc.
  - [Avoiding AI Pitfalls](./resources/ai-pitfalls.md): Breaking out of generic suggestions.
  - [Context Integration](./resources/context-integration.md): Using KIs and logs to ground ideas.
- **Examples**: Reference outcomes of brainstorming sessions.
  - [New Product Idea Example](./examples/new-product-idea.md).

## Strategy
1.  **Project Context**: Use [Context Integration](./resources/context-integration.md) to ground the session in KIs and logs.
    - **Expansion Check**: Before starting a new brainstorm, check if a `docs/` folder exists with previous ideas. If it does, **ask the user** if they want to brainstorm a *completely new* app idea or *expand/iterate*/create new version/edit on an existing project found in the docs.
2.  **Identify the Objective**: Clearly define the brainstorming goal.
3.  **Breadth First**: Use [Frameworks](./resources/frameworks.md) and [Personas](./resources/personas.md) to generate many concise ideas.
4.  **Avoid Blandness**: Apply [AI Pitfalls](./resources/ai-pitfalls.md) strategies to ensure unique ideas.
5.  **Narrow the Search**: Present 3-5 concise ideas to the user for selection.
6.  **Human Selection**: **Wait** for user input before proceeding to depth.
7.  **Depth Next**: Deep-dive only into the selected idea(s) using the [Creative Session](./workflows/creative-session.md) workflow, which includes comprehensive documentation.
8.  **Evaluate & Iterate**: Use [Evaluation Frameworks](./resources/evaluation.md). If results are poor, use [Creative Blocks](./resources/creative-blocks.md) to pivot and loop back to step 3.
9.  **Deliver Results**: Use the [Brainstorm Template](./resources/brainstorm-template.md) for the final output.

- **Guidelines**
- **Domain Expansion**: You are *not* restricted to the user's current known domain. Explore new industries and markets freely.
- **Priority: Lean Tech Innovation**: Give highest priority to ideas that have **high tech dependency** (require advanced technology) but **low current tech adaptation**, while also being **low in implementation difficulty**.
  - *Note*: Prioritize from **low to high difficulty**. A "Quick Tech Win" (Low Difficulty + High Leverage) is always preferred over a "Big Bet" (High Difficulty + High Leverage).
- **Narrow the Scope Early**: Avoid deep-diving into a single idea until the user has seen and approved a concise shortlist.
- **Quantity over Quality (initially)**: Generate as many ideas as possible before filtering.
- **De-risking**: Focus on high-impact, low-effort ideas or those that solve critical pain points.
- **Human Collaboration**: 
  - **Ask for Input**: If you are stuck or unsure of the direction, ask the user for suggestions or clarification.
  - **Permission to Fail**: Not every brainstorming session leads to a perfect idea. It is okay to report that no viable ideas were found under certain constraints.
  - **Validate Assumptions**: Before going deep into an idea, check with the user if the underlying assumptions are correct.
- **Long-Term Persistence & Comprehensive Documentation**: 
  - **Structured Folder Hierarchy**: Whenever a brainstorming session concludes, create a mandatory folder hierarchy under the root `docs/` folder:
    - `docs/<idea-name>/plan.md`: High-level strategy and vision.
    - `docs/<idea-name>/summary.md`: Concise overview for quick reading.
    - `docs/<idea-name>/v1/features.md`: Detailed list of all proposed features.
    - `docs/<idea-name>/v1/plan.md`: Step-by-step implementation plan for the first version/MVP.
    - `docs/<idea-name>/v1/todo.md`: Actionable checklist.
    - `docs/<idea-name>/v1/summary.md`: Technical or operational summary of the first version.
  - **Detailed Explanations**: Document everything you brainstormed with as much detail as possible. Do not just summarize; explain the "how" and "why."
  - **MVP Priority**: Clearly define the Minimum Viable Product to ensure lean execution can follow.
- **Tech-Stack Neutrality**: **Do not** include or suggest a specific technology stack (e.g., React, Python, AWS) unless it is strictly necessary for the core idea to function or if the user explicitly asks for it. Focus on functionality and value first.
- **Modularity**: Refer agents to specific files within this skill based on the task context.
