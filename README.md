# CWW (Chat With Web)
![Explain Example](./explain.png)

CWW (Chat With Web) lets you chat with the currently opened tab in Firefox. It enables you to interact with articles and YouTube videos directly from your browser.

---
Version 0.3.0
## What's New in This Version

### Added / Changed
- **Any OpenAI-compatible LLM is now supported** — no longer limited to Google Gemini. You can use OpenAI, Anthropic Claude, Google Gemini, OpenRouter, Ollama (local), LM Studio (local), or any provider with an OpenAI-spec API.
- **Provider URL, Model, and API Key are now configured in the extension settings** — nothing is stored on the backend server.
- **Verify & Save** — settings are verified against your LLM provider before being saved. If the provider is unreachable or the key is invalid, you are notified immediately.
- **PageSnap fallback** — for pages the backend cannot fetch directly (e.g. paywalled, JavaScript-heavy, or sign-in-required pages), the extension captures the page HTML from your browser and sends it to the backend for parsing. This means pages requiring a sign-in are now supported as long as you are already logged in and viewing the page.
- **Ollama support (local inference)** — run fully offline with any Ollama-compatible model. See the Ollama section below for recommendations.

### Removed
- **Gemini-only setup** — the extension no longer has a dedicated "Gemini API Key" field. All providers are configured through the unified Provider URL + Model + API Key fields.
- **Docker image** — the Docker image has been decommissioned and is no longer available or maintained. Use the native binary or service installation instead.
- **Docker proxy variables** (`CWW_USE_YT_PROXY`, `CWW_WEBSHARE_USER`, `CWW_WEBSHARE_PASS`) — these were part of the old Python-based Docker backend and are not available in the current Go backend.
- **Hardcoded language restriction** — the chat language is now determined by the LLM you configure, not fixed to English/Gemini.

---

## Requirements

To use CWW, you need to:
1. Run the **cww-backend**.
2. Install the **Firefox Addon**.

---

## Installation Guide

### 1. Install cww-backend

