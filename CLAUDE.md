# study-notes

Personal technical study notes for interview preparation.

## Structure

- `notes/` — Markdown files containing study notes, one topic per file.
- `assets/` — Images and other media referenced by notes (diagrams should use Mermaid instead, see below).

## File naming

- All files in `notes/` and `assets/` use kebab-case, e.g. `java.md`, `system-design.md`, `hashmap-vs-treemap.md`, `java-garbage-collection.md`, `load-balancer-architecture.png`.

## Required structure for every note

1. `# Title` — a single H1 matching the file's topic.
2. `## Index` — immediately after the title. A bulleted list of links to every `##`, `###`, and `####` heading in the file, nested to mirror the heading hierarchy (`###` under its parent `##`, `####` under its parent `###`), each heading as its own separate index entry.
3. Content sections using `##`, `###`, and `####` (three heading levels below the title), followed by normal paragraph text under each heading.

Whenever a heading is added, removed, or renamed, update the Index section in the same edit.

### Formatting rules

- One H1 per file (the title only).
- Headings follow strict hierarchy: `#` → `##` → `###` → `####` (don't skip levels).
- Two blank lines before a new `##` section; one blank line before `###`/`####` headings and after every heading, so sections are visually well separated.
- Each heading is followed by normal paragraph text (not immediately another heading or just a list).
- One blank line between paragraphs and between a paragraph and the next list/code block/table.
- Lists use `-` for bullets, consistently indented with 2 spaces per nesting level; use nested bullets for sub-points instead of flattening them into one list.
- Use numbered lists (`1.`, `2.`, ...) for sequential steps, ordered processes, or ranked items.
- Use Markdown tables for comparisons, side-by-side properties, or structured data (e.g. comparing data structures, time complexities, pros/cons).
- Choose whichever format (prose, bullets, numbered list, table) best fits the content — don't force paragraphs where a table or list would be clearer, and don't over-fragment simple explanations into unnecessary lists.
- Formatting consistency and the heading hierarchy are non-negotiable: before finishing any edit, verify heading levels, spacing, and the Index are all correct — this is critical for fast, reliable rendering and navigation.

## Content and writing style

- Written in English only.
- Technically accurate, using correct terminology — but concise, not over-explained.
- Written for interview prep: prioritize what's correct, relevant, and memorable over exhaustive coverage.
- Use short examples and simple analogies to make technical concepts easier to recall, without sacrificing technical accuracy.
- Factual and technical correctness is non-negotiable: never write information you are not confident is correct, since these notes are used directly for interview preparation. When uncertain about a detail, verify it (e.g. via research) rather than guessing, or flag it clearly instead of stating it as fact.
- Notes can be long (potentially thousands of lines); keep individual sections tight and well-scoped rather than padding them.

## Code examples

- Default language: the latest stable version of Java for DSA, system design, and core CS topics (data structures, algorithms, OOP, concurrency, etc.).
- For notes specifically about another technology/language/framework (e.g. Python, JavaScript, SQL, Docker), use that technology's language for code examples instead of Java.
- Outside of the cases above, use Java unless the user explicitly asks for another language.
- All code, including single identifiers, method/class names, commands, and config snippets used as examples, must be inside proper fenced code blocks or inline code spans (`` `like this` ``) — never written as plain text.
- Prefer multi-line fenced code blocks (triple backticks, with the correct language tag: ```java ...```, ```json ...```, ```bash ...```, etc.) over single-backtick inline code spans whenever the example is a runnable snippet, not just a short identifier or term mentioned in a sentence.
- Code must be correct by inspection; it does not need to be run or tested.
- Never include real personal information (names, emails, etc.) in code examples — use only mock/test data (e.g. `john.doe@example.com`, `Jane Smith`, `ACME Corp`).

## Diagrams

- Prefer Mermaid diagrams (fenced ```mermaid blocks) over static images for flowcharts, sequence diagrams, class diagrams, etc.
- Use the `assets/` folder only for images that can't reasonably be expressed as a Mermaid diagram.
- Reference images from `assets/` using a relative path (e.g. `![Load balancer architecture](../assets/load-balancer-architecture.png)`), never an absolute path.

## Working with these files (context management)

- Notes can be very long. Avoid reading an entire file when only a small edit is needed.
- Prefer targeted reads/edits (specific line ranges or sections) over full-file reads when updating an existing note.
- When adding/editing a section, also patch just the Index section rather than rewriting the whole file.
