# Ollama + Open WebUI Setup on RunPod (Manual Installation)

This guide shows how to **manually install and connect Ollama with Open WebUI on a RunPod GPU instance** without using Docker templates.
It is useful for developers and educators who want to **demonstrate how the LLM backend (Ollama) connects to a web interface (Open WebUI).**


# Single Block command
```
apt-get update && apt-get upgrade -y
apt install -y zstd
curl -fsSL https://ollama.com/install.sh | sh

nohup ollama serve > ollama.log 2>&1 &

pip install -U open-webui

OLLAMA_BASE_URL=http://127.0.0.1:11434 nohup open-webui serve --port 8005 > webui.log 2>&1 &
```


---

# Architecture

```
Browser
   ↓
Open WebUI (Port 8005)
   ↓
Ollama API (Port 11434)
   ↓
Local LLM Models
```

* **Ollama** → Runs the AI models
* **Open WebUI** → Provides a ChatGPT-like interface
* **RunPod** → Hosts the GPU server

---

# Step 1 — Update System

Run the following commands inside your RunPod terminal.

```bash
apt-get update && apt-get upgrade -y
apt install -y zstd
```

---

# Step 2 — Install Ollama

Install Ollama using the official install script.

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

---

# Step 3 — Start the Ollama Server

Start Ollama in the background.

```bash
nohup ollama serve > ollama.log 2>&1 &
```

Ollama will run on:

```
http://localhost:11434
```

---

# Step 4 — Install Open WebUI

Install Open WebUI using pip.

```bash
pip install -U open-webui
```

---

# Step 5 — Start Open WebUI

Run Open WebUI and connect it to Ollama.

```bash
OLLAMA_BASE_URL=http://127.0.0.1:11434 nohup open-webui serve --port 8005 > webui.log 2>&1 &
```

Open WebUI will now run on:

```
http://localhost:8005
```

---

# Step 6 — Access Open WebUI

RunPod exposes ports using a proxy URL.

Open the following in your browser:

```
https://YOUR-POD-ID-8005.proxy.runpod.net
```

Example:

```
https://rigz16scnzzzar-8005.proxy.runpod.net
```

You should now see the **Open WebUI interface**.

---

# Step 7 — Download a Model (Optional but Recommended)

To make the interface immediately usable, download a model.

Example:

```bash
ollama pull qwen2.5-coder:1.5b
```

or

```bash
ollama pull llama3
```

---

# RunPod Port Configuration

RunPod requires ports to be **exposed manually**.

You must expose:

| Port  | Purpose              |
| ----- | -------------------- |
| 8005  | Open WebUI interface |
| 11434 | Ollama API           |

### Important

* **Port 8005 can be changed** to any other port.
* If you change the port, you must also update the command:

```
open-webui serve --port YOUR_PORT
```

and access it using:

```
https://YOUR-POD-ID-YOUR_PORT.proxy.runpod.net
```

### Example

If using port `7860`:

```
open-webui serve --port 7860
```

Access:

```
https://YOUR-POD-ID-7860.proxy.runpod.net
```

---

# Using Ollama API Outside the VPS

If you want to access the Ollama API from external applications:

Expose **port 11434** in RunPod.

Your API endpoint will become:

```
http://YOUR-POD-IP:11434
```

Example API call:

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "llama3",
  "prompt": "Explain transformers simply"
}'
```

---

# Useful Commands

Check running services:

```bash
ss -lptn | grep -E "11434|8005"
```

Expected output:

```
11434 → ollama
8005 → open-webui
```

---

# Logs

Ollama logs:

```
ollama.log
```

Open WebUI logs:

```
webui.log
```

View logs with:

```bash
tail -f webui.log
```

---

# Summary

This setup provides:

* Local LLM inference using **Ollama**
* A modern chat interface using **Open WebUI**
* GPU acceleration through **RunPod**

```
Browser
   ↓
Open WebUI
   ↓
Ollama
   ↓
Local AI Models
```

You now have your **own private ChatGPT-like server running on RunPod**.

---
