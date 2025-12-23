---
description: Define TypeScript/JavaScript tools that extend Roo's capabilities beyond built-in tools, enabling project-specific workflows and team standardization.
keywords:
  - experimental features
  - custom tools
  - TypeScript tools
  - JavaScript tools
  - tool extension
  - defineCustomTool
  - workflow automation
image: /img/social-share.jpg
---
# Custom Tools

Define TypeScript or JavaScript tools that Roo can call like built-in tools—standardize team workflows instead of re-prompting the same steps every task.

:::warning Experimental Feature
Custom tools is an experimental feature. Custom tools are **automatically approved** when enabled—Roo won't ask for permission before running them. Only enable this feature if you trust your tool code.
:::

---

## What it does

Custom tools let you codify project-specific actions into TypeScript/JavaScript files that Roo calls like [`read_file()`](/basic-usage/how-tools-work) or [`execute_command()`](/basic-usage/how-tools-work). Ship tool schemas alongside your repo so teammates don't need to keep re-explaining the same workflow steps. Tools are validated with Zod and automatically transpiled from TypeScript.

---

## How to create a tool

Tools live in `.roo/tools/` (project-specific) or `~/.roo/tools/` (global) as `.ts` or `.js` files. Tools from later directories can override earlier ones.

#### Basic structure

```typescript
import { parametersSchema as z, defineCustomTool } from "@roo-code/types"

export default defineCustomTool({
  name: "tool_name",
  description: "What the tool does (shown to AI)",
  parameters: z.object({
    param1: z.string().describe("Parameter description"),
    param2: z.number().describe("Another parameter"),
  }),
  async execute(args, context) {
    // args are type-safe and validated
    // context provides: mode, task
    return "Result string shown to AI"
  }
})
```

#### What you define

- **`name`**: Tool name Roo sees in its available tools list
- **`description`**: Shown to the AI so it knows when to call the tool
- **`parameters`**: Zod schema converted to JSON Schema for validation
- **`execute`**: Async function returning a string result to Roo

Tools are dynamically loaded and transpiled with esbuild. Changes to tool files trigger automatic reload.

---

## Enabling the feature

1. Open Roo Code settings (gear icon in top right)
2. Go to the "Experimental" tab
3. Toggle "Enable custom tools"

<img src="/img/custom-tools/custom-tools.png" alt="Enable custom tools toggle in experimental settings" width="400" />

**Critical:** When enabled, custom tools are **auto-approved**—Roo runs them without asking. Disable if you don't trust the tool code.

---

## Tool directories

- **`.roo/tools/`** in your workspace: project-specific tools shared with your team
- **`~/.roo/tools/`** in your home folder: personal tools across all projects

Tools from both directories are loaded. Tools with the same name in `.roo/tools/` override those in `~/.roo/tools/`.

---

## Limits

- **No approval prompts**: Tools are auto-approved when the feature is enabled—security trade-off for convenience
- **String-only results**: Tools must return strings (Roo's protocol constraint)
- **No interactive input**: Tools can't prompt the user mid-execution
- **No npm packages**: Tools are transpiled in isolation; use Node.js built-ins only
- **Cache invalidation**: Tool updates may require reloading the window

**vs. MCP:** [MCP](/features/mcp/overview) is for external services (search, APIs). Custom tools are for in-repo logic you control directly. MCP is more extensible; custom tools are lighter weight for project-specific actions.
