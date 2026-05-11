# Workspace Context: Claude Code with Ollama

This file contains the configuration and operational details for using the Claude Code CLI with a local Ollama instance, as discussed on March 9, 2026.

## Setup Instructions

To use Claude Code for free with local models via Ollama:

1.  **Environment Variables (Windows PowerShell - Session):**
    ```powershell
    $env:ANTHROPIC_BASE_URL="http://localhost:11434"
    $env:ANTHROPIC_API_KEY="ollama"
    ```

2.  **Make Permanent (Windows):**
    - Run `notepad $PROFILE` in PowerShell.
    - Add the two lines above to the file and save.

3.  **Environment Variables (Mac/Linux):**
    ```bash
    export ANTHROPIC_BASE_URL="http://localhost:11434"
    export ANTHROPIC_API_KEY="ollama"
    ```

4.  **Launch Command:**
    ```powershell
    claude --model <model-name>
    ```

## Recommended Models

| Model | Recommendation | Notes |
| :--- | :--- | :--- |
| **Qwen 2.5-Coder (32B)** | **Best Choice** | High tool-calling reliability; optimized for 40+ languages. Requires 32GB+ RAM. |
| **Qwen 2.5-Coder (7B)** | **Good for 16GB RAM** | More reliable than Llama 3.1 8B for agentic tasks. |
| **Llama 3.1 (8B)** | **Alternative** | Good general reasoning, but may struggle with Claude Code's large system prompt (16.5k tokens). |
| **Llama 3.2 (1B/3B)** | **Not Recommended** | Too small to reliably handle complex tool-calling schemas. |

## Alternative Model: DeepSeek Coder V2

If Qwen 2.5 doesn't work for you, try **DeepSeek Coder V2**:
- **Why:** It has a 128k context window and is specifically fine-tuned for the same "agentic" tasks Claude Code performs.
- **Pull Command:** `ollama pull deepseek-coder-v2`
- **Note:** It is a "Mixture of Experts" (MoE) model, meaning it is very "smart" but requires significant RAM to load.

## Final Success Checklist

1. [ ] **Ollama is running** in the background.
2. [ ] **Model is pulled** (e.g., `ollama pull qwen2.5-coder:7b`).
3. [ ] **Env variables are set** (`ANTHROPIC_BASE_URL` and `ANTHROPIC_API_KEY`).
4. [ ] **Context is increased** (either via `Modelfile` or `OLLAMA_CONTEXT_LENGTH`).
5. [ ] **Launch Claude:** `claude --model qwen2.5-coder:7b`.

## Useful Ollama Commands

- **List models:** `ollama list`
- **Check running models:** `ollama ps`
- **Delete a model:** `ollama rm <model-name>`
- **Pull/Update a model:** `ollama pull <model-name>`
- **Rename (Copy + Delete):**
  ```powershell
  ollama cp <old-name> <new-name>
  ollama rm <old-name>
  ```

## Troubleshooting: Context Window
Claude Code sends a large system prompt (~16.5k tokens). If the model fails or "forgets" instructions, you must increase the context window (`num_ctx`).

**Option A (Global):**
Set the environment variable `OLLAMA_CONTEXT_LENGTH` to `64000`.

**Option B (Custom Model):**
1. Create a `Modelfile`:
   ```dockerfile
   FROM qwen2.5-coder:7b
   PARAMETER num_ctx 64000
   ```
2. Run `ollama create qwen-64k -f Modelfile`.
3. Launch with `claude --model qwen-64k`.

## Best Practices

### 1. Optimize for Speed (GPU vs CPU)
Run `ollama ps` while the model is active. If the "CPU" percentage is high, the model is too large for your VRAM. Try a smaller version (e.g., `q4_K_M` quantization) for faster responses.

### 2. Create a `CLAUDE.md`
If the agent makes mistakes, create a `CLAUDE.md` file in your project root. Claude Code reads this file automatically to learn your coding standards, naming conventions, and project structure.

### 3. Use `/config`
Inside the Claude Code CLI, type `/config` to toggle settings like "Auto-compact" (helps with small context windows) or "Power-steering" (for better reasoning).

## Maintenance & Updates

- **Update Claude Code:** `npm install -g @anthropic-ai/claude-code@latest`
- **Update Ollama Models:** `ollama pull qwen2.5-coder:7b`
- **Reset the Agent:** If Claude Code gets stuck or starts looping, you can clear its local state by deleting the `.claude` folder (if it exists) in your project or by running the `/reset` command inside the CLI.

