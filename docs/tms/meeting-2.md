# Meeting 2 — 09/10/2025

## What Got Done
- Read through the shared Google Drive.
- Identified core problem: **cross-site automation is hard**  
  (MFA, key distribution, multi-institution identities).
- Clarified TMS goals:
  - Delegated command execution
  - Account linking
  - Sandbox/deploy tools
  - Modular Rust design
- Current authentication methods:
  - One-time SSH keys
  - Short-lived tokens (PAM)
  - HTTPS host agent
  - TOTP for MFA
- Planned authentication improvements:
  - WebAuthn/FIDO2
  - Standards-based tokens (SciTokens/WLCG/EGI)
- Team & operations:
  - UT Austin lead with UH, SDSC, OSC, Globus, etc.
  - Agile workflow + CI/CD
- Outreach:
  - Workshops, hackathons, training
- Roadmap:
  - Prototype → Tokens/PAM → WebAuthn → Sandbox
  - Sustain via OSS, audits, production deployments
- Risks & review:
  - Novelty, missing threat model, single points of failure, adoption plan, broader impacts
  - Mitigation: tighten scope, explicit threat/innovation framing
- Ran `tms_server` again.
- Learned there are **two config files**:
  - `~/.tms` overrides the config inside `tms_server`.
- Modified config:
  - Enabled the test tenant
  - Changed `http_addr` to drop the secure part
  - This allowed creating and listing new keys (creds).
- Got `tms_loadtest` working once MVP was enabled.
- Continued building `tms_min`:
  - Slowed down to better understand each line of code.
  - Switched to **Poem** framework to match `tms_server`.

## Road Blockers
- In `tms_server`, able to **create and list** keys but not able to **delete** them.

## What’s Next
1. Figure out how to delete keys in `tms_server`.
2. Run more rigorous tests with `tms_loadtest`.
3. Continue building out `tms_min`.

