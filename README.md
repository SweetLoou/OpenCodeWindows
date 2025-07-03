# OpenCode from Windows on WSL 2 — Setup Guide

Run OpenCode on Windows right inside WSL Ubuntu and edit seamlessly from Windows. Follow the four stages below and you’ll be chatting with your code in minutes.

---

## Table of Contents

* [Prerequisites](#prerequisites)
* [Stage 1: Install WSL 2](#stage-1-install-wsl-2)
* [Stage 2: Set Up Your Linux Dev Stack](#stage-2-set-up-your-linux-dev-stack)
* [Stage 3: Wire In AI Credentials and Model](#stage-3-wire-in-ai-credentials-and-model)
* [Stage 4: Choose an Editor](#stage-4-choose-an-editor)
* [Workflow Pointers](#workflow-pointers)
* [Updating and Uninstalling](#updating-and-uninstalling)
* [Optional: Claude-Style Chat and Edit in VS Code](#optional-claude-style-chat-and-edit-in-vs-code)
* [Troubleshooting](#troubleshooting)
* [Bottom Line](#bottom-line)

---

## Prerequisites

* Windows 10 22H2 or Windows 11 with virtualization enabled
* **Admin PowerShell** access (for one-time `wsl --install`)
* A free API key from OpenAI, Anthropic, Google AI, etc.

---

## Stage 1: Install WSL 2

```powershell
wsl --install           # one-command setup
# Installs WSL 2 engine, Ubuntu LTS, and Windows Terminal in one shot.

# Reboot when prompted.
# Launch Ubuntu from the Start menu to finish first-run steps.

# Tip - enable systemd (nicer service management):
sudo tee /etc/wsl.conf << EOF
[boot]
systemd=true
EOF
wsl --shutdown          # then reopen Ubuntu
```

---

## Stage 2: Set Up Your Linux Dev Stack

```bash
# inside Ubuntu shell
sudo apt update && sudo apt upgrade -y      # refresh packages
sudo apt install -y curl build-essential    # toolchain prereqs

# Install Node LTS (>= 18)
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs

# Install OpenCode
npm install -g opencode-ai
opencode --version                          # sanity check
```

---

## Stage 3: Wire In AI Credentials and Model

Append your key(s) and default model to `~/.bashrc`:

```bash
echo 'export GOOGLE_API_KEY="PASTE-KEY"'           >> ~/.bashrc
echo 'export OPENCODE_MODEL="google/gemini-2.5-pro"' >> ~/.bashrc
source ~/.bashrc
```

Need a model string? Query the Models.dev catalog any time:

```bash
curl -s https://models.dev/api.json | jq '.[] | {provider,id}'
```

---

## Stage 4: Choose an Editor

### A. Visual Studio Code + Remote WSL (recommended)

1. Install VS Code on Windows.
2. Add the **WSL** extension (part of **Remote Development**).
3. From Ubuntu, open your project:

   ```bash
   code .
   ```

   VS Code pops up on Windows but is attached to the Ubuntu FS; integrated terminals already run in WSL, so `opencode` "just works."

### B. NeoVim in terminal (minimal)

* Stay in Ubuntu shell.
* Install NeoVim + an AI helper (e.g., Aider).
* Zero-GUI, keyboard-only bliss.

---

## Workflow Pointers

| Task                                | Where to run it                       | Notes                                                               |
| ----------------------------------- | ------------------------------------- | ------------------------------------------------------------------- |
| Git clones / code                   | `~/code/...` inside Ubuntu            | Native Linux FS is \~10× faster than `C:\` mounts.                  |
| CLI tools (`opencode`, `npm`, etc)  | Ubuntu terminal OR VS Code’s terminal | Full Linux tool-chain parity.                                       |
| Windows apps needing files          | `\\wsl$\\Ubuntu\\home\\<user>\\...`   | No sync needed.                                                     |
| Quick one-off OpenCode from Windows | Windows PowerShell                    | `wsl opencode ask "..."` runs the WSL binary from a Windows prompt. |

---

## Updating and Uninstalling

```bash
# Update OpenCode
npm update -g opencode-ai

# Remove OpenCode
npm uninstall -g opencode-ai

# Update Ubuntu packages
sudo apt update && sudo apt upgrade -y

# Update WSL engine
wsl --update
```

---

## Optional: Claude-Style Chat and Edit in VS Code

Want the Claude Code vibe? Add the **Continue** extension:

```bash
# inside VS Code Extensions panel
search "Continue – open-source AI code assistant" → Install
```

Create `~/.continue/config.json` (WSL side):

```json
{
  "models": [
    {
      "title": "Gemini-2.5-Pro",
      "provider": "openai",
      "model": "gemini-2.5-pro",
      "apiKey": "${env:GOOGLE_API_KEY}",
      "roles": ["chat", "autocomplete", "embed"]
    }
  ],
  "defaultChatModel": "Gemini-2.5-Pro",
  "defaultAutocompleteModel": "Gemini-2.5-Pro"
}
```

* Open the Continue sidebar (Ctrl+Shift+Alt+C).
* Highlight code, ask questions, and watch inline patches appear—just like Claude, but powered by your own model in WSL.

---

## Troubleshooting

| Symptom                   | Fix                                                                                    |
| ------------------------- | -------------------------------------------------------------------------------------- |
| "No providers configured" | Run `opencode auth login` or set `*_API_KEY` env vars.                                 |
| TUI looks garbled         | Use a true-color terminal (Windows Terminal, VS Code) and a Powerline-compatible font. |
| Keybind conflicts         | Change the leader key in `~/.config/opencode/config.json`.                             |
| Can’t scroll back in TUI  | Use PgUp/PgDn (full page) or Ctrl+Alt+U/D (half page).                                 |

---

## Bottom Line

WSL 2 gives you a clean Linux sandbox; VS Code Remote WSL delivers a seamless GUI.
Together with OpenCode (and optionally Continue) you get:

* Native Linux performance for package managers & compilers
* Instant Windows access to every file
* A Claude-style, chat-and-edit cockpit whenever you need it
* Freedom to drop to a pure terminal workflow anytime.

> **Note:** ChatGPT can make mistakes. Always verify important commands and paths before running.
