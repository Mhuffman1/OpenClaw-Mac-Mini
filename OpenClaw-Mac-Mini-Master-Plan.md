# OpenClaw Mac Mini Master Plan

**Date:** 2026-02-20
**Hardware:** Mac Mini M4 base (16GB RAM, 256GB SSD, ~$599)
**Software:** OpenClaw (open-source AI agent framework)
**Purpose:** Universal runtime for Matt's AI ecosystem, starting with HomeschoolOS

---

## The Big Idea

The Mac Mini becomes a 24/7 always-on AI agent that Matt and his kids interact with through Discord (and eventually other messaging platforms). It runs OpenClaw, which is a persistent daemon that receives messages, parses intent, retrieves context, selects tools, executes actions, and delivers responses. It has persistent memory and a modular skill system that uses SKILL.md files — **almost identical to the convention Matt already uses across every project.**

This isn't just for HomeschoolOS. It's the runtime for the entire ecosystem: CMO-in-a-box, Delivrd engagement automation, recursive self-improvement loops, file hygiene, and anything else Matt builds. HomeschoolOS is priority #1 because it's the most immediately useful.

---

## Phase 0: Hardware Setup (Day 1)

### Unboxing & Initial macOS Setup

1. Plug in Mac Mini, connect monitor via HDMI, pair Bluetooth keyboard + mouse
2. Walk through macOS setup (Apple ID, Wi-Fi, basic preferences)
3. **Order an HDMI dummy plug** ($8-12 on Amazon) — this tricks macOS into thinking a display is connected when running headless. Critical for 24/7 operation. Until it arrives, keep the monitor plugged in.
4. Enable **FileVault** (System Settings → Privacy & Security → FileVault) — encrypts the drive
5. Enable the **firewall** (System Settings → Network → Firewall)

### Install Prerequisites

```bash
# Install Homebrew (macOS package manager)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Node.js (OpenClaw needs it)
brew install node

# Install Git
brew install git

# Install Python 3 (for HomeschoolOS tools like hsync)
brew install python3

# Install pnpm (if you want the hackable install)
brew install pnpm
```

### macOS Permissions (Required)

Go to **System Settings → Privacy & Security** and add Terminal.app to:
- Full Disk Access
- Accessibility
- Screen Recording

**Restart Terminal after toggling permissions.** This is the #1 troubleshooting issue.

### Power & Sleep Settings

System Settings → Energy Saver:
- Prevent automatic sleeping when display is off: **ON**
- Wake for network access: **ON**
- Start up automatically after a power failure: **ON**

The M4 idles at 3-4 watts. Running it 24/7 costs ~$15/year in electricity.

---

## Phase 1: OpenClaw Installation & Configuration

### Install OpenClaw

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

Then run the onboarding wizard:

```bash
openclaw onboard
```

### Configure AI Models

During onboarding, OpenClaw asks for API keys. You need to set up **separate API accounts** (not your chat subscriptions):

