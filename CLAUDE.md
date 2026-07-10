# CLAUDE.md

This directory holds the official Pest documentation. When writing or editing any file here, match the established tone of the docs. Consistency of voice matters as much as technical accuracy.

## Voice & Tone

The Pest docs are **warm, welcoming, and confident**. The reader should feel like they're being guided by a knowledgeable friend who wants them to succeed — never lectured at, never overwhelmed.

- **Write in second person.** Address the reader directly as "you" and refer to "your test suite", "your project", "your application". Make the docs feel personal.
- **Use the collaborative "we" and "let's".** When walking through an example, invite the reader along: "Next, let's write our first test", "We'll cover each of these in detail". This builds a sense of working through the material together.
- **Stay in the present tense.** Describe what Pest does, not what it will do or would do: "Pest provides", "When you run the command, Pest displays…".
- **Be encouraging and positive.** Pest is a joy to use, and the docs should reflect that. It's natural to describe features as "convenient", "elegant", "powerful", or "beautiful" — but let the quality speak for itself and never oversell.
- **Explain the "why", not just the "how".** Don't only state what a feature does; give the reader a brief sense of when and why they'd reach for it. Motivate each concept before showing the code.

## Writing Style

- **Clarity above cleverness.** Prefer short, direct sentences. If a sentence is hard to follow, split it. The goal is that a newcomer to testing can read a page top to bottom and understand it.
- **Introduce, then demonstrate.** Lead with a sentence or two of prose that sets up an example, present the code, then explain what the reader is looking at afterward.
- **Assume intelligence, not prior knowledge.** The reader is a capable developer, but may be new to Pest or to testing in general. Define terms the first time they appear and avoid unexplained jargon.
- **Keep it concise.** Say what needs to be said and move on. Respect the reader's time — no filler, no padding, no repetition for its own sake.
- **Use American English spelling and conventions.**

## Structure & Formatting

- Every documentation page begins with YAML front matter containing a `title` and a `description`. The `description` is a single, inviting sentence that summarizes the page.
- The page's first heading (`#`) matches the title. Use `##` for major sections and `###` for subsections.
- Wrap inline references to functions, methods, classes, files, and commands in backticks: `expect()`, `it()`, `./vendor/bin/pest`, `Pest.php`.
- Use fenced code blocks with a language hint (```php, ```bash, ```plain). Keep example code realistic, minimal, and focused on the concept being taught — no unrelated noise.
- Link generously to related pages using the `/docs/{page}` convention so readers can explore adjacent topics.

## What to Avoid

- Don't be terse or robotic — this is not an API dump. Prose carries the reader between the code.
- Don't be overly casual, jokey, or use slang, emoji, or exclamation-heavy hype. The tone is friendly and professional, not flippant.
- Don't condescend. Never imply something is "obvious", "simple", or "just" a matter of doing X — what's simple to one reader is new to another.
- Don't assume the reader has read other pages out of order; give each page enough context to stand on its own, then link out for depth.
