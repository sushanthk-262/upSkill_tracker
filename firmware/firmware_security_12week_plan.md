# Firmware Security Research — 12-Week Execution Plan

Built from current progress: UEFIExtract/UEFITool set up, Dell PowerEdge + Lenovo ThinkPad T14 Gen 1 BIOS already extracted, Phoenix protected-range hashes and FIT tables already identified. This plan starts from there, not from zero.

**Assumption:** ~8–11 hrs/week, Intel IFWI validation role continuing in parallel, 6-month layoff risk window in background.

---

## The Artifact — What You're Actually Building

**Target: "LogoFAIL Detector" — a firmware scanning tool that identifies vulnerable BMP/JPEG DXE image parsers in UEFI firmware images, with a public writeup reproducing the LogoFAIL root cause analysis.**

Why this specific artifact:
- Directly extends the Lenovo T14 work already done
- LogoFAIL is well-documented publicly (Eclypsium/Binarly) — enough reference material to validate your findings against
- Produces three deliverables in one build: a tool (GitHub), a technical writeup (portfolio/LinkedIn), and a reusable detection capability (interview talking point)
- Natural extension path to your ML background — v2 can add embedding-based similarity search over firmware modules

---

## Track 1 — Build (project milestones)

### Weeks 1–2: Foundation — understand the attack class cold
- Finish extracting T14 Gen 1 fully (resolve path-length issue, generate full `.report` + `.dump`)
- Locate the image-parsing DXE driver in the dump (search for BMP/JPEG/Logo module GUIDs)
- Read both Eclypsium LogoFAIL posts + the original Binarly disclosure in full; take structured notes on the exact root cause (buffer overflow in logo image dimension parsing — confirm this against what you find in the actual Lenovo binary)
- Pull T14 Gen 2 (Tiger Lake, LogoFAIL-affected family) and T14 Gen 3 (Alder Lake) — extract both
- **Deliverable:** a private notes doc mapping the theoretical LogoFAIL root cause to the actual module you've located in real firmware

### Weeks 3–4: Diffing — find what changed
- Download 2–3 historical BIOS versions for T14 Gen 2 (pre- and post- the LogoFAIL disclosure date, Dec 2023)
- Diff the DXE volume module-by-module between versions using UEFITool/UEFIExtract output
- Identify exactly what Lenovo patched in the vulnerable parser
- **Deliverable:** a before/after technical comparison — this is your core finding, write it up even in rough form now

### Weeks 5–6: Tooling — automate the detection
- Write a Python script (using `uefi-firmware-parser` or your own GUID-based scanning) that:
  - Takes a firmware image as input
  - Extracts and identifies image-parsing DXE modules
  - Flags modules matching the pre-patch (vulnerable) signature vs post-patch
  - Outputs a simple report: vulnerable / patched / unknown
- Start simple — a working CLI tool beats a polished one that's late
- **Deliverable:** working CLI tool, tested against your T14 Gen 1/2/3 samples + the Dell PowerEdge samples

### Weeks 7–8: Validate and expand coverage
- Test the tool against 3–5 more public firmware images from different vendors (HP, additional Dell, additional Lenovo models) to check false positive/negative rate
- Add FwHunt rule format compatibility if feasible (Binarly's open format) — this signals you understand the existing ecosystem rather than reinventing it
- **Deliverable:** tool tested across multiple vendors, README with methodology, clear usage instructions

### Weeks 9–10: Polish + publish
- Clean up the GitHub repo: README with architecture explanation, example output, clear "why this matters" section, decision document (why you built it this way, what it doesn't do, what you'd add with more time)
- Write the public technical blog post / LinkedIn long-form post: "Reproducing LogoFAIL: What I Learned Scanning Real Firmware for a Known UEFI Vulnerability"
- Publish both together
- **Deliverable:** live GitHub repo + published writeup

### Weeks 11–12: Buffer + stretch goal
- Buffer week for whatever slipped
- Stretch goal if ahead of schedule: add the ML angle — train a simple classifier or embedding-similarity search to flag "firmware modules structurally similar to known-vulnerable LogoFAIL parsers" across a larger firmware corpus. Even a rough version is a strong differentiator given your AI background.

---

## Track 2 — Job Switch Preparation (parallel, every week)

### Company targets — firmware security specific

**Tier 1 — Dedicated firmware security companies (best fit, smallest pool):**
| Company | Why it fits | Notes |
|---|---|---|
| Eclypsium | Builds exactly what you're building — firmware vulnerability detection platforms | Remote-friendly, US-based, actively publishes research — engage with their blog publicly before applying |
| Binarly | Same space, smaller, research-heavy culture | Founder (Alex Matrosov) is active on X/LinkedIn — genuine engagement with his public work is a real door-opener |
| Red Balloon Security | Embedded/firmware security research, historically strong on silicon-level attacks | US-based, harder visa/remote path from India but worth tracking |

**Tier 2 — Firmware vendors (BIOS development + security teams):**
| Company | Why it fits |
|---|---|
| AMI (American Megatrends) | Writes the BIOS itself — security team roles exist, India presence |
| Insyde Software | Same category, Taiwan-based but has distributed teams |
| Phoenix Technologies | The exact vendor whose BIOS you've been analyzing (Lenovo T14 Gen 1) |

**Tier 3 — Your existing employer and adjacent hardware companies (leverage your Intel position):**
| Company | Why it fits |
|---|---|
| Intel (internal transfer) | Security-focused BIOS/firmware teams exist internally — your current IFWI role is the on-ramp. Ask your colleague contact for team names to target. |
| AMD | Equivalent firmware security surface, PSP (Platform Security Processor) work is a close TPM-adjacent analog |
| Qualcomm | On-device/embedded security, your hardware validation background transfers directly |

**Tier 4 — Security companies with firmware-adjacent teams:**
| Company | Why it fits |
|---|---|
| CrowdStrike | Has firmware/BIOS threat detection capabilities in their platform |
| Palo Alto Networks | Similar — endpoint + firmware-level threat research |

### What these companies actually screen for

Based on how firmware security hiring typically works (this is informed reasoning, not verified job postings — confirm specifics when you actually see a JD):
- **Practical skills test over LeetCode-style DSA** — expect a take-home or live exercise involving an actual firmware image or binary to analyze
- **Reverse engineering fundamentals** — comfort with Ghidra/IDA, reading disassembly, understanding calling conventions
- **Systems-level C/C++** — UEFI/firmware code is C, not Python; your tooling can be Python but you need to read C comfortably
- **Protocol/spec literacy** — ability to reference the UEFI spec, TPM spec directly when discussing a vulnerability, not just vibes-level understanding
- **A portfolio that proves hands-on work** — this is why Track 1's artifact matters more than certificates here; firmware security hiring is craft-signal-driven, not credential-driven

### Resume / portfolio repositioning

Current title: "System Integration & Validation Engineer"
Target framing: **"Firmware Security Researcher (transitioning from Validation Engineering)"**

Resume additions once Track 1 artifact exists:
- Project line: "Built and published a firmware vulnerability detection tool reproducing the LogoFAIL (CVE-class) UEFI image-parsing exploit across multiple vendor BIOS images — [GitHub link]"
- Reframe existing Intel validation bullets to emphasize security-adjacent testing (TPM, secure boot TCDs) rather than generic automation language
- Add a dedicated "Security Research" section separate from "Experience" — this signals the pivot clearly to a recruiter scanning fast

### Networking / visibility — niche-specific

- **Follow and genuinely engage with:** Alex Matrosov (Binarly), Eclypsium's research team, UEFI Forum discussions
- **Communities:** r/ReverseEngineering, r/AskNetsec (occasional firmware threads), OSFC (Open Source Firmware Conference) talks on YouTube — not live events necessarily, but the talks are free and excellent
- **Conferences to reference/cite (don't need to attend):** Black Hat/DEF CON firmware security talks, OffensiveCon, hardwear.io — citing specific talks in your writeup signals you're plugged into the field
- **LinkedIn posting cadence:** once Track 1 produces real findings (Week 3–4 diffing results), start posting — "what I found" posts, not "what I'm learning" posts

---

## Weekly Time Allocation (8–11 hrs/week)

```
Build (Track 1):        6–7 hrs/week
Job prep (Track 2):      1–2 hrs/week  (increases in Weeks 9–12)
Reading/reference:       1–2 hrs/week  (Eclypsium/Binarly/spec reading, ongoing)
```

Track 2 stays light early — the artifact is the actual qualification. Don't start heavy applying until Week 9+ when you have something real to show.

---

## Hard Constraints Respected

- All firmware images used are publicly downloadable vendor releases (Dell, Lenovo support sites)
- All vulnerability reproduction is based on publicly disclosed CVEs with existing public writeups — no novel 0-day hunting, no risk of stepping on responsible disclosure
- Nothing from Intel-internal TCDs, test methodology, or pre-release firmware appears in any public artifact
- Timeline respects the 6-month layoff risk — by Week 12 you have a live portfolio piece and have started Tier 1–3 outreach, leaving 1–2 months of runway for actual interviews before any potential layoff timeline closes in

---

## What Changes If Layoff Risk Escalates Before Week 12

If the rumor firms up into something concrete before Week 12:
- Compress Weeks 9–12 into Weeks 7–10 — ship the tool slightly rougher, writeup still goes out
- Pull Track 2 applying forward — start Tier 3 (Intel internal transfer conversation, AMD/Qualcomm) by Week 6 instead of Week 9, since those don't require the polished artifact as strictly
- Tier 1 (Eclypsium/Binarly) still needs the real artifact — don't rush applying there with nothing to show
