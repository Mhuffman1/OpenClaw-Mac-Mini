# OpenClaw Mac Mini — Build Guide

> **Written for Matt.** Two tracks: stuff you do with your hands (🖐️) and stuff Claude Code does for you (🤖). You handle the physical setup, GUI clicks, and account creation. Claude Code handles every Terminal command, config file, and installation.
>
> **Total estimated time:** ~90 minutes
>
> **What you need before starting:**
> - Mac Mini M4 (still in the box)
> - A monitor with HDMI input + an HDMI cable
> - Logitech MX Keys keyboard + Logitech MX Master mouse (you already have these)
> - Your Wi-Fi password
> - Your Apple ID email and password (or be ready to create one)
> - A credit card (for API billing — you won't be charged much, but you need one on file)
> - Your phone (for verification codes)

---

## Security — The Short Version

You're running this on a private home network with four family members. You're not a target. The three things that actually matter:

1. **Docker sandboxing** (Phase 5) — the agent runs in a container and can only see files you put in the workspace folder. If it goes haywire, it can't touch your real stuff.
2. **API spending limits** (Phase 4) — caps both Anthropic and OpenAI at $50/month so a bug can't run up a bill.
3. **Read community skills before installing them** — 12% of skills on ClawHub are confirmed malicious. Only install skills you wrote, we built together, or you've read every line of.

That's it. The rest of the guide is setup, not security.

---

## Phase 1: 🖐️ Unbox and Boot (30-45 min)

Everything in this phase is physical — hands and eyeballs only.

### Step 1.1: Physical Setup

1. Take the Mac Mini out of the box. It's a small silver/black square, about 5 inches on each side.
2. Find the **power cable** (it's in the box). Plug the round end into the back of the Mac Mini, the other end into a wall outlet.
3. Find an **HDMI cable** (you may need to buy one if it didn't come with your monitor). Plug one end into the **HDMI port** on the back of the Mac Mini (it's on the right side of the back panel). Plug the other end into your TV/monitor.
4. **DO NOT press the power button yet.** First, make sure your monitor/TV is on and set to the correct HDMI input.

### Step 1.2: Connect Your MX Keys and MX Master

Both of your Logitech devices can connect via Bluetooth or a USB Bolt receiver. **Bluetooth is simplest for initial setup:**

1. **MX Keys keyboard**: Turn it on (switch on the top edge). Press one of the **Easy-Switch buttons** (1, 2, or 3 at the top-left of the keyboard) and **hold it for 3 seconds** until the LED for that number starts blinking fast. This puts it in pairing mode. Pick a number you haven't used — this will be your Mac Mini slot.
2. **MX Master mouse**: Turn it on (switch on the bottom). Press the **Easy-Switch button** on the bottom of the mouse to pick a slot (1, 2, or 3), then **hold it for 3 seconds** until the LED blinks fast.
3. Leave both devices blinking — the Mac Mini will find them during setup.

**Alternative: Logi Bolt USB receiver.** If you have the small USB receiver that came with either device, you can plug it into one of the USB-A ports on the back of the Mac Mini. Devices paired to that receiver will connect immediately without Bluetooth setup. If you go this route, skip the Bluetooth pairing screens during macOS setup.

### Step 1.3: Power On

1. The **power button** is on the back of the Mac Mini, bottom-right corner. It's a small round button. Press it once.
2. You'll hear a startup chime (or see the Apple logo on screen). Wait.
3. If your MX Keys and MX Master are in Bluetooth pairing mode (blinking), the Mac Mini should detect them during setup. You'll see a screen that says "Bluetooth Keyboard" or "Bluetooth Mouse" — click **Connect** (or **Pair**) for each one. If it asks for a pairing code on the keyboard, type the numbers shown on screen and press Enter.
4. If neither device connects, plug in the Logi Bolt USB receiver as a fallback — everything will just work immediately.

### Step 1.4: macOS Setup Assistant

The screen will show "hello" in various languages. Here's every screen you'll walk through:

**Screen: Language**
- Select **English** (or your preferred language)
- Click the arrow to continue

**Screen: Country or Region**
- Select **United States**
- Click **Continue**

**Screen: Accessibility**
- Click **Not Now** (unless you need accessibility features)

**Screen: Wi-Fi**
- Your Wi-Fi network name will appear in a list
- Click on your network name
- Type your Wi-Fi password in the password field
- Click **Continue**
- Wait for it to connect (10-30 seconds)

**Screen: Data & Privacy**
- This is just an info screen. Click **Continue**

**Screen: Migration Assistant**
- You'll see options to transfer data from another Mac, Time Machine backup, or Windows PC
- Click **"Not Now"** — we're setting this up fresh
- If it asks "Are you sure?" click **"Don't Transfer"**

**Screen: Apple ID**
- If you have an Apple ID: type your email and password, click **Sign In**
- If you don't: click **"Create Apple ID"** and follow the prompts (you'll need your email and phone for verification)
- You can also click **"Set Up Later"** and skip this — but I recommend signing in now because you'll need the App Store later

**Screen: Terms and Conditions**
- Click **Agree**, then **Agree** again on the popup

**Screen: Computer Account**
- **Full Name:** Matt Huffman (or whatever you want)
- **Account Name:** matt (this is your username — keep it short, lowercase, no spaces)
- **Password:** Pick something strong. You'll use this to log into the Mac and to run admin commands.
- **Hint:** Optional. Skip it or add one.
- Click **Continue**

**Screen: Location Services**
- Click **"Enable Location Services"** (you can turn it off later)
- Click **Continue**

**Screen: Select Your Time Zone**
- It should auto-detect. If not, select **Eastern Time** (since you're in NC)
- Click **Continue**

**Screen: Analytics**
- Uncheck everything if you want privacy. Or leave defaults.
- Click **Continue**

**Screen: Screen Time**
- Click **"Set Up Later"**

**Screen: Appearance**
- Pick **Light**, **Dark**, or **Auto**
- Click **Continue**

**You're in.** You should now see the macOS desktop — a big empty screen with a dock (row of app icons) at the bottom.

### Step 1.5: First Things After Boot

**Update macOS** (do this first, it takes 15-30 min):
1. Click the **Apple menu** (top-left corner of the screen, the Apple logo)
2. Click **"System Settings"**
3. In the left sidebar, click **"General"**
4. Click **"Software Update"**
5. If there's an update available, click **"Update Now"** or **"Upgrade Now"**
6. Let it download and install. The Mac will restart.

---

## Phase 2: 🖐️ System Settings (15-20 min)

All GUI — click through these in System Settings.

### Step 2.1: Enable FileVault (Disk Encryption)

1. Click **Apple menu** (top-left) → **System Settings**
2. In the left sidebar, click **"Privacy & Security"**
3. Scroll down to **"FileVault"**
4. Click **"Turn On FileVault"**
5. Choose **"Allow my iCloud account to unlock my disk"** (easier to recover if you forget your password)
6. Click **Continue**
7. It will start encrypting in the background. You don't need to wait — just let it run.

### Step 2.2: Enable the Firewall

1. Still in **System Settings → Privacy & Security**
2. Scroll to **"Firewall"** (it might be under "Network" in newer versions — if you don't see it in Privacy & Security, go to **System Settings → Network → Firewall**)
3. Click the toggle to turn the Firewall **ON**
4. Click **"Options..."** if available
5. Enable **"Enable stealth mode"** — this hides your Mac from network scans
6. Click **OK**

### Step 2.3: Energy Settings (Keep It Running 24/7)

1. **System Settings → Energy** (or "Energy Saver" on some versions)
2. Find **"Prevent automatic sleeping when the display is off"** → Toggle **ON**
3. Find **"Wake for network access"** → Toggle **ON**
4. Find **"Start up automatically after a power failure"** → Toggle **ON**
5. Set **"Turn display off after"** → **Never** (or 15 minutes if you want the TV to sleep — the Mac Mini will keep running either way)

### Step 2.4: Enable Screen Sharing (For Remote Access From Laptop)

1. **System Settings → General → Sharing**
2. Find **"Screen Sharing"** → Toggle **ON**
3. Find **"Remote Login"** (SSH) → Toggle **ON**
4. Note: You'll be able to connect from your laptop later via **Finder → Go → Connect to Server** or by using the built-in Screen Sharing app

### Step 2.5: macOS Permissions (For Terminal and Claude Code)

1. **System Settings → Privacy & Security**
2. Find **"Full Disk Access"** → Click it
3. Click the **"+"** button (or toggle for Terminal)
4. Navigate to **Applications → Utilities → Terminal.app** → Click **Open**
5. Go back to Privacy & Security
6. Find **"Accessibility"** → Add Terminal.app the same way
7. Find **"Screen Recording"** → Add Terminal.app the same way (if available)

---

## Phase 3: 🖐️ Bootstrap Tools (15 min)

Three things you type into Terminal by hand. After this, Claude Code takes over.

### Step 3.1: Open Terminal

1. Press **Command + Space** (this opens Spotlight search — a search bar in the middle of your screen)
2. Type **Terminal**
3. Click on **Terminal.app** in the results
4. A window opens with a blinking cursor. This is where you'll type commands.

**TIP:** Right-click on the Terminal icon in the dock (bottom of screen) and click **"Options" → "Keep in Dock"** so you can find it easily later.

### Step 3.2: Install Xcode Command Line Tools

Type this and press **Enter**:

```
xcode-select --install
```

A popup will appear. Click **"Install"**, then **"Agree"** to the license. Wait for it to download and install (5-10 min).

### Step 3.3: Install Homebrew

Type this whole thing into Terminal (it's one long command — you can copy and paste it):

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Press **Enter**. It will:
1. Ask for your Mac password (the one you set during setup). **When you type your password, nothing will appear on screen — no dots, no stars, nothing. This is normal. Just type it and press Enter.**
2. Ask you to press Enter to confirm installation. Press **Enter**.
3. Download and install (2-5 min).

**IMPORTANT:** After Homebrew installs, it will show some text that says something like "Run these commands to add Homebrew to your PATH." It will look something like:

```
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

**Copy and paste those EXACT lines** (both of them) into Terminal and press Enter after each one. This makes Homebrew available every time you open Terminal.

Verify it worked:
```
brew --version
```
You should see something like `Homebrew 4.x.x`. If you see "command not found," the PATH step above didn't work — try closing and reopening Terminal, then try again.

### Step 3.4: Install Claude Code

```
brew install --cask claude-code
```

Wait for it to install. Then launch it:

```
claude
```

Claude Code will start and ask you to authenticate. It will open a browser window — **sign in with your Claude account** (huffman.matthew@gmail.com). This is a one-time OAuth process.

Once authenticated, you'll see a prompt inside Terminal where you can type natural language to Claude Code. **This is your new command center.** From here on out, you tell Claude Code what to do and it does it.

### ✅ Phase 3 Checkpoint

You should now have:
- Homebrew working (`brew --version` shows a version)
- Claude Code running in Terminal with an authenticated session

If both are true, you're ready. **Everything from here is Claude Code's job.**

---

## Phase 4: 🖐️ Create Accounts and Collect Credentials (~30 min)

You need to create API keys and set up Discord. This is all web browser work — do it while Claude Code waits. Collect everything into a note so you can hand it all to Claude in Phase 5.

### Step 4.1: Anthropic API Key

**In your web browser (Safari or Chrome on the Mac Mini):**

1. Go to **https://console.anthropic.com**
2. **Sign in** with your Claude account (huffman.matthew@gmail.com)
   - If you don't have a developer account yet, click **"Sign Up"** and use your same email
   - You'll need to verify your phone number via SMS

3. Once logged in, look at the **left sidebar**. Find **"API Keys"** (it may have a key icon)
4. Click **"API Keys"**
5. Click **"+ Create Key"**
6. Name it: **"OpenClaw Mac Mini"**
7. Click **"Create"** (or "Add")
8. **THE KEY WILL ONLY BE SHOWN ONCE.** It looks like: `sk-ant-api03-xxxxxx...`
9. **COPY IT IMMEDIATELY.** Paste it into a note (Notes app, TextEdit, whatever you have open).

10. Now set up billing. In the left sidebar, find **"Plans & Billing"** or **"Billing"**
11. Click **"Buy Credits"** (or "Add to Balance")
12. Start with **$10** — this is enough to test everything
13. Enter your credit card information
14. Click **Purchase**

15. **Set a spending limit:**
    - Look for "Usage Limits" or "Spending Limit" in the billing section
    - Set a monthly limit of **$50** to start (you can increase later)

16. **Enable auto-reload** (optional):
    - Toggle auto-reload ON
    - Set "Reload when balance drops below" → **$5**
    - Set "Reload amount" → **$10**

### Step 4.2: OpenAI API Key

**In your web browser:**

1. Go to **https://platform.openai.com**
   - This is DIFFERENT from chatgpt.com. This is the developer platform.
2. **Sign in** with your OpenAI account (same email you use for ChatGPT)
   - You may need to verify your phone number
   - If it asks you to create an organization, just use your name

3. In the left sidebar, find **"API Keys"** (or go directly to `platform.openai.com/api-keys`)
4. Click **"Create new secret key"**
5. Name it: **"OpenClaw Mac Mini"**
6. Permissions: **"All"** (or leave default)
7. Click **"Create secret key"**
8. **SAME DEAL — SHOWN ONCE ONLY.** It looks like: `sk-xxxxxx...`
9. **COPY IT IMMEDIATELY.** Paste into your notes.

10. Now set up billing. In the left sidebar, find **"Settings"** → **"Billing"**
11. Click **"Add payment details"**
12. Enter your credit card
13. Purchase **$10** in credits to start
14. Set a monthly spending limit of **$50**

### Step 4.3: Discord Setup

Do all of this in your web browser. It's a lot of clicking, but it's all straightforward.

**Create a Discord account** (if you don't have one):
1. Go to **https://discord.com** → **"Register"**
2. Sign up with your email, create a username and password
3. Verify your email
4. **Download the Discord app** on the Mac Mini: **https://discord.com/download** → "Download for Mac"
5. Open the downloaded file and drag Discord to your Applications folder
6. Open Discord and sign in

**Create the family server:**
1. Click the **"+"** button in the left sidebar
2. Click **"Create My Own"** → **"For me and my friends"**
3. Name it: **"Huffman Homeschool"**
4. Click **"Create"**

**Quick hygiene:**
5. Click the **server name** at the top → **"Server Settings"**
6. Go to **Roles** → click **"@everyone"**
7. Turn OFF **"Create Invite"** (so the kids can't accidentally invite friends)
8. Click **"Save Changes"**

**Create the channels:**

Right-click on the server name → **"Create Category"** → **📚 Junie**
Inside that category, create text channels: **junie-lessons**, **junie-submit**, **junie-grades**

Right-click on the server name → **"Create Category"** → **📚 Addie**
Inside that category: **addie-lessons**, **addie-submit**, **addie-grades**

Right-click on the server name → **"Create Category"** → **🔧 System**
Inside that category: **logs**, **alerts**

Create one more channel outside any category: **matt-command**

Your server should look like:
```
Huffman Homeschool
├── #general
├── #matt-command
├── 📚 Junie
│   ├── #junie-lessons
│   ├── #junie-submit
│   └── #junie-grades
├── 📚 Addie
│   ├── #addie-lessons
│   ├── #addie-submit
│   └── #addie-grades
└── 🔧 System
    ├── #logs
    └── #alerts
```

**Create kid accounts:**
1. Use email aliases: `huffman.matthew+junie@gmail.com` and `huffman.matthew+addie@gmail.com` (Gmail treats these as the same inbox but Discord sees them as different accounts)
2. Go to **https://discord.com/register** in a private/incognito window
3. Create accounts for Junie and Addie
4. From your main account, **Invite People** to the server → copy the link → accept on each kid's account

**Create the bot:**
1. Go to **https://discord.com/developers/applications**
2. Sign in with YOUR Discord account
3. Click **"New Application"** → Name: **"HomeschoolBot"** → Agree → **"Create"**
4. Left sidebar → **"Bot"**
5. Click **"Reset Token"** → **"Yes, do it!"**
6. **Copy the token** → paste into your notes. Label it "Discord Bot Token."
7. Scroll down to **"Privileged Gateway Intents"** → Toggle ON all three:
   - **Presence Intent** → ON
   - **Server Members Intent** → ON
   - **Message Content Intent** → ON (CRITICAL)
8. Click **"Save Changes"**

**Invite the bot to your server:**
1. Left sidebar → **"OAuth2"**
2. Under SCOPES: check ✅ **"bot"** and ✅ **"applications.commands"**
3. Under BOT PERMISSIONS: check ✅ View Channels, Send Messages, Send Messages in Threads, Create Public Threads, Read Message History, Embed Links, Attach Files, Add Reactions, Manage Threads
4. Copy the **Generated URL** at the bottom
5. Paste into your browser → select **"Huffman Homeschool"** → **"Authorize"**

**Get channel and server IDs:**
1. In Discord → **User Settings** (gear icon, bottom-left) → **Advanced** → Toggle **"Developer Mode"** → **ON**
2. Close settings
3. Right-click on the **server name** → **"Copy Server ID"** → paste into notes, label "Guild ID"
4. Right-click each channel → **"Copy Channel ID"** → paste and label:
   - matt-command
   - junie-lessons
   - junie-submit
   - addie-lessons
   - addie-submit

### ✅ Phase 4 Checkpoint

Before moving on, you should have ALL of these in your notes:

```
Anthropic API Key:    sk-ant-api03-...
OpenAI API Key:       sk-...
Discord Bot Token:    ...
Guild ID:             ...
matt-command ID:      ...
junie-lessons ID:     ...
junie-submit ID:      ...
addie-lessons ID:     ...
addie-submit ID:      ...
```

If any are missing, go back and get them. Phase 5 needs every single one.

---

## Phase 5: 🤖 Hand It to Claude Code (~30 min)

This is where Claude Code earns its keep. You'll give it a series of prompts. It does all the work. You watch.

**Go back to your Terminal where Claude Code is running.** If you closed it, open Terminal and type `claude` to restart it.

### Prompt 1: Install Everything

Copy and paste this into Claude Code:

```
I need you to set up this Mac Mini as a development machine. Do the following in order:

1. Install Node.js 22 via Homebrew: brew install node@22 (run brew link node@22 if needed)
2. Install Python 3 via Homebrew: brew install python3
3. Install Git via Homebrew: brew install git
4. Create the workspace folder: mkdir -p ~/openclaw-workspace
5. Install OpenClaw: curl -fsSL https://openclaw.ai/install.sh | bash
   - If that fails, try: npm install -g openclaw@latest
   - Run the onboarding: openclaw onboard --install-daemon
   - For onboarding questions: select Anthropic as provider, skip the API key for now, skip messaging channel, enable web search if offered, yes to daemon
6. Install Docker: brew install --cask docker

After each install, verify it worked (node -v, python3 --version, git --version, openclaw status, docker --version).

Tell me the results of every verification check.
```

**When it finishes:** It will tell you Docker Desktop needs to be opened. Do that now:
1. Open **Docker Desktop** from Applications
2. It will ask for permission to install helper tools — click **"OK"** and enter your password
3. Wait for Docker to finish starting (the whale icon in the menu bar will stop animating)
4. Click the **gear icon** (Settings) → Under General → check ✅ **"Start Docker Desktop when you sign in"** → **"Apply & Restart"**

### Prompt 2: Configure OpenClaw

Now paste this, **filling in your actual values** from the Phase 4 notes:

```
Configure OpenClaw with these settings:

1. Lock gateway to localhost:
   openclaw config set gateway.host 127.0.0.1
   openclaw config set dm.policy pairing

2. Enable Docker sandboxing:
   openclaw config set sandbox.enabled true
   openclaw config set sandbox.provider docker
   openclaw config set sandbox.mounts '["~/openclaw-workspace:/workspace"]'

3. Write the full config file at ~/.openclaw/openclaw.json with this content:

{
  "providers": {
    "anthropic": {
      "apiKey": "PASTE-YOUR-ANTHROPIC-KEY-HERE"
    },
    "openai": {
      "apiKey": "PASTE-YOUR-OPENAI-KEY-HERE"
    }
  },
  "agent": {
    "model": "anthropic/claude-sonnet-4"
  },
  "gateway": {
    "port": 18789,
    "host": "127.0.0.1"
  },
  "sandbox": {
    "enabled": true,
    "provider": "docker",
    "mounts": ["~/openclaw-workspace:/workspace"]
  },
  "channels": {
    "discord": {
      "enabled": true,
      "token": "PASTE-YOUR-DISCORD-BOT-TOKEN-HERE",
      "dm": {
        "policy": "pairing"
      },
      "guilds": {
        "PASTE-YOUR-GUILD-ID-HERE": {
          "slug": "huffman-homeschool",
          "requireMention": false,
          "channels": {
            "PASTE-MATT-COMMAND-CHANNEL-ID": {
              "allow": true,
              "systemPrompt": "You are HomeschoolBot. Matt is the admin. Help with lesson planning, grading, and school management."
            },
            "PASTE-JUNIE-LESSONS-CHANNEL-ID": {
              "allow": true,
              "systemPrompt": "You are HomeschoolBot talking to Junie (3rd grade, age 7). Be warm, encouraging, age-appropriate. Help with lessons using Socratic questioning."
            },
            "PASTE-JUNIE-SUBMIT-CHANNEL-ID": {
              "allow": true,
              "systemPrompt": "Junie is submitting completed work. Acknowledge receipt and trigger grading."
            },
            "PASTE-ADDIE-LESSONS-CHANNEL-ID": {
              "allow": true,
              "systemPrompt": "You are HomeschoolBot talking to Addie (5th grade, age 10). Be encouraging and challenge her thinking. Help with lessons using Socratic questioning."
            },
            "PASTE-ADDIE-SUBMIT-CHANNEL-ID": {
              "allow": true,
              "systemPrompt": "Addie is submitting completed work. Acknowledge receipt and trigger grading."
            }
          }
        }
      }
    }
  }
}

Note: The grades, logs, and alerts channels aren't wired yet — those are output channels we'll set up later when we build the HomeschoolOS skills.

4. Test the API: openclaw chat "Hello, what model are you running?"
5. Restart OpenClaw: openclaw restart
6. Check status: openclaw status --all

Tell me the results.
```

### Prompt 3: Auto-Start and SSH

```
Two things:

1. Create a LaunchAgent so OpenClaw starts automatically on boot:
   - First run: which openclaw (to get the correct path)
   - Create ~/Library/LaunchAgents/ai.openclaw.gateway.plist with:
     - Label: ai.openclaw.gateway
     - ProgramArguments: [the path from above, "gateway", "--port", "18789"]
     - RunAtLoad: true
     - KeepAlive: true
     - StandardOutPath: /tmp/openclaw-gateway.log
     - StandardErrorPath: /tmp/openclaw-gateway.error.log
   - Load it with: launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/ai.openclaw.gateway.plist
     (If that fails, try: launchctl load ~/Library/LaunchAgents/ai.openclaw.gateway.plist)
   - Verify: openclaw status --all

2. Generate an SSH key for this Mac Mini so we can clone from GitHub:
   - ssh-keygen -t ed25519 -C "mac-mini-openclaw" -f ~/.ssh/id_ed25519 -N ""
   - Print the public key so I can add it to GitHub: cat ~/.ssh/id_ed25519.pub

Note about the LaunchAgent: After a reboot, Docker Desktop takes 10-30 seconds to start. OpenClaw may fail on first attempt — that's fine, KeepAlive will restart it automatically once Docker is ready.
```

**When Claude Code prints the SSH public key:**
1. Go to **https://github.com/settings/keys** in your browser
2. Click **"New SSH key"**
3. Title: **"Mac Mini"**
4. Paste the public key Claude Code printed
5. Click **"Add SSH key"**

Then tell Claude Code:

### Prompt 4: Clone and Install HomeschoolOS

```
The SSH key is added to GitHub. Now:

1. Clone HomeschoolOS into the workspace:
   git clone git@github.com:Mhuffman1/HomeschoolOS.git ~/openclaw-workspace/homeschool-os
   (If that repo doesn't exist yet, just create the directory: mkdir -p ~/openclaw-workspace/homeschool-os)

2. If the clone worked, install dependencies:
   cd ~/openclaw-workspace/homeschool-os
   npm install
   pip3 install --break-system-packages google-api-python-client google-auth-httplib2 google-auth-oauthlib

3. Verify the workspace is visible:
   ls ~/openclaw-workspace/

Tell me what's there.
```

**If HomeschoolOS isn't on GitHub yet:** You'll AirDrop or USB-copy it from your laptop instead. On your laptop, compress the `MattOS/homeschool-os/` folder, AirDrop to the Mac Mini, then tell Claude Code:

```
I AirDropped homeschool-os.zip to this Mac. Unzip it into the workspace:
cd ~/Downloads && unzip homeschool-os.zip -d ~/openclaw-workspace/
Then run npm install and pip3 install --break-system-packages google-api-python-client google-auth-httplib2 google-auth-oauthlib in ~/openclaw-workspace/homeschool-os/
```

### Step 5.5: 🖐️ Re-Authenticate Google OAuth

The Google Drive and Calendar tokens from your laptop won't work on the Mac Mini. In Terminal (outside Claude Code — press **Ctrl+C** to exit Claude Code, or open a new Terminal tab with **Command+T**):

```
cd ~/openclaw-workspace/homeschool-os
python3 operations/tools/push_to_drive_api.py
```

This will open a browser window asking you to sign into Google. Sign in with **huffman.matthew@gmail.com**, grant permissions. It will save a new `token_push.pickle` file.

Then:
```
python3 operations/tools/push_to_calendar.py
```

Same process — sign in, authorize. Saves `token.pickle`.

### Step 5.6: 🖐️ Quick Chat With the Kids

Before they start using it:
- "This is a homework helper bot. It's a tool, not a person."
- "If it ever says something weird or confusing, just come tell me."

---

## Phase 6: 🖐️ + 🤖 Full Shakedown (30-45 min)

Don't rush this. Every test you skip here is a mystery you'll debug later at the worst possible time.

### Test 1: 🖐️ Cold Reboot

This is the most important test. Everything should come back on its own.

1. **Restart the Mac Mini**: Apple menu → Restart
2. Wait 2 minutes after the desktop appears (Docker needs time to start)
3. Open Discord on your phone or laptop (NOT on the Mac Mini)
4. Go to **#matt-command** and type: "Are you there?"
5. Wait up to 60 seconds. If the bot responds, auto-start works.
6. **If it doesn't respond after 60 seconds**: Open Terminal on the Mac Mini and run `openclaw status --all`. If it says the gateway isn't running, run `openclaw restart` and try Discord again. If THAT works, the issue is the LaunchAgent timing — Docker was still starting. The KeepAlive setting will handle this automatically next time.

### Test 2: 🖐️ Discord in Every Channel

Test each channel the bot should respond in. You're checking that channel IDs are wired correctly and the per-channel system prompts work.

1. Go to **#matt-command**, type: "What channel is this?" — bot should respond
2. Go to **#junie-lessons**, type: "Hi, what's my name?" — bot should say Junie
3. Go to **#addie-lessons**, type: "Hi, what's my name?" — bot should say Addie
4. Go to **#junie-submit**, type: "I finished my math" — bot should acknowledge
5. Go to **#addie-submit**, type: "I finished my reading" — bot should acknowledge
6. Go to **#junie-grades** and type something — bot should NOT respond (it's not wired to this channel yet, that's expected)

If a channel doesn't get a response, the channel ID is wrong in the config. Right-click the channel in Discord → Copy Channel ID → compare to what's in `~/.openclaw/openclaw.json`. Open Claude Code and tell it to fix the ID.

### Test 3: 🤖 File Access and Sandbox

Open Claude Code in Terminal and paste:

```
Run these tests and tell me the results:

1. File access: Tell OpenClaw via Discord (#matt-command) to run "ls /workspace/" — does it see homeschool-os/?
2. Write test: Tell OpenClaw to create /workspace/test-write.txt with "hello from openclaw"
3. Verify on host: cat ~/openclaw-workspace/test-write.txt (should say "hello from openclaw")
4. Sandbox containment: Tell OpenClaw to run "ls /Users/" — it should NOT see your home folder
5. Sandbox containment: Tell OpenClaw to run "cat /etc/hostname" — should show a container ID, not "Matts-Mac-Mini"
6. Cleanup: rm ~/openclaw-workspace/test-write.txt

If any sandbox tests fail, check: openclaw config get sandbox.enabled and openclaw config get sandbox.provider
```

### Test 4: 🤖 Pipeline Tools

In Claude Code:

```
Test these on the host machine (not inside Docker):
1. cd ~/openclaw-workspace/homeschool-os && node -e "console.log('Node works')"
2. python3 -c "print('Python works')"
3. If ./hsync exists, run: ./hsync push --dry-run
Tell me the results.
```

### Test 5: 🖐️ API Spend Limits

Log into both consoles and verify your spending limits are set:

1. **https://console.anthropic.com** → Billing → Usage Limits → should show $50/month
2. **https://platform.openai.com** → Settings → Billing → should show $50/month

### What Success Looks Like

- ✅ Mac Mini reboots and bot comes back online by itself
- ✅ Bot responds correctly in all five configured channels
- ✅ Bot uses the right persona per channel (Junie channels vs. Addie channels vs. Matt)
- ✅ Bot can read AND write files in `/workspace/`
- ✅ Bot CANNOT see files outside the workspace
- ✅ Node.js and Python work on the host machine
- ✅ API spending limits confirmed set on both providers
- ✅ OAuth tokens are authenticated for Google Drive + Calendar

---

## What's Next

Once all tests pass, you're ready for:

1. **Create HomeschoolOS skills for OpenClaw** — turn each `hsync` command into an OpenClaw skill
2. **Generate the shakedown week syllabus** — real lessons for both kids
3. **Run the live test** — you play both kids for a week
4. **Iterate** — fix everything that breaks
5. **Set up web search for corpus updates** — we'll build a skill that fetches from a list of domains you approve, so the agent isn't browsing the open internet

We'll do those together in the next session. The Mac Mini will be ready and waiting.

---

## Good Habits

These aren't setup steps — just things to keep in mind as you use the system:

- **Read community skills before installing them.** `openclaw skill inspect <name> --source clawhub` downloads it without installing. Look for anything that runs shell commands or hits URLs you don't recognize. If you're not sure, ask me.
- **If the bot acts weird** — run `openclaw stop` to kill it, then `openclaw logs` to see what happened. Come back here and we'll figure it out.
- **If you think an API key leaked** — go to console.anthropic.com and platform.openai.com, delete the old keys, create new ones. The spending limits cap your exposure at $50 either way.

---

## Troubleshooting Quick Reference

| Problem | Fix |
|---------|-----|
| "command not found" after installing something | Close Terminal and reopen it. Or run `source ~/.zprofile` |
| Password doesn't show when typing in Terminal | Normal — just type it blind and press Enter |
| OpenClaw won't start | Run `openclaw doctor` to diagnose |
| Discord bot doesn't respond | Check Message Content Intent is ON in Developer Portal |
| "Permission denied" errors | Check Phase 2.5 macOS permissions. Restart Terminal after. |
| Can't connect to Wi-Fi | Make sure you're using the 5GHz network for best results |
| Mac Mini went to sleep | Check Phase 2.3 energy settings |
| Google OAuth fails | Make sure you're signing in with huffman.matthew@gmail.com |
| Node/npm errors | Run `brew reinstall node@22` |
| Python module not found | Run the `pip3 install` command from Phase 5 again |
| Docker not running | Open Docker Desktop from Applications. Wait for whale icon to stop animating. |
| "sandbox error" or "container failed" | Run `docker ps` to check Docker is running. Try `openclaw restart`. |
| Claude Code auth fails | Run `claude` again — it will re-prompt for OAuth |

---

## Files Created During This Setup

| File | Location | What |
|------|----------|------|
| `openclaw.json` | `~/.openclaw/` | OpenClaw configuration (API keys, Discord, settings) |
| `ai.openclaw.gateway.plist` | `~/Library/LaunchAgents/` | Auto-start config for OpenClaw |
| `id_ed25519` / `id_ed25519.pub` | `~/.ssh/` | SSH key for GitHub access |
| `token_push.pickle` | `~/openclaw-workspace/homeschool-os/` | Google Drive OAuth token |
| `token.pickle` | `~/openclaw-workspace/homeschool-os/` | Google Calendar OAuth token |

---

## Sources

- [Claude Code Setup](https://code.claude.com/docs/en/setup)
- [Claude Code on Homebrew](https://formulae.brew.sh/cask/claude-code)
- [Apple: Set up your Mac mini](https://support.apple.com/guide/mac-mini/set-up-your-mac-apd831707cb3/mac)
- [OpenClaw Official Setup Guide](https://getclawdbot.org/docs/setup-guide)
- [OpenClaw Mac Mini Complete Guide](https://aiopenclaw.org/blog/openclaw-mac-mini-complete-guide)
- [Discord Developer Portal](https://discord.com/developers/applications)
- [Discord Channel Integration Guide](https://open-claw.me/channels/discord)
- [Homebrew Installation](https://brew.sh)
