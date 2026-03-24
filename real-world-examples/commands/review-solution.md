Using our discussion as the source of truth, audit the solution you most recently proposed or implemented.

Step 1 — Identify the solution boundary. Find the latest message where you described a solution (proposed or implemented). Trace backward through the conversation to identify the discussion that led to it — agreements, requirements, constraints, edge cases, preferences, and decisions. If the conversation covered multiple topics, scope only to the discussion relevant to this solution.

Step 2 — Spawn two parallel foreground subagents (use the most capable model available). Tell each which message(s) constitute the solution and which portion of the discussion is relevant (from your Step 1 analysis).

Agent 1 — Completeness: Extract every agreement, requirement, constraint, edge case, and decision from the relevant discussion. For each, mark it "addressed," "partial," or "missing" in the solution. Cite evidence from the solution for each. If you can't point to evidence, treat it as "partial" or "missing."

Agent 2 — Stress test: Given what the solution is trying to achieve, identify the most likely failure points, what it didn't consider (e.g. specs or documentation clashes or missed updates), and what assumptions it's making. Focus on practical risks, not theoretical nitpicks.

Step 3 — Review the subagents' findings. For each item, critically evaluate whether it materially affects the solution. Discard noise. For anything substantive, recommend one of: implement now, flag for operator, or dismiss with reason. Then apply the non-dismissed changes — revise the proposal or update the implementation as appropriate.
