Let's double-check this.

Spawn two subagents in parallel:

1. **Critical reviewer** — Give this agent full context of what you're claiming (or, in case I'm using this command to double-check your execution, then give it full context of what you executed). Ask it to review your findings (or implementation if this has already been implemented). It should assess correctness, completeness, and whether the approach actually solves the stated problem or challenge. It should flag anything that looks wrong, incomplete, or fragile.

2. **Hole-poker** — Give this agent the same context. Ask it to actively poke holes in your findings. It should try to find edge cases you missed, assumptions you didn't validate, failure modes you didn't consider, and ways your solution could break. It should think adversarially.

Use the most capable model available for both subagents.

Once both return, review their findings together. Consider each point on its merits — not everything they flag will be actionable, but don't dismiss things reflexively either. Fix anything that a responsible person in your position would fix, and note anything you're consciously choosing to leave as-is and why.
