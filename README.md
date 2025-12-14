# react-to-solid-llms (r2s llms 🧠)

A bunch of LLM-friendly examples + guides to let AI easily one-shot port any ReactJS ecosystem lib to SolidJS.

## Usage

### 0️⃣ Zero-setup llms.txt

Use inside any coding agent / chat e.g. Claude Code, OpenCode

Example Prompt (Or just use the link):

```sh
Reference: https://raw.githubusercontent.com/blankeos/react-to-solidjs-llms/main/llms.txt

😏 Port this codebase to SolidJS, no mistakes.
```

### 🚀 IDE / MCP agent

Visit [gitmcp.io](https://gitmcp.io/blankeos/react-to-solid-llms) for instructions to add to your Cursor / VS Code / Coding Agent settings.

### Contributing

Example Prompt:

```
You're maintaining react-to-solid-llms docs for React → SolidJS ports.

1. Study `@folder1` (React lib) + `@folder2` (Solid port)
2. Add to `llms.txt`: `[Topic Name](https://raw.githubusercontent.com/blankeos/react-to-solidjs-llms/main/topic/subtopic.md)`
3. Create `topic/subtopic.md` with guides or examples or explanations of concepts.

[Subtopic 1](/topic-1/subtopic-1.md)
```

> Crafted from my experience porting a bunch of react libs to solid. (Mostly just fed it through Gemini 3, but I can't fully verify the information lol)
>
> - @mantine/hooks -> bagon-hooks
> - @number-flow/react -> solid-number-flow
> - sonner -> solid-sonner
