Using the plan as the source of truth, audit your implementation for coverage and alignment.

Spawn 1-3 subagents (in parallel if possible) based on the complexity and number of affected files, provide them with the plan and ask them to do the following:

Read the plan, research the codebase (or part of it assigned to you) and audit this implementation for coverage and alignment. For each requirement you can infer from the plan, mark it "covered," "partial," or "missing." Briefly cite where it's addressed in the implementation: document and section. If you can't point to evidence, treat it as "partial" or "missing." Also note any other problems encountered along the way that might cause problems or be in conflict with the essence of the plan or the essence of this system.

Then, do two things:
1. List the top gaps (these are missing, partially covered, or unclear requirements prioritized by impact) and problems.
2. Suggest high-impact improvements to the implementation which would increase its alignment with the essence of the requested changes.

Once they get back to you with results, consider critically what they reported. Think about possible impacts of ignoring each of their findings and then implement everything that would not be ignored by a responsible senior system maintainer.

Then spawn 1-3 subagents based on the complexity and number of affected files, and ask them to research the impact of the implementation on the state of documentation of the system.

They are looking for important gaps in the documentation related to the implementation or contradictions with what was implemented.

Based on their findings, update the documentation to be in a state enabling us to keep this system well maintained and documented. If possible, use subagents for the documentation update as well, but make them surgical or do the updates yourself (we can't risk degrading existing documentation). Review their results afterwards.

Use the most capable model available for all subagents.