| Provider | Where to Get Key | Cost Model | What to Use It For |
|----------|-----------------|------------|-------------------|
| **Anthropic** | [console.anthropic.com](https://console.anthropic.com) | Pay-per-token | Primary model provider. Haiku for routine tasks, Sonnet for lesson generation, Opus for complex planning |
| **OpenAI** | [platform.openai.com](https://platform.openai.com) | Pay-per-token | Secondary/experimental. Test GPT-4o vs Claude for specific tasks |

**CRITICAL:** Your $100/month Claude plan and $200/month OpenAI plan are **chat subscription plans** for the web apps. They do NOT include API access. API billing is completely separate, pay-as-you-go, per token. You need to add a credit card to each API console.

**Estimated monthly API costs for HomeschoolOS:**
- Haiku for routine tasks (pull, grade, file ops): ~$2-5/month
- Sonnet for lesson generation (10-20 lessons/week): ~$10-20/month
- Opus for weekly planning + complex reasoning: ~$5-15/month
- **Total estimate: $15-40/month** (varies with usage)

### Model Routing Strategy

| Task | Recommended Model | Why |
|------|------------------|-----|
| Weekly planning | Claude Opus or Sonnet | Complex reasoning, multi-source synthesis |
| Lesson generation (research + write) | Claude Sonnet | Good balance of quality and cost |
| DOCX conversion, file ops | Claude Haiku | Routine, low-complexity |
| Grading (objective) | Claude Haiku | Pattern matching, fast |
| Grading (rubric flagging) | Claude Sonnet | Needs judgment |
| Captain's log writing | Claude Haiku | Transcription-like |
| Socratic conversations with kids | Claude Sonnet | Needs pedagogical awareness |
| CMO strategy generation | Claude Opus | Highest quality for strategy |
| Recursive self-improvement | Claude Sonnet | Good enough for review loops |
| File hygiene / organization | Claude Haiku | Routine classification |

OpenClaw supports model routing per-skill, so each skill can specify which model to use.

### Security Hardening

```bash
# After install, apply minimum security
openclaw config set server.bind 127.0.0.1  # Loopback only
openclaw config set server.auth.token <generate-a-strong-token>
openclaw config set dm.policy pairing      # DMs require pairing
```

---

## Phase 2: Discord Setup

### Create the Family Discord Server

1. Download Discord on your phone + Mac Mini
2. Create a new server: **"Huffman Homeschool"** (or whatever you want to call it)
3. Create an account for each kid (Junie, Addie) — Discord requires age 13+, so these would be under your supervision

### Discord Bot Setup

1. Go to [Discord Developer Portal](https://discord.com/developers/applications)
2. Create New Application → name it "HomeschoolBot" (or "Claw" or whatever)
3. Go to Bot tab → Create Bot → Copy the Bot Token
4. Under Privileged Gateway Intents, enable:
   - Message Content Intent ✓
   - Server Members Intent ✓
5. Go to OAuth2 → URL Generator → Select `bot` scope → Select permissions: Send Messages, Read Messages, Manage Threads, Embed Links, Attach Files, Read Message History
6. Use the generated URL to invite the bot to your server

### Server Channel Structure

```
Huffman Homeschool/
├── #general              — Family chat, announcements
├── #matt-command          — Matt's control channel (plan week, swap lesson, etc.)
│
├── 📚 Junie/
│   ├── #junie-lessons     — Lesson delivery + Socratic conversations
│   ├── #junie-submit      — "I finished my math!" → triggers pull + grade
│   └── #junie-grades      — Grade reports, alerts
│
├── 📚 Addie/
│   ├── #addie-lessons     — Lesson delivery + Socratic conversations
│   ├── #addie-submit      — Submission triggers
│   └── #addie-grades      — Grade reports, alerts
│
├── 🔧 System/
│   ├── #logs              — System logs, pipeline status
│   └── #alerts            — Grading alerts, errors
```

Each kid has their own channels. Matt has visibility to everything. Kids can't see each other's grades. Threads within lesson channels enable Socratic back-and-forth on specific topics.

### Configure OpenClaw Discord Integration

Add to `~/.openclaw/openclaw.json`:

```json
{
  "channels": {
    "discord": {
      "token": "YOUR_DISCORD_BOT_TOKEN",
      "guilds": {
        "huffman-homeschool": {
          "channels": {
            "matt-command": { "allow": true, "skills": ["all"] },
            "junie-lessons": { "allow": true, "skills": ["lesson-delivery", "socratic"] },
            "junie-submit": { "allow": true, "skills": ["submission-handler"] },
            "addie-lessons": { "allow": true, "skills": ["lesson-delivery", "socratic"] },
            "addie-submit": { "allow": true, "skills": ["submission-handler"] }
          }
        }
      }
    }
  }
}
```

---

## Phase 3: HomeschoolOS Integration

### The Key Insight

Matt's HomeschoolOS is already built. The pipeline works: syllabi → weekly draft → research-informed generation → DOCX conversion → Drive push → Calendar events → pull → grade. All the tools exist (`hsync`, `generate_docx.js`, Drive API scripts, Calendar API scripts, grade.py`). What's missing is the **always-on interface** — the ability to trigger all of this from a message instead of a terminal command.

OpenClaw IS that interface.

### Copy HomeschoolOS to Mac Mini

The entire `MattOS/homeschool-os/` folder needs to live on the Mac Mini. Options:

1. **Git clone** from the HomeschoolOS repo (if pushed to GitHub)
2. **Direct copy** via AirDrop, USB drive, or rsync over local network
3. **Google Drive sync** (the Drive-Mirror folder already exists)

The OAuth tokens (`token_push.pickle`, `token.pickle`) need to be re-authenticated on the Mac Mini since they're tied to the machine. This is a one-time setup:

```bash
# On Mac Mini, run the push script once to trigger OAuth flow
cd ~/homeschool-os
python3 operations/tools/push_to_drive_api.py  # Will open browser for Google auth
python3 operations/tools/push_to_calendar.py   # Same for Calendar
```

### HomeschoolOS Skills for OpenClaw

Each `hsync` command becomes an OpenClaw skill:

| OpenClaw Skill | Maps To | Trigger |
|---------------|---------|---------|
| `plan-week` | Read syllabi + logs → produce weekly draft | Matt messages "plan the week" |
| `generate-lesson` | Three-query research → teacher + student markdown | After Matt approves draft items |
| `convert-push` | DOCX conversion → Drive + Calendar | Automatic after generation |
| `pull-submissions` | `hsync pull` | "Pull work" or automatic on schedule |
| `grade-work` | `hsync grade` | "Grade the work" or automatic after pull |
| `captain-log` | Write to captain's log | Matt messages about how the day went |
| `swap-lesson` | Delete old event → generate new → push | "Swap Thursday's lesson for X" |
| `add-slides` | Create PPTX → upload → update calendar | "Make slides for Thursday's math" |
| `submission-handler` | Kid says "done with math" → pull + grade | Message in #submit channel |
| `socratic` | Conversational tutoring on lesson topic | Kid asks questions in #lessons channel |
| `weekly-closeout` | Extract action items → update planning | "Close out the week" |

### Scheduled Tasks (Cron Jobs via OpenClaw)

| Schedule | Task | What It Does |
|----------|------|-------------|
| Daily 3:00 PM | Auto-pull | `hsync pull` — grab any completed work from Drive |
| Daily 3:30 PM | Auto-grade | `hsync grade` — grade completed assignments |
| Daily 4:00 PM | Grade report | Post grade summary to #alerts if any new grades |
| Sunday 7:00 PM | Week prep reminder | "Hey Matt, ready to plan next week?" |
| Weekly | File hygiene | Check for orphan files, update maps |

---

## Phase 4: Live Shakedown Week

### What We're Testing

Both kids (Junie 3rd grade, Addie 5th grade), full pipeline, real content, Matt plays both kids.

### Pre-Shakedown Checklist

- [ ] Mac Mini set up and running
- [ ] OpenClaw installed and configured
- [ ] Discord server created with channel structure
- [ ] HomeschoolOS files copied to Mac Mini
- [ ] OAuth tokens re-authenticated on Mac Mini
- [ ] Node.js dependencies installed (`npm install` in homeschool-os/)
- [ ] Python dependencies installed (google-api-python-client, etc.)
- [ ] All HomeschoolOS skills registered in OpenClaw
- [ ] Syllabi updated: `external` blocks changed to `generate` for testing
- [ ] HDMI dummy plug installed (or monitor still connected)

### Shakedown Protocol

**Day 0 (Setup Day):**
1. Message "plan the week" in #matt-command
2. OpenClaw reads syllabi for both kids → produces weekly draft
3. Matt reviews draft in Discord, approves it
4. OpenClaw generates lessons for the full week (both kids, all subjects)
5. Confirms: all DOCX files in Drive, all calendar events created
6. Verify: open each Google Calendar, click each lesson link, confirm files open

**Days 1-5 (Simulated School Days):**
Each day, Matt:
1. Checks the calendar — what's scheduled for today
2. Opens the student worksheet in Google Docs (as if he's the kid)
3. Fills in answers (deliberately mixing correct + incorrect for grading tests)
4. Messages in #junie-submit or #addie-submit: "Done with math!"
5. OpenClaw pulls → grades → posts results to #grades channel
6. Matt writes captain's log: "Junie finished math, got 80%. Struggled with fractions."
7. At end of day: messages "close out the day" — OpenClaw extracts action items

**Day 5 (Friday) — Test Edge Cases:**
- Swap a lesson mid-week ("Replace tomorrow's science with a lesson on volcanoes")
- Add supplementary slides to an existing lesson
- Submit work partially (test incomplete-skip logic)
- Submit work late (test idempotency)
- Ask Socratic questions in #junie-lessons ("What is a fraction? Can you explain it like I'm 7?")

**Post-Shakedown:**
- Review gradebook — all grades logged correctly?
- Review captain's logs — all entries captured?
- Review action items — nothing dropped?
- Review calendar — all events correct, swaps worked?
- Review Drive — all files in correct folders?
- Note every friction point, bug, and surprise

### Syllabus for Shakedown Week

Generate content for the current week using existing syllabi. Here's what to cover based on what's in the curriculum folders:

**Junie (3rd Grade):**
- Math: Next unit in sequence from syllabus-math.json
- ELA: Next unit from syllabus-ela.json (skip `external` Bridgeway blocks)
- Science: Next unit from syllabus-science.json
- Social Studies: Next unit from syllabus-social-studies.json

**Addie (5th Grade):**
- Math: Next unit in sequence from syllabus-math.json
- ELA: Next unit from syllabus-ela.json
- Science: Next unit from syllabus-science.json
- Social Studies: Next unit from syllabus-social-studies.json

Each subject gets one LESSON or ACTIVITY per day based on syllabus sequencing. That's ~4 items per kid per day × 5 days = ~40 lessons total for the week.

---

## Phase 5: Beyond HomeschoolOS

### Cursor CMO → OpenClaw

The Cursor CMO v05.1 has **32 executable commands** (discovery, competitive positioning, ICP/personas, brand messaging, channel strategy, campaign plans, forecasting, research, compile, recursion, etc.) that currently run inside Cursor. These can become OpenClaw skills:

| CMO Command | OpenClaw Skill Potential |
|------------|------------------------|
| `/scaffold` | Create new company engagement via messaging |
| `/ingest` | Feed evidence (transcripts, docs) via messaging |
| `/run-pipeline` | Run full strategy generation |
| `/compile` | Cross-reference and validate artifacts |
| `/phase-b3-recursion` | Recursive self-improvement pass |
| `/research` | Web research → knowledge files |
| `/switch-run` | Switch active company (portfolio mode) |

**The "CMO in a box" vision:** You message the bot "Run competitive analysis for Found Health." It loads the engagement, pulls context, runs the pipeline, and delivers strategy documents — all through Discord or WhatsApp. No need to open Cursor.

### Delivrd → OpenClaw

The Company Compiler skills (`/ingest`, `/atomize`, `/compile`, etc.) become OpenClaw skills. The engagement workflow (decompose → research → score → build → deploy → measure) can be triggered conversationally. Meeting transcripts can be fed via messaging. Atoms, builder notes, and execution plans are generated and stored automatically.

### Recursive Self-Improvement

Matt's core pain point: "I make something, I say review it, it finds 5 bugs, I fix them, it finds 6 more." This becomes an automated OpenClaw skill:

```
trigger: "run improvement loop on [project]"
steps:
  1. Build/compile the project
  2. Run review (lint, test, spec check)
  3. Log all issues found
  4. Fix issues automatically
  5. Re-review
  6. Repeat until clean bill of health OR max iterations
  7. Post summary to #system channel
```

This can run as a cron job or on-demand.

### Auto-Hygiene

OpenClaw scheduled skill that runs weekly:
- Check for orphan files at root level
- Classify using HOME-ORGANIZATION-OS.md rules
- Route to correct locations or flag for Matt
- Update 00-MAP.md files where structure changed
- Generate/update READMEs
- Run LIBRARY-REGISTRY.md audit

### System Visualizer

Matt mentioned a visualizer plugin. OpenClaw can generate visual maps of project structures using Mermaid diagrams, ASCII trees, or interactive HTML. Schedule it to run after any major file changes.

---

## Ecosystem Survey — What Matt Has

### Active Projects (OpenClaw Priority)

| Project | Files | Status | OpenClaw Fit |
|---------|-------|--------|-------------|
| **HomeschoolOS** | ~45 folders/files, full pipeline | Active, go-live Aug 2026 | **Priority #1** — becomes the primary OpenClaw workload |
| **Cursor CMO v05.1** | 32 commands, portfolio mode | Active | **Priority #2** — "CMO in a box" via messaging |
| **Delivrd** | 6-phase engagement, 45+ items | Active engagement | **Priority #2** — Company Compiler via messaging |
| **Gardener (Karesansui)** | 14 items, spec v1.3 | Active, Phase 1 ready | **Future** — self-validating KB maps perfectly to OpenClaw's memory + skill system |

### Complete Systems (Ready to Port)

| Project | Files | What | OpenClaw Fit |
|---------|-------|------|-------------|
| **CMO-OS** | 175 files | Virtual CMO knowledge base | Corpora for CMO skills. Could power the CMO-in-a-box |
| **pile** | 129 CAM files | StoneSight knowledge hub | Knowledge store for content-engine skills |
| **content-engine** | 10 items, 4 skills | Content pipeline (brief→draft→critique→distribute) | Direct port to OpenClaw skills |
| **TOOLFORGE** | 7 items | Role-OS builder framework | Meta-skill: build new role systems via messaging |
| **PromptMaster** | 9 items | Prompt engineering system | Powers prompt generation skills |
| **Company Compiler** | 11 items, v3 Kit | Company atomization system | Already proven on Delivrd |

### Shared Infrastructure (Powers Everything)

| System | Purpose | OpenClaw Role |
|--------|---------|--------------|
| **SKILLS-LIBRARY** | Canonical SKILL.md files | **Direct mapping** to OpenClaw skill format |
| **PROMPTS-LIBRARY** | Reusable prompts | Loaded as context for OpenClaw skills |
| **GLOBAL-KNOWLEDGE** | Cross-project knowledge | OpenClaw memory/context store |
| **LIBRARY-INBOX** | Triage zone | Auto-triage via OpenClaw skill |
| **operations/** | Shared tooling scripts | OpenClaw skill wrappers |
| **CODEX-AUTOMATION-BRIDGE** | Automation lanes + agent protocol | OpenClaw replaces Codex for some lanes |

### The Critical Alignment

**Matt's SKILL.md convention ≈ OpenClaw's SKILL.md format.**

OpenClaw skills are folders containing a SKILL.md with YAML frontmatter and natural-language instructions. Matt's SKILLS-LIBRARY already uses this exact pattern. The skills in CMO-OS, Delivrd, content-engine, and HomeschoolOS all follow this convention. Porting them to OpenClaw requires adding YAML frontmatter (metadata, environment requirements, trigger keywords) but the core instruction content stays the same.

This is not a rebuild. It's a migration with a thin adapter layer.

---

## Execution Order

### Week 1: Foundation
- [ ] Set up Mac Mini (hardware, macOS, prerequisites)
- [ ] Install OpenClaw, complete onboarding
- [ ] Set up Anthropic API account + add billing
- [ ] Set up OpenAI API account + add billing (for experimentation)
- [ ] Create Discord server + bot + channel structure
- [ ] Configure OpenClaw ↔ Discord integration
- [ ] Copy HomeschoolOS to Mac Mini
- [ ] Re-authenticate OAuth tokens on Mac Mini
- [ ] Install Node.js + Python dependencies
- [ ] Create first HomeschoolOS OpenClaw skill (plan-week)
- [ ] Test: message "plan the week" on Discord → get weekly draft back

### Week 2: HomeschoolOS Full Pipeline
- [ ] Create remaining HomeschoolOS OpenClaw skills
- [ ] Set up cron jobs (auto-pull, auto-grade, weekly reminder)
- [ ] Update syllabi (remove `external` blocks for testing)
- [ ] Run live shakedown week (both kids, full pipeline)
- [ ] Document every friction point and bug
- [ ] Fix issues found during shakedown

### Week 3: Polish & Expand
- [ ] Address all shakedown issues
- [ ] Add Socratic conversation skill
- [ ] Add lesson swap skill
- [ ] Add supplementary materials skill
- [ ] Test additional messaging platforms (WhatsApp, iMessage)
- [ ] Begin CMO skill migration (start with /scaffold and /ingest)

### Week 4+: Ecosystem Wiring
- [ ] Port Cursor CMO commands to OpenClaw skills
- [ ] Port Delivrd/Company Compiler skills
- [ ] Set up recursive self-improvement skill
- [ ] Set up auto-hygiene scheduled skill
- [ ] Set up system visualizer
- [ ] Test cross-project interactions

---

## Decisions Made (2026-02-20)

### Storage
Start lean. Only active project files on the Mac Mini. Drive-Mirror and archives stay on the laptop. Revisit if/when 256GB gets tight.

### Backup
Git-based via GitHub private repos. All project files are already git-friendly. System-level Time Machine backup deferred — get the system running first.

### Physical Setup
Mac Mini lives in the playroom (which is becoming the homeschool room). Connected to the TV via HDMI at all times. Bluetooth keyboard for direct interaction. Portable — can unplug and move to Matt's desk for heavy config work. Screen Sharing (built-in macOS) is an easy add for remote access from the laptop, but not critical since Matt has physical access.

### Model & Subscription Strategy
- **Keep the $200/month OpenAI plan** — experiment with subscription-based access through OpenClaw (gray area, not locked down by OpenAI yet). If it works, the $200 covers unlimited o1/GPT-4o for all workloads.
- **Anthropic: API keys only** — Anthropic banned third-party OAuth token use (Feb 19, 2026). Cannot use the $100 Claude Pro subscription with OpenClaw. Must use API keys from console.anthropic.com.
- **Baseline: API keys for both providers** — safe path that works regardless of subscription policy changes.
- **Cost optimization:** Prompt caching (90% savings) + batch API (50% off) dramatically reduce per-token costs.

### Curriculum Generation Strategy
**Generate the entire school year in one batch**, not week by week. This is a big one-time API spend (~$50-100) but means:
- Matt can review the full curriculum for coherence before school starts
- Daily/weekly operation becomes pure delivery + grading (Haiku-level, pennies)
- The expensive creative work happens once; the runtime is cheap
- For the shakedown test: generate just one week to validate the pipeline

### Laptop ↔ Mac Mini Workflow
- **Laptop** = daily driver, where Matt builds, writes, thinks, talks to Claude (Cowork)
- **Mac Mini** = always-on agent, executes OpenClaw, responds to messaging, runs cron jobs
- **Sync method:** Git (push from laptop → Mac Mini pulls) or shared folder (Google Drive / local network). Develop on laptop, deploy to Mac Mini.
- **No rush to migrate everything** — keep working on the laptop, stand up OpenClaw on the Mac Mini in parallel, migrate project by project as skills get wired up.

### Multi-Tenant Consulting Vision
OpenClaw becomes a consulting tool. When a new client calls:
- "Hey Claw, scaffold a new Company Compiler engagement for Acme Corp"
- OpenClaw creates the 6-phase folder structure, runs overnight decomposition using the framework
- Skills proven in one engagement get promoted to shared library via the existing promotion pipeline
- Each client is a tenant (like Cursor CMO's portfolio mode) with full isolation
- Millennium Panther / Cursor CMO becomes the "CMO in a box" product — usable for Matt's own companies AND client engagements

## Remaining Open Questions

1. **Discord age requirements:** Discord officially requires users to be 13+. Junie is 7 and Addie is 10. The accounts would technically be under your supervision. Consider whether this matters for your use case, or whether an alternative platform (iMessage, which has no age restriction) is better for the kids while you use Discord for management.

2. **OpenClaw version pinning:** OpenClaw is moving fast (multiple releases per month). Consider pinning to a specific version for stability rather than auto-updating.

3. **Skill security:** ClawHub had 341 malicious skills found in an audit. Only install skills you write yourself or from trusted sources. Don't auto-install community skills without reviewing them.

4. **OpenAI subscription routing:** Test whether ChatGPT Pro ($200) sign-in works with OpenClaw. If yes, huge cost savings. If no (or if OpenAI locks it down), fall back to API keys. Don't depend on it — treat it as a bonus.

---

## Sources

- [OpenClaw Official Site](https://openclaw.ai/)
- [OpenClaw Setup Guide](https://getclawdbot.org/docs/setup-guide)
- [OpenClaw Mac Mini Complete Guide](https://aiopenclaw.org/blog/openclaw-mac-mini-complete-guide)
- [Mac Mini M4 Setup Guide](https://getclawdbot.org/docs/mac-mini-m4-setup)
- [OpenClaw Skills Documentation](https://docs.openclaw.ai/tools/skills)
- [Discord SKILL.md](https://github.com/openclaw/skills/blob/main/skills/steipete/discord/SKILL.md)
- [Custom Skill Creation Guide](https://zenvanriel.nl/ai-engineer-blog/openclaw-custom-skill-creation-guide/)
- [SitePoint Mac Mini Setup](https://www.sitepoint.com/how-to-set-up-openclaw-on-a-mac-mini/)
- [OpenClaw Security Concerns (Northeastern)](https://news.northeastern.edu/2026/02/10/open-claw-ai-assistant/)
- [DigitalOcean Guide](https://www.digitalocean.com/resources/articles/what-is-openclaw)
