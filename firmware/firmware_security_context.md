# Context File — Sushanth's Firmware Security Research Track

This file is prepared for a local LLM (Ollama) to use as background context for extensive research. It summarizes a career transition, the current technical direction, and active hands-on work. No prior conversation history is assumed — treat this as the complete briefing.

---

## 1. Who This Is For

Sushanth Kulkarni — System Integration & Validation Engineer at Intel, Bengaluru, India.

- IMTech (5-year integrated) in Computer Science, University of Hyderabad, GPA 8.87/10
- Current team: IFWI (Firmware Integration and Validation) — automating TCDs (Test Case Definitions), working on BIOS/IFWI validation, leaning toward security-specific validation (TPM, secure boot, boot flow integrity)
- Prior Intel role: SDE Intern, dGPU Performance Engineering — MLPerf + OpenVINO automation
- Technical background: Python, Java, C++, PyTorch, federated learning (BERT-based log anomaly detection, F1 > 0.90), FastAPI, React, some Solidity/Web3
- Has completed Andrej Karpathy's "Let's Build GPT from Scratch" — understands transformers, self-attention, multi-head attention, residual connections, layer norm at implementation level

## 2. The Career Direction Journey (for context on why this path was chosen)

Sushanth originally pursued a 26-week plan to transition into Applied AI/LLM Engineering (RAG pipelines, agents, LangChain). After executing partially and stalling for ~2 months, he reassessed:

- Applied AI engineering felt "surface-level — just calling APIs, not real engineering"
- Considered alternatives: compilers, OS/systems, ML Systems engineering, an MTech via GATE, IoT
- Determined the actual need was **structured learning** (cohort-based, not self-directed) — this is a stated learning preference, not just for this domain
- A senior Intel colleague (memory side, server CPUs) advised: being on an Intel BIOS-adjacent team is a rare, valuable platform. Recommended going deep on a specific BIOS security niche rather than switching companies/domains entirely.

**Decision made: Firmware Security Research** (not firmware development). Specifically:
- Builds directly on his current IFWI/security validation TCD work at Intel
- Faster ramp than switching to an unrelated domain (compilers, pure OS) because he already has platform access and context
- Strong AI-irreplaceability — LLMs cannot yet reliably reason about hardware trust boundaries, boot flow attack surfaces, or firmware-level exploitation
- Small, high-value talent pool globally; underserved niche
- Natural intersection with his existing AI/ML background (ML-based firmware anomaly detection is a real commercial product category — see Eclypsium, Binarly)

**Explicitly chosen over:**
- Firmware/BIOS *development* (Path A) — longer ramp, requires internal team transfer, less aligned with current role
- Compilers — cold start, no current leverage, 2-3 year ramp
- Web3 — market reality in 2026 doesn't support it as primary career path (covered in prior research)
- MTech/GATE — too slow given a rumored ~6 month layoff risk window at Intel; also would mean 2-3 years out of industry

## 3. Legal / Ethical Boundaries (important — respect these in all research)

```
LEGAL, in scope:
- Analyzing publicly released BIOS/firmware update images (vendor-published)
- Reproducing published CVEs in a lab environment (QEMU/OVMF or spare hardware)
- Reverse engineering for security research purposes
- Publishing original findings based on public firmware images
- Using open-source TPM implementations, open-source firmware parsing tools

OFF LIMITS — do not research or help produce:
- Anything derived from Intel-internal, NDA-covered, or pre-release firmware/test data
- Internal Intel TCD methodology, internal vulnerability disclosures, or internal tools
- Any content that would violate Sushanth's Intel NDA or IP obligations
```

The working rule: if it was learned via public CVE disclosures, public vendor firmware, or public security research (Eclypsium/Binarly blogs, academic papers), it's fair game to analyze and discuss. If it was learned through Intel internal access, it stays out of any research output, writeup, or public artifact.

## 4. Current Hands-On Work (active, in progress)

