---
name: to-todos
description: Creates todos from a comprehensive specification document as cohesive, manageable units with deterministic execution order and explicit dependency links. Use only when explicitly invoked with `/to-todos` and a specification document or path.
disable-model-invocation: true
---

# To Todos

Create todos from one specification using Pi's installed `todo` tools.
Create todos through the tool rather than writing todo files directly.

## Input

Require a specification in the conversation or a document path supplied with the invocation. Read the complete document, including appendices and linked local material needed to understand it. If no specification is identifiable, ask for one and stop.

## Workflow

1. Verify that the `mitsupi:todos.ts` extension is installed by confirming its `todo` tools are available. If the extension or its tools are unavailable, report what is missing and stop before creating anything.
2. Extract the required outcomes, constraints, acceptance criteria, explicit exclusions, implementation decisions, and testing decisions from both the specification and relevant conversation context. Preserve decisions negotiated during grilling or clarification; do not generalize them away or invent work absent from the source material.
3. Split the work into cohesive units. Each todo must:
   - deliver one useful, independently verifiable outcome;
   - fit within one focused implementation session;
   - include all layers needed for that outcome rather than separate layer-only tasks;
   - state concrete acceptance criteria;
   - include mentioned implementation decisions from the specification context only when they directly relate to that todo's planned scope;
   - include mentioned testing decisions from the specification context only when they directly relate to that todo's planned scope;
   - preserve implementation and testing decisions in separate `## Implementation decisions` and `## Testing decisions` sections rather than blending them into general scope or acceptance criteria;
   - state `None specified.` in either decision section when the source contains no applicable explicit decision; never invent one;
   - omit decisions owned by another todo or unrelated to its planned scope;
   - avoid duplicating scope owned by another todo.
4. Split a unit further if it has multiple unrelated outcomes, cannot be verified clearly, or is too large for one focused session. Merge units that are not useful on their own.
5. Build a directed acyclic dependency graph. Add an edge only when one todo genuinely cannot begin or complete before another. Do not treat execution sequence alone as dependency.
6. Assign each todo a unique positive integer `execution_order` using a topological ordering. Break ties between independent todos by choosing the order that reduces integration risk and enables earlier verification.
7. Create todos with Pi's installed `todo` tools in execution order. Let the tools own the todo format; do not redefine it in this skill.
8. After all IDs are known, update each todo so it:
   - has an `execution_order` frontmatter property;
   - identifies the todos it depends on;
   - identifies the todos it blocks;
   - uses actual `TODO-<id>` references for every dependency.
9. Carry each mentioned implementation decision, testing decision, rationale, constraint, and out-of-scope boundary into the todo whose planned scope it directly affects. Keep implementation decisions in `## Implementation decisions` and testing decisions in `## Testing decisions`. Do not copy unrelated decisions into a todo or repeat them across todos unless multiple scopes genuinely require them. Information from the specification or prior grilling must not be lost during decomposition.
10. Use the `mitsupi:todos.ts` extension's `todo` tools to retrieve and verify the created todos. Confirm every specification outcome, implementation decision, and testing decision is covered in the appropriate scoped section; execution order is dependency-safe; references resolve; dependency relationships are reciprocal; and the graph has no cycles or self-references.
11. Report the created todos in execution order with their IDs and dependency summary.
