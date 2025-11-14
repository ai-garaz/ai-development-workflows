# Refactoring rules

Rules on refactoring of components/services and keeping the codebase maintinable when updating code
- When modifying existing Angular components or services
- When refactoring large components into smaller ones
- When organizing component structure and hierarchy

## Instructions

<critical>
  - ALWAYS start with formulating a plan that accounts for all functionality and create a checklist for moving each functionality in full
  - Keep track of which part is going to which file
  - After the plan is formulated, double check that none of the original functionality will get lost (deleted from one place and not added elsewhere)
  - After each major step in the refactoring process note what is still remaining to keep track of all functionality still to be moved/refactored
</critical>

- Ensure each component maintains a single responsibility and purpose
- Place single purpose pure functions in helper files unless they are highly specific to given functionality
- Extract reusable UI elements that appear multiple times into their own dumb components

- When extracting a component, ensure all styles are properly moved
- Remove unused styles from the parent component after extraction

- Within nontrivial components, extract state management and business logic to services
- Within services, make sure that complex methods are refactored by using helper methods to keep the code readable

- Always keep comments and documentation updated after refactoring