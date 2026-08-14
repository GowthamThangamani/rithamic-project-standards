# Rithamic Login Frontend (`rithamic-login`) Integration Guide

This guide details how the centralized authentication web app (`rithamic-login`, hosted at `auth.rithamic.co.in` or `login.rithamic.co.in`) communicates with `rithamic-backend-api` and client applications.

---

## 1. Overview of `rithamic-login`

`rithamic-login` is the dedicated, branded authentication portal for the Rithamic software ecosystem.
- Clean Google/Microsoft-style single login interface.
- Accepts URL parameters: `?project=...&returnUrl=...`.
- Dispatches OTP requests and social login flows.
- Automatically generates and forwards the **SSO Ticket** back to `returnUrl`.

---

## 2. URL Parameters Received by `rithamic-login`

| Parameter | Type | Required? | Example | Description |
|:---|:---|:---:|:---|:---|
| `project` | String | No | `rithamic_familytree` | Target project context (applies custom logo/theme). |
| `returnUrl` | String (URI) | No | `https://familytree.rithamic.co.in/auth/callback` | Where to redirect user after successful login. |
| `mode` | String | No | `otp` \| `oauth` | Default login mode tab. |

---

## 3. Frontend Authentication Workflow

### Step 1: User enters email on `rithamic-login`
```javascript
const res = await fetch('https://api.rithamic.co.in/api/auth/' + projectKey + '/otp/request', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ recipient: email, channel: 'email' })
});
```

### Step 2: User verifies OTP
```javascript
const res = await fetch('https://api.rithamic.co.in/api/auth/' + projectKey + '/otp/verify', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ recipient: email, otp: code })
});
const { token, user } = await res.json();
```

### Step 3: Redirecting Back to Calling App with SSO Ticket
If `returnUrl` was provided:
```javascript
// Request a 60s single-use SSO Ticket
const ticketRes = await fetch('https://api.rithamic.co.in/api/auth/sso/generate-ticket', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer ' + token
  },
  body: JSON.stringify({ targetProjectKey: projectKey })
});
const { ticket, redirectUrl } = await ticketRes.json();

// Redirect back to calling application
const finalRedirect = returnUrl.includes('?') 
  ? `${returnUrl}&ticket=${ticket}` 
  : `${returnUrl}?ticket=${ticket}`;

window.location.href = finalRedirect;
```

---

## 4. How Client Applications (`rithamic-familytree`, `rithamic-pos`, etc.) Consume the Ticket

On `https://familytree.rithamic.co.in/auth/callback`:
```javascript
const urlParams = new URLSearchParams(window.location.search);
const ticket = urlParams.get('ticket');

if (ticket) {
  // Exchange ticket with backend API for local JWT
  const res = await fetch('https://api.rithamic.co.in/api/auth/sso/exchange', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ ticket, targetProjectKey: 'rithamic_familytree' })
  });
  const data = await res.json();

  if (data.token) {
    sessionStorage.setItem('authToken', data.token);
    // Redirect to home dashboard
    window.location.href = '/';
  }
}
```