#### Download the Correct Build for Your Platform
Pre-built binaries and service files for all major platforms are available for download on the [Releases page](https://github.com/Asimq/cww/releases).

Extract the archive that matches your operating system and CPU architecture:

| Archive Name         | Platform                |
|---------------------|-------------------------|
| windows-amd64.zip   | Windows 64-bit (x86_64) |
| windows-386.zip     | Windows 32-bit (x86)    |
| linux-amd64.zip     | Linux 64-bit (x86_64)   |
| linux-arm64.zip     | Linux 64-bit (ARM)      |
| darwin-amd64.zip    | macOS 64-bit (Intel)    |
| darwin-arm64.zip    | macOS 64-bit (Apple Silicon) |


After downloading, extract the contents to a folder of your choice. Then follow the instructions below for your platform.


#### Install and Manage as a Service (All Platforms)

On **Windows, Linux, and macOS**, you can install, start, stop, and uninstall the backend as a service using the same commands. The service will be set to start automatically on boot and will survive restarts.

1. Extract the pre-built release for your platform to a folder (e.g., `C:\cww-backend` on Windows, `/opt/cww-backend` or `~/cww-backend` on Linux/macOS).
2. Open a terminal (**PowerShell as Administrator** on Windows, or a terminal with appropriate permissions on Linux/macOS) in that folder.
3. Run the following command to install the service:
   ```sh
   ./cww-backend install
   ```
   - To manage the service:
     ```sh
     ./cww-backend start      # Start the service
     ./cww-backend stop       # Stop the service
     ./cww-backend uninstall  # Uninstall the service
     ```
   - On Windows, use `./cww-backend.exe ...` if running from PowerShell.
   - Set environment variables (if required) before installing or starting the service (see "Environment Variables" below).

---

### 2. Install Firefox Extension

Download the Firefox signed addon from [this link](https://k00.fr/59jn0t05) and follow the steps below:

1. Download the `.xpi` file to your computer.
2. Open Firefox and either:
   - Drag and drop the `.xpi` file into the Firefox window.
   - Right-click the `.xpi` file and select "Open with Firefox".
3. Follow the prompts to install the addon.

---

## Configuring the Extension

1. Open Firefox → `about:addons` → find CWW → click the three dots → **Options**.
2. Set the **Service URL** to `http://localhost:54321` (default if running locally).
3. Set your **Provider URL**, **Model**, and **API Key** for your chosen LLM provider (see the table below or use the **Fill** buttons on the options page).
4. Click **Verify & Save** — the extension tests the connection to your provider before saving. If verification fails, check your key and URL.

### Provider Reference

| Provider | Provider URL | Example Model |
|---|---|---|
| Google Gemini | `https://generativelanguage.googleapis.com/v1beta/openai/` | `gemini-2.5-flash` |
| OpenAI | `https://api.openai.com/v1` | `gpt-4o` |
| Anthropic Claude | `https://api.anthropic.com/v1` | `claude-3-5-haiku-20241022` |
| OpenRouter | `https://openrouter.ai/api/v1` | `google/gemini-2.5-flash` |
| Ollama (local) | `http://localhost:11434/v1` | `gemma3:latest` |
| LM Studio (local) | `http://localhost:1234/v1` | *(your loaded model)* |

> For local servers (Ollama, LM Studio), enter any non-empty string as the API Key (e.g. `ollama`).

> Google Gemini offers some free requests per day — a good starting point if you don't have a paid API subscription. Get your key at [Google AI Studio](https://aistudio.google.com/app/apikey).

---

## PageSnap

PageSnap is a feature in the browser extension that captures the HTML of the currently open page and sends it directly to the backend for parsing, instead of having the backend fetch the page itself.

**Why it was introduced:**

**1. Access to pages already open in your browser**
Some pages cannot be fetched by the backend — paywalled articles, pages behind a sign-in, or JavaScript-rendered content that requires an active browser session. PageSnap bypasses this by reading the page content directly from your browser, where you are already authenticated and the page is fully loaded.

**2. Privacy control when using non-local LLMs**
When PageSnap is turned off, the backend fetches the page content server-side and sends it to your configured LLM provider. If you are using a cloud-based provider (e.g. OpenAI, Gemini, OpenRouter), this means the page content is transmitted to a third-party service. Keeping PageSnap off on pages with sensitive information avoids sending that content to an external LLM.

If you are using a local model (Ollama, LM Studio), there is no privacy concern — all data stays on your machine — so PageSnap can be safely turned on without issue.

---

## Using Ollama (Local Inference)

Ollama lets you run models fully offline on your own machine — no API key or internet connection required after the model is downloaded.

**Minimum recommended model:** `gemma3:1b` — 32K context window
```sh
ollama pull gemma3:1b
```

**Better quality:** `gemma3:latest` (4b) — 128K context window (requires more RAM/VRAM)
```sh
ollama pull gemma3:latest
```

> **Context length note:** `gemma3:1b` has a 32K context window, which is sufficient for short to medium articles but will struggle with long YouTube transcripts or lengthy pages. `gemma3:latest` (4b) offers a 128K context window and handles most content well, at the cost of higher memory usage. The backend has been optimised for Gemma/Ollama usage — responses are structured to keep token usage lean — but very long content may still produce incomplete or lower-quality answers on smaller models. For best results with `gemma3:1b`, stick to shorter articles or videos under ~15 minutes.

---

## Environment Variables

| Variable               | Default | Description                                         |
|------------------------|---------|-----------------------------------------------------|
| `CWW_PORT`             | `54321` | Port the backend listens on                         |
| `CWW_SESSION_TTL`      | `3600`  | Session idle timeout in seconds (default 1 hour)    |
| `CWW_CLEANUP_INTERVAL` | `300`   | Session GC interval in seconds (default 5 min)      |
| `GIN_MODE`             | `debug` | Set to `release` for production deployments         |
| `CWW_DEBUG`            | `0`     | Set to `1` for verbose LLM error logging            |

> **Windows service note:** Services run under the SYSTEM account and do not inherit user environment variables. Set any variables at Machine level:
> ```powershell
> [System.Environment]::SetEnvironmentVariable("CWW_PORT", "54321", "Machine")
> ```

---

## Backend Network Notes

- The backend is intended to run at your home/local IP. Data centre IPs and most VPN exit nodes are blocked by YouTube's transcript API.
- If you are on a VPN, bypass the backend's traffic through it for YouTube chat to work.
- Proxy support (previously available in the old Python-based backend) is not available in the current Go backend.

---

## Verifying the Backend is Running

Open your browser and visit:

    http://localhost:54321/about

A JSON response with the app name and version confirms the backend is up.

---

## Current Limitations

- Pages that require a sign-in can be used if you are already signed in and viewing the page — the PageSnap fallback will capture the content directly from your browser session. However, pages that require sign-in and cannot be fetched server-side are not supported without PageSnap.
- Private YouTube videos cannot be accessed.
- Chatting with non-article, non-YouTube pages may not work if the page blocks server-side fetching — use the PageSnap fallback in that case.
- Streaming responses are not supported yet — the full reply is returned at once after the LLM finishes generating.

---

## Using CWW

Click the **CWW addon icon** in Firefox to open the chatbox and start chatting with the current article or YouTube video.

![Summary Example](./summary.png)

---

Enjoy chatting with your web pages using CWW!





