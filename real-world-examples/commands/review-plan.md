Using the original request and our discussion around it as the source of truth, audit the plan you just produced for coverage and alignment.

For each major requirement you can infer from the request, mark it "covered," "partial," or "missing." Briefly cite where it's addressed in the plan: Section Name or Short Quote. If you can't point to evidence, treat it as "partial" or "missing." Another sensitive area to cover is decisions and answers to questions. Audit whether the decisions we made by you asking questions and me giving you answers made it to the final plan without explicitly communicated divergences.

Then, do three things:
1. Give a simple coverage score between 0 and 100, and a 1- to 2-sentence rationale.
2. List the top gaps (these are missing, partially covered, or unclear assumptions prioritized by impact).
3. Produce a patched version of the plan that closes those gaps with minimal changes, preserving the original structure where possible. Add or adjust sections rather than rewriting everything.

Then spawn a subagent (use the most capable model available) with the task of auditing alignment with existing system. The objective is to find any possible conflicts with the system and its documentation. If the complexity of the task requires multiple sub-agents, you can spawn up to three.
