---
name: js-array-book-writer
description: >
  Write new JavaScript array method sections for book.md following exact pattern, format, and style.
  When adding a new method, match all conventions from existing methods in book.
---

## Task

When asked to add new JS array method section(s) to `book.md`, generate content matching existing sections exactly.

**Do not alter** existing method sections. Insert new sections only under `## Methods` heading.

## Pattern Rules

Each method section MUST include:

1. **Header**: `### \`methodName\``
2. **Definition line**: Bold intro explaining purpose + behavior
3. **Primary code example**: Shows practical usage with `employees` or `company` dataset
4. **Output comments**: Multi-line comments inside code blocks (`// [...]`)
5. **Edge cases/notes**: Notes on mutability, performance, edge conditions
6. **Related comparisons**: Contrast with similar methods when useful (e.g., flat vs flatMap)

## Style Guidelines

- Use `employees` dataset for most examples (from `book.md` Source Data section)
- Use `company.departments[...]` when accessing nested dept-level data
- Add `//` comments explaining each step, not just code syntax
- Mark mutability explicitly in Definition or notes:
  - "It does not mutate the original array"
  - "It mutates the original array"
- When showing before/after states, use two code blocks (one per state)
- Include practical use cases, not just basic usage

## Format Template

```markdown
---

### \`methodName\`

**Definition:** [Concise explanation of purpose and behavior. 1-2 sentences max.]

[Primary example using employees dataset]

\`\`\`js
// Comment explaining this operation
const result = methodName(args);

console.log(result);
// [output with multi-line comments if helpful]
\`\`\`

[Optional: Secondary example or comparison]

\`\`\`js
// Show alternative usage or what happens without method
alternativeCode;
// comment explaining difference
\`\`\`

[Optional: Edge cases, mutability notes, related methods comparisons]
```

## Examples from Book (Study These)

**flatMap example structure:**

- Shows primary use case (allSkills extraction)
- Shows what you'd get without it (array of arrays)
- Shows conditional output pattern (active employees only)
- Note about efficiency vs chaining .map().flat()

**sort example structure:**

- Basic usage (strings work by default)
- Numbers broken without comparator (shows wrong result)
- Comparator explanation with inline comments
- Descending variant
- Bubble sort comparison for understanding
- Object sorting example with spread copy

**Notes always include:**

- Mutability status in Definition or separate note
- When relevant: performance considerations
- Edge cases (undefined return, empty array behavior, etc.)

## Do's

- Match tone of existing writing (conversational but technical)
- Use bold for emphasis on key terms (initial value, comparator, mutates)
- Keep code minimal and focused on the method being demonstrated
- Use consistent formatting: two-space indent in code blocks, no trailing whitespace

## Don'ts

- Do not rewrite existing sections when inserting new ones
- Do not skip comments that explain logic
- Do not use inconsistent dataset references (stick to book's company/employees)
- Do not write definitions that are too vague or too long

## Output Format

Return full markdown section ready to paste under `### \`[lastMethod]\``heading, ending before final closing of file. Include separator line`---` after section.

Example call: "Add a section for `toReversed`" → returns method definition + examples matching book style.
