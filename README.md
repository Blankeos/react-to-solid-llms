# react-to-solid-llms (r2s llms 🧠)

A bunch of LLM-friendly examples + guides to let AI easily one-shot port any ReactJS ecosystem lib to SolidJS.

## Usage

### 0️⃣ Zero-setup llms.txt

Use inside any coding agent / chat e.g. Claude Code, OpenCode

Example Prompt (Or just use the link):

```sh
Reference: https://raw.githubusercontent.com/blankeos/react-to-solid-llms/main/llms.txt

😏 Port this codebase to SolidJS, no mistakes.
```

### 🚀 IDE / MCP agent

Visit [gitmcp.io](https://gitmcp.io/blankeos/react-to-solid-llms) for instructions to add to your Cursor / VS Code / Coding Agent settings.

### Contributing

Some LLM tips that I use to add more content here:

#### Adding more topics

```
You're maintaining react-to-solid-llms docs for React → SolidJS ports.

1. Study `@folder1` (React lib) + `@folder2` (Solid port)
2. Update `llms.txt`:
  - If the concept is new: add `[Topic Name](https://raw.githubusercontent.com/blankeos/react-to-solid-llms/main/topic/subtopic.md)`
  - If the concept already exists: merge / enhance the existing topic entry or Markdown file instead of duplicating
3. Create `topic/subtopic.md` with guides or examples or explanations of concepts.
 i.e. [Subtopic 1](/topic-1/subtopic-1.md)
```

#### Verifying that the hyperlinks work:

(I use Mistral's `Devstral 2` model for this, it can easily make subagents to check each hyperlink very easily without bloating context)

```
Can you verify that each of the hyperlinks from this root llms.txt works and has relevant content?

https://raw.githubusercontent.com/Blankeos/react-to-solid-llms/main/llms.txt
```

#### Verifying that the contents are real

Use your brain 🧠😭

### Why I made this

Crafted from my experience porting a bunch of react libs to solid. (Mostly just fed it through Gemini 3, but I can't fully verify the information). But my sources were:

- @mantine/hooks -> bagon-hooks
- @number-flow/react -> solid-number-flow
- sonner -> solid-sonner
- [Functional recursion](https://www.carlo.tl/blog/how-to-dynamically-wrap-components-in-solidjs)
