# Node-RED Alexa Remote Management Flow

This Node-RED flow is designed to maintain a resilient connection with the Amazon Alexa ecosystem, ensuring that session cookies and CSRF tokens remain valid at all times.

## 📋 Table of Contents
- [Overview](#overview)
- [Flow Logic](#flow-logic)
- [Key Nodes](#key-nodes)
- [Configuration](#configuration)

## 📖 Overview
This flow solves the common issue of session expiration in Alexa Remote nodes. It implements a "self-healing" logic that detects authentication loss and re-initializes the session without manual intervention.

## 🧠 Flow Logic

### Initialization & Refresh
- **Startup:** Tries an initial handshake as soon as Node-RED deploys/starts.
- **Midnight Refresh:** Triggers a proactive full refresh daily at 00:01.
- **Context Storage:** The CSRF token is stored in `flow.alexa`. The flow only executes a full refresh sequence if the token has changed, preventing unnecessary overhead.

### Monitoring
- Every 120 seconds (`Test`), the system verifies the current auth status.
- A `delay` node with a random interval (1-3s) is used to prevent suspicious traffic patterns.

### Error Recovery
A `Catch` node specifically monitors failures from the "checkAuthentication" node. If an error is caught, it sends a "refresh" payload to restart the authentication chain.

## 🛠 Key Nodes

| Node Name | Type | Function |
| :--- | :--- | :--- |
| `Test` | Inject | Cyclic trigger (2 min) for status check. |
| `Alexa Remote Init` | alexa-remote-init | Handles the initial handshake and cookie refresh. |
| `Check Auth` | alexa-remote-other | Verifies if the current session is still valid. |
| `Catch` | catch | Intercepts auth errors to trigger recovery. |

## ⚙️ Configuration
To use this flow:
1. Ensure `node-red-contrib-alexa-remote2` is installed.
2. Configure the Account node with your local IP for Proxy authentication.
3. Verify that the path `/config/amazon.txt` is writable by the Node-RED process.