Sushanth is actively extracting and analyzing real UEFI firmware images using public tools, on his own machine, using publicly downloaded vendor BIOS files.

**Hardware/firmware samples in use:**
- Dell PowerEdge R340/R240/T340/T140 server BIOS (various versions, Intel Xeon E-2100/2200 series) — used for initial tool familiarization
- Lenovo ThinkPad T14 Gen 1 (Types 20UD, 20UE) BIOS — **Phoenix Technologies BIOS**, not AMI/Insyde. Intel platform (10th gen Comet Lake class, based on bundled microcode patches: 806EB, 806EC, 906EA, 906EC, 906ED, A0660 — spanning Whiskey Lake through Comet Lake microcode)
- Plans to also pull ThinkPad T14 Gen 2 (Tiger Lake, LogoFAIL-affected model family per Binarly's public research) and Gen 3 (Alder Lake) for cross-generation diffing

**Tools currently installed/in use:**
- `UEFIExtract` (CLI) — NE alpha 75 build, from LongSoft/UEFITool GitHub releases
- `UEFITool` (GUI) — same repo, being downloaded separately
- Windows environment (not Linux) — all commands should be given in Windows/cmd syntax unless Sushanth specifies otherwise

**Key artifacts found inside the extracted Lenovo BIOS flash package (pre-extraction from the firmware volume itself):**
- `$0AR1B00.FL1` — the raw UEFI firmware image (17.9 MB), this is the file being fed to UEFIExtract/UEFITool
- `chklogo.exe` + `BIOS_LOGO.TXT` — Lenovo's logo validation utility, directly relevant to the LogoFAIL vulnerability class (DXE-phase image parser exploitation)
- Six Intel microcode `.PAT` files (listed above) — CPU silicon patches applied at early boot
- `pwdchk.exe` / `pwdchk64.exe` — BIOS password validation utility
- `ShellFlash.efi`, `bootx64.efi`, `WinFlash32/64.exe` — UEFI flashing utilities

**Latest UEFIExtract run result (on the T14 Gen 1 image) revealed:**
- Parser warnings: `unaligned volume`, `file header parsing failed with invalid size` — common with Phoenix images, not necessarily an error in the firmware itself
- **Protected Ranges found** (Phoenix's own integrity/hash verification mechanism, conceptually related to Intel Boot Guard):
  - Range 1: offset `B20000h`, size `3E0000h` (~4MB), SHA-256 hash `0B26A75B8AC7BCF802C33CB71A43E97333AE09D0AD1C64189357508C32096DA6`
  - Range 2: offset `AB0000h`, size `40000h` (~256KB), SHA-256 hash `3666E10F9678FE3B52FE96EFF74D10BDF4F6F8367EA43D65D41FA4CDEAC9FCBD`
- Multiple **FIT (Firmware Interface Table)** candidates found but not referenced from the last VTF (Volume Top File) — FIT tables list microcode updates, Boot Guard config, and Startup ACM (Authenticated Code Module) pointers
- Dump failed initially due to Windows path length limits (long nested folder path from the Lenovo installer extraction) — resolved by copying the `.FL1` to a short path (`C:\fw\`)

**Immediate next steps in progress:**
1. Re-run extraction from short path, generate full `.dump` folder + `.report` text tree
2. Search the report for `BMP`, `Jpeg`, `Logo`, `Smm`, `Tpm` module names
3. Locate the DXE-phase image/logo parsing driver (the LogoFAIL attack class target)
4. Cross-reference findings against public LogoFAIL writeups (Eclypsium, Binarly) to understand root cause
5. Eventually: diff BIOS versions (e.g., pre- and post-patch) to reverse-engineer what a security fix changed — mirroring Binarly's public research methodology

## 5. Reference Material Already Identified (for the local LLM to draw on if relevant)

**Primary vulnerability research blogs:**
- Eclypsium blog — eclypsium.com/blog (LogoFAIL, PixieFail writeups)
- Binarly blog — binarly.io/blog (Secure Boot bypasses, PKFail, BMC vulnerabilities, FwHunt detection rules)

**Books:**
- *Rootkits and Bootkits* — Alex Matrosov (No Starch Press) — UEFI bootkits, Secure Boot attacks
- *Beyond BIOS* — Vincent Zimmer — UEFI development reference
- *A Practical Guide to TPM 2.0* — Arthur & Challener (free PDF via Springer)
- OSTEP (*Operating Systems: Three Easy Pieces*) — free, pages.cs.wisc.edu/~remzi/OSTEP/ — for OS fundamentals brush-up (virtualization + concurrency sections most relevant)

**Specs:**
- UEFI Specification — uefi.org/specifications
- TPM 2.0 Spec — trustedcomputinggroup.org
- NIST SP 800-147 — BIOS security guidelines

**Tools:**
- UEFITool / UEFIExtract — github.com/LongSoft/UEFITool
- uefi-firmware-parser (Python) — github.com/theopolis/uefi-firmware-parser
- FwHunt (Binarly's detection rule format) — github.com/binarly-io/FwHunt
- IBM Software TPM 2.0 — github.com/kgoldman/ibmswtpm2
- Ghidra (reverse engineering) — ghidra-sre.org
- QEMU + OVMF — for safe UEFI emulation/testing

**Known public CVEs/vulnerability classes already studied or flagged for study:**
- LogoFAIL (2023) — DXE-phase image parser (BMP/JPEG) buffer overflow leading to arbitrary code execution before OS boot, affects many AMI/Insyde/Phoenix-based systems including Lenovo
- PixieFail (2024) — remote UEFI network stack vulnerabilities (PXE boot)
- PKFail — non-production/test Secure Boot keys shipped in production firmware (Binarly research)
- CVE-2024-0762 — Phoenix SecureCore buffer overflow (TPM-related, affects many Intel-based laptops including some Lenovo models)

## 6. What Good Research Output Looks Like (portfolio goal)

The end goal of this hands-on work is not just learning — it's building demonstrable, publishable security research artifacts:
- Firmware analysis tooling (open source, GitHub)
- Reproduction of a known CVE's root cause with clear technical writeup
- Firmware diffing analysis (before/after a security patch — what changed and why)
- Possibly: ML-assisted anomaly detection across firmware module sets (intersection with his existing AI background)

All public-facing output must only use publicly sourced firmware and publicly disclosed vulnerability information, per the legal boundaries in Section 3.

## 7. How Sushanth Prefers Technical Explanations (style guidance for the LLM)

- Wants concrete, inside-out walkthroughs — innermost operation first, then outward
- Appreciates ASCII diagrams for structural/hierarchical concepts (memory layouts, module trees, shape visualizations)
- Direct, no padding, no excessive caveats
- Learns by doing — prefers running tools and interpreting real output over abstract theory first
- Comfortable with dense technical material even if pace feels slow to him — this is normal, not a deficiency
- When something clicks, moves on without needing extra reassurance

## 8. AR — Next Action Item (for the model to act on when prompted)

Sushanth's next prompt to the larger model (Qwen2.5 32B) will ask it to generate a **robust 2-3 month plan** to:
- Develop something resume-worthy in firmware security research (a concrete project/artifact, not just study)
- Prepare for a potential job switch within that window
- Include a company target guide (who hires for this niche, what they look for)
- Include a prep structure (what to learn/build week by week)

When this prompt arrives, the model should treat Sections 1-7 above as full background and produce a specific, dated, week-by-week plan — not generic advice. The plan should assume the tooling, firmware samples, and legal boundaries already established in this file, and should build on the hands-on UEFIExtract/UEFITool work already in progress rather than starting over.

---
*End of context file. This covers direction, rationale, active technical state, and working constraints as of the current point in Sushanth's firmware security research track.*