## Core Claude Code Commands

Once inside the `claude` CLI, you can use these commands:

- `/review` - Ask the agent to review your current changes.
- `/test` - Ask the agent to run tests and fix any failures it finds.
- `/ask <question>` - Ask a question without the agent trying to modify files.
- `/compact` - Manually shrink the context to save memory/tokens.
- `/reset` - Clear the current conversation history.
- `exit` or `Ctrl+C` - Close the CLI.

### Keyboard Shortcuts
- **Tab:** Autocomplete file paths or commands.
- **Up/Down Arrow:** Cycle through your command history.
- **Ctrl+L:** Clear the terminal screen.

## Configuration Files

Instead of environment variables, you can use JSON files for persistence:

- **Global Settings:** `~/.claude/settings.json`
- **Project Settings:** `.claude/settings.local.json` (Git-ignored)

**Example `settings.json`:**
```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "http://localhost:11434",
    "ANTHROPIC_API_KEY": "ollama"
  }
}
```

## Windows & WSL Note
If you run Claude Code inside **WSL** but Ollama is running on your **Windows host**:
1. Use the Windows IP instead of `localhost`:
   ```bash
   export ANTHROPIC_BASE_URL="http://$(hostname).local:11434"
   ```
2. Ensure Ollama is set to listen on all interfaces (Set Windows env var `OLLAMA_HOST=0.0.0.0` and restart Ollama).

## Which Tool to Use?

| Feature | Claude Code (Local) | Gemini CLI (Me) |
| :--- | :--- | :--- |
| **Cost** | Free (Local hardware) | Free (Google AI Studio Tier) |
| **Privacy** | 100% Local | Cloud-based (Google) |
| **Speed** | Depends on your GPU | High (Cloud-powered) |
| **Context** | Limited by VRAM (e.g., 32k-64k) | **Massive (Up to 2 Million tokens)** |
| **Strengths** | Fast local file edits. | Full repo analysis, deep logic. |

## Quantization & VRAM Management

If a model is too slow or you run out of memory, try a smaller "quantized" version.

- **q4_K_M (Recommended):** Best balance of speed and intelligence. Most models default to this.
- **q8_0:** Higher intelligence, but takes ~2x more VRAM. Use this only if the model is making logical errors.
- **q2_K / q3_K:** Very fast and small, but the model may become "stupid" or fail to follow instructions.

**VRAM Calculation Rule of Thumb:**
- **7B Model:** Needs ~6GB - 8GB VRAM.
- **32B Model:** Needs ~20GB - 24GB VRAM.
- *Adding a 64k context window adds another ~2GB - 4GB of VRAM usage.*

## Advanced: MCP (Model Context Protocol)

You can give Claude Code "extra powers" by adding local MCP servers:

1. **Sequential Thinking (Logic Booster):**
   ```bash
   claude mcp add sequential-thinking -- npx -y @modelcontextprotocol/server-sequential-thinking
   ```
2. **Filesystem (External File Access):**
   ```bash
   claude mcp add filesystem -- npx -y @modelcontextprotocol/server-filesystem C:\Users\YourUser\Documents
   ```
3. **Google Search (Live Info):**
   ```bash
   claude mcp add google-search -- npx -y @modelcontextprotocol/server-google-search
   ```

## Common Use Cases (Try these first!)

- **Project Onboarding:** "Explain how the routing works in this project."
- **Code Cleanup:** "Find all console.logs and remove them."
- **Bug Fixes:** "I'm getting a 404 error on the /api/users route. Find and fix it."
- **Documentation:** "Add JSDoc comments to all functions in the utils folder."
- **Refactoring:** "Convert this React class component into a functional component with hooks."

## Agentic Safety & Isolation

### 1. Shell Safety
Claude Code will ask for permission before running commands. You can configure "Auto-approve" for safe commands (like `ls` or `pwd`) in your `settings.json`, but **never** auto-approve write/delete commands.

### 2. Docker Isolation (Recommended for untrusted code)
If you are working on a project you don't fully trust, run Claude Code inside a Docker container:
```bash
docker run -it -v $(pwd):/app -w /app node:latest /bin/bash
# Inside the container:
npm install -g @anthropic-ai/claude-code
claude
```

## Performance Note
Ensure your hardware can handle the increased memory usage from a larger context window to avoid slow responses or tool-calling failures.
