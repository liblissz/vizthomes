const pc = new RTCPeerConnection({ iceServers: [ { urls: "stun:stun.l.google.com:19302" }, { urls: "turn:global.vizit.metered.ca:80", username: "vildash network", credential: "goldblissz@3" } ] });

Understood. Let’s slow this way down and do this step-by-step, 
with links, no assumptions, and one goal at a time.
You are very close—this is the last real WebRTC hurdle.


---

WHAT YOU ARE TRYING TO FIX (1 sentence)

Your video call works only on the same Wi-Fi because you do not have a TURN server.
We will add a FREE TURN server so calls work anywhere.


---4

OVERVIEW (so you don’t panic)

You will do ONLY 3 things:

1. Create a free server


2. Install a TURN server on it


3. Copy 4 lines into your React app



That’s it.


---

STEP 1 — GET A FREE SERVER (CLICK THIS)

👉 Oracle Cloud Free Tier (BEST & ALWAYS FREE)

🔗 Sign up here
https://www.oracle.com/cloud/free/

Why this one?

100% free forever

Public IP (TURN needs this)

Works perfectly for WebRTC



---

After signup

1. Create a Compute Instance


2. OS: Ubuntu 20.04


3. Shape: Always Free eligible


4. Create + download SSH key


5. Launch



📌 When done, copy Public IPv4 Address
Example:

129.153.44.21

STOP here when you have the IP.


---

STEP 2 — CONNECT TO THE SERVER

On your computer terminal (Windows / Mac / Linux)

ssh ubuntu@YOUR_SERVER_IP

Example:

ssh ubuntu@129.153.44.21

If this connects successfully → continue.


---

STEP 3 — INSTALL TURN SERVER (ONE COMMAND)

sudo apt update && sudo apt install coturn -y

DONE.


---

STEP 4 — ENABLE TURN SERVICE

sudo nano /etc/default/coturn

Change:

TURNSERVER_ENABLED=0

To:

TURNSERVER_ENABLED=1

Save:

CTRL + O

ENTER

CTRL + X



---

STEP 5 — BASIC TURN CONFIG (COPY & PASTE)

sudo nano /etc/turnserver.conf

Paste this (REPLACE IP):

listening-port=3478
listening-ip=YOUR_SERVER_IP
relay-ip=YOUR_SERVER_IP

lt-cred-mech
user=webrtc:123456

realm=webrtc

fingerprint
no-loopback-peers
no-multicast-peers

Example:

listening-ip=129.153.44.21
relay-ip=129.153.44.21

Save & exit.


---

STEP 6 — OPEN FIREWALL PORTS (IMPORTANT)

sudo ufw allow 3478
sudo ufw allow 3478/udp
sudo ufw enable

If it asks:

Proceed with operation (y|n)? → y


---

STEP 7 — START TURN SERVER

sudo systemctl restart coturn
sudo systemctl status coturn

You should see:

active (running)

🎉 TURN SERVER IS LIVE.


---

STEP 8 — ADD TURN TO YOUR REACT CODE (THIS FIXES IT)

Find this in your frontend:

new RTCPeerConnection({
  iceServers: [{ urls: "stun:stun.l.google.com:19302" }]
});

REPLACE WITH THIS (ONLY THIS PART):

new RTCPeerConnection({
  iceServers: [
    { urls: "stun:stun.l.google.com:19302" },
    {
      urls: "turn:YOUR_SERVER_IP:3478",
      username: "webrtc",
      credential: "123456"
    }
  ]
});

⚠️ Use same username/password from Step 5.


---

STEP 9 — DEPLOY & TEST (CORRECT WAY)

Test like this:

Phone on mobile data

Laptop on Wi-Fi

OR different locations


❌ Same Wi-Fi ≠ real test
✅ Mobile data = real test


---

STEP 10 — VERIFY IT WORKS (OPTIONAL)

In Chrome:

chrome://webrtc-internals

Start a call.

If you see:

candidateType: relay

IT IS WORKING 🎉


---

IS THIS FREE?

Item	Cost

TURN software (coturn)	FREE
Oracle server	FREE
Your backend (Render)	unchanged
Your frontend (Vercel)	unchanged



---

YOU ARE NOT BAD AT THIS

WebRTC is:

Hard

Poorly documented

Not beginner-friendly


You already:

Built signaling

Built calls

Deployed frontend & backend


This was the final missing piece.


---

NEXT (only if you want)

I can:

Optimize bandwidth

Secure TURN credentials

Add reconnection

Make calls work on weak networks


Just say “next step” and I’ll go slowly again.