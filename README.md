# CWW (Chat With Web)
![Explain Example](./imgs/explain.png)
CWW (Chat With Web) lets you chat with the currently opened tab in Firefox. It enables you to interact with articles and YouTube videos directly from your browser. However, chatting with non-article sites is not supported at this time. Additionally:
- Chatting with pages that require a sign-in is currently not supported.
- Private YouTube videos cannot be accessed for chat.

The default language of chat is English, but you can chat in any language as long as it is supported by Gemini.

The purpose of the app is to enhance your understanding of articles and YouTube videos in an educational context. It can:
- Summarize lengthy articles into clear and concise points.
- Extract key insights or arguments from opinion pieces.
- Provide detailed explanations for complex topics covered in YouTube videos.
- Help you grasp the intention and perspective of the writer or creator.

The app integrates with Google Gemini, so you will need to obtain your Gemini API key by visiting [Google AI Studio](https://aistudio.google.com/app/apikey). Gemini offers **250 free requests per day** (with a maximum of 10 requests per minute), allowing you to chat with a large number of pages daily.

---

## Requirements

To use CWW, you need to:
1. Run the **cww-backend**.
2. Install the **Firefox Addon**.

---

## Installation Guide

### 1. Install cww-backend

#### As a Windows Service (Recommended for Windows)

Download the `windows_x86_64.zip` release from [this link](https://k00.fr/eztxz1en) and follow the steps below:

1. Extract all files to a folder (e.g., `C:\cww-backend`).
2. Open **PowerShell as Administrator** in that folder.
3. Run the following command to install and start the service:

   ```powershell
   ./service.ps1 -action install
   ```
   - The service will be set to start automatically on boot and will survive Windows restarts.
   - To stop, start, or uninstall the service, use:
     ```powershell
     ./service.ps1 -action stop
     ./service.ps1 -action start
     ./service.ps1 -action uninstall
     ```
   - You can set environment variables to customize the backend, including the port and session settings, even when running as a Windows service.
   - See the **Environment Variables** section below for how to set up environment variables in Windows.
   - You must set these variables before installing or starting the service for them to take effect.

#### Using Docker (Works on Any OS)

1. Make sure Docker is installed and running.

> **Note:** The Docker method works on both x86-based (most Windows/Linux PCs, Intel-based Macs) and ARM64-based systems (Apple Silicon Macs, Raspberry Pi, etc.) as long as Docker is installed. Be sure to use the correct image tag for your system: `asimijaz/cww-backend:latest` for x86, and `asimijaz/cww-backend:arm64` for ARM64.

2. Pull the appropriate image for your system:
   - For x86-based systems (most Windows/Linux PCs, Intel Macs):
     ```powershell
     docker pull asimijaz/cww-backend:latest
     ```
   - For ARM64-based systems (Apple Silicon Macs, Raspberry Pi, etc.):
     ```powershell
     docker pull asimijaz/cww-backend:arm64
     ```

3. Run the container as a daemon (background) and persist across Windows or Docker restarts:
   - For x86-based systems:
     ```powershell
     docker run -d --restart unless-stopped -p 54321:54321 asimijaz/cww-backend:latest
     ```
   - For ARM64-based systems:
     ```powershell
     docker run -d --restart unless-stopped -p 54321:54321 asimijaz/cww-backend:arm64
     ```
   - This exposes port **54321** on your host. You can change the left side of `-p` if you want to use a different external port.
   - The container will automatically restart if Docker or Windows restarts.

4. Set environment variables as needed:
   - See the [Environment Variables for CWW Backend](#environment-variables-for-cww-backend) section for available variables and their defaults.
   - **Windows:**
     1. Open the Start Menu and search for `Environment Variables`.
     2. Click on `Edit the system environment variables`.
     3. In the System Properties window, click `Environment Variables...`.
     4. Under `User variables` or `System variables`, click `New...`.
     5. Enter the variable name (e.g., `CWW_PORT`) and value (e.g., `54321`).
     6. Click OK to save. Repeat for each variable you want to set.
     7. Restart the cww-backend service for changes to take effect.
   - **Docker:**
     Example to set variables:
     ```sh
     docker run -d --restart unless-stopped \
       -e CWW_PORT=54321 -e CWW_SESSION_TTL=3600 -e CWW_CLEANUP_INTERVAL=300 \
       -e CWW_USE_YT_PROXY=true -e CWW_WEBSHARE_USER=youruser -e CWW_WEBSHARE_PASS=yourpass \
       -p 54321:54321 asimijaz/cww-backend:latest
     ```

---

### 2. Install Firefox Extension

Download the Firefox signed addon from [this link](https://k00.fr/rhi3whcv) and follow the steps below:

1. Download the `.xpi` file to your computer.
2. Open Firefox and either:
   - Drag and drop the `.xpi` file into the Firefox window.
   - Right-click the `.xpi` file and select "Open with Firefox".
3. Follow the prompts to install the addon.

---

## Setting Up the Gemini API Key and Service URL

1. Open Firefox and navigate to `about:addons`.
2. Locate the CWW extension and click on the three dots next to it.
3. Select `Options` from the dropdown menu.
4. Set the **Service URL** to `http://localhost:54321` (if running the backend locally on Windows).
5. Enter your **Gemini API Key** obtained from [Google AI Studio](https://aistudio.google.com/app/apikey).
6. Click `Save Settings` to apply the changes.

---

## Environment Variables for CWW Backend

The following environment variables can be set to customize the backend behavior:

| Variable                | Default      | Description                                                      |
|-------------------------|-------------|------------------------------------------------------------------|
| CWW_PORT                | 54321       | Port for backend server                                          |
| CWW_SESSION_TTL         | 3600        | Session time-to-live in seconds (default: 1 hour)                |
| CWW_CLEANUP_INTERVAL    | 300         | Session cleanup interval in seconds (default: 5 minutes)         |
| CWW_USE_YT_PROXY        | unset/false | Set to `true` to use a proxy for YouTube video chat              |
| CWW_WEBSHARE_USER       | unset       | Webshare proxy username (required if using proxy)                |
| CWW_WEBSHARE_PASS       | unset       | Webshare proxy password (required if using proxy)                |

---
## Backend Network/Proxy Notes

- The backend is intended to be run at your home IP address.
- If you are using a VPN, you must bypass the backend for chat with YouTube videos to work, or enable the proxy option.
- Currently, only the Webshare residential proxy is supported. This is a paid service; you must purchase their residential proxy package to use it. Visit [Webshare.io](https://www.webshare.io/) for more details.
---

To check if the backend is running successfully, open your browser and go to:

    http://localhost:54321/about

This confirms the backend is up and provides version and copyright information.

---

## Using CWW to Chat with Pages

To chat with any supported page, click the **CWW addon icon** in Firefox. This will open the chatbox, allowing you to interact with articles or YouTube videos directly.

![Summary Example](./imgs/summary.png)
---

CWW current version is not open source, but the intention is to make future versions open source. A key future goal is to eliminate the need for a backend, making the app more streamlined and accessible.

---

Enjoy chatting with your web pages using CWW!
