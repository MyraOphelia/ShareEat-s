# ShareEat Local MCP Server

This folder contains a local **MCP (Model Context Protocol) server** for your project.

In simple terms, MCP lets an AI assistant use safe tools you define (read files, search code, run lint, call local model, etc.) instead of only chatting.

---

## What MCP Is (Simple)

MCP is a standard way to connect AI clients (like Cursor) to tools.

- AI asks: "run tool X with these inputs"
- MCP server runs it safely
- MCP server returns results

So your AI can do real project work, not only text answers.

---

## What This MCP Server Can Do

This server exposes 7 tools:

1. **`project_file_reader`**  
   Read selected files from your project safely.

2. **`project_file_writer`**  
   Create/update files. Important files require explicit confirmation.

3. **`code_search`**  
   Search for functions, classes, routes, TODOs, or keywords.

4. **`function_manager`**  
   List important functions and give a quick explanation of what they likely do.

5. **`code_maintenance_tool`**  
   Detect risky patterns (TODO/FIXME, console logs, long lines, possible duplication).

6. **`command_runner`**  
   Run safe commands only (`npm`, `node`, `npx`) for typical maintenance tasks.

7. **`local_ai_helper`**  
   Send prompt to local **Ollama** model and return response.

---

## Recommended Local Model (MacBook M4 Pro, 16GB RAM)

Best default for speed + quality:

- **`qwen2.5:3b-instruct`** (recommended)

Other free options you can try:

- `llama3.2:3b`
- `mistral:7b-instruct` (heavier but stronger)
- `deepseek-r1:1.5b` (small reasoning model)

---

## Install Ollama

### 1) Install Ollama

- macOS: [https://ollama.com/download](https://ollama.com/download)

Verify:

```bash
ollama --version
```

### 2) Pull a model

```bash
ollama pull qwen2.5:3b-instruct
```

### 3) (Optional) test model directly

```bash
ollama run qwen2.5:3b-instruct
```

---

## Install MCP Server Dependencies

From your project root:

```bash
cd mcp
npm install
```

Run server:

```bash
npm start
```

This MCP uses **stdio transport**, which is what Cursor expects for local MCP servers.

---

## Connect To Cursor

In Cursor MCP settings, add a local server entry like this:

```json
{
  "mcpServers": {
    "shareeat-local-mcp": {
      "command": "node",
      "args": [
        "/ABSOLUTE/PATH/TO/PROJECT_02_2026/mcp/src/server.mjs"
      ]
    }
  }
}
```

Replace with your real absolute path.

If you prefer launching from folder context, use:

```json
{
  "mcpServers": {
    "shareeat-local-mcp": {
      "command": "npm",
      "args": ["start"],
      "cwd": "/ABSOLUTE/PATH/TO/PROJECT_02_2026/mcp"
    }
  }
}
```

---

## Safety Rules Built In

- Blocks path access outside your project root.
- Blocks dangerous shell commands by allow-listing safe commands only.
- Requires `confirm_important=true` before writing important paths.
- Skips `.git` and `node_modules` during project scans.

---

## Extend It Easily

Main file:

- `mcp/src/server.mjs`

To add a new tool:

1. Add tool definition in `ListToolsRequestSchema` response.
2. Add handler function.
3. Route it in `CallToolRequestSchema` switch.

---

## 7 Practical Examples For Your ShareEat Project

1. **Find all Late Pickup logic**
   - Use `code_search` with query: `late_pickup`

2. **Inspect pickup flow file**
   - Use `project_file_reader` on `js/pickup.js`

3. **Patch FAQ text quickly**
   - Use `project_file_writer` for `js/help-center.js`

4. **List major checkout functions**
   - Use `function_manager` to summarize `js/checkout.js`

5. **Catch cleanup tasks**
   - Use `code_maintenance_tool` to spot TODO/FIXME/duplication

6. **Run website checks**
   - Use `command_runner` for `npm start` or lint script

7. **Draft migration/help text locally (no cloud AI)**
   - Use `local_ai_helper` with model `qwen2.5:3b-instruct`

---

## Notes

- This is intentionally simple and beginner-friendly.
- If you want, next step can add:
  - SQL migration helper tool
  - Supabase schema inspector tool
  - safer write approvals with per-file diff preview
