# Meeting 2 — September 10th, 2025

## What got Done
- Read through the shared google drive

    - Problem: cross-site automation is hard (MFA, key distribution, multi-institution identities).
    - TMS goal: delegated command exec + account linking + sandbox/deploy tools; modular Rust.
    - Auth now: one-time SSH keys, short-lived tokens (PAM), HTTPS host agent; TOTP handles MFA.
    - Next auth: WebAuthn/FIDO2; token standards (SciTokens/WLCG/EGI).
    - Team/ops: UT Austin lead w/ UH, SDSC, OSC, Globus, etc.; agile + CI/CD.
    - Outreach: workshops/hackathons/training
    - Roadmap: prototype → tokens/PAM → WebAuthn → sandbox; sustain via OSS + audits + prod deploys.
    - Review risks: novelty, missing threat model, SPOF, adoption plan, broader impacts → tighten scope + explicit threat/innovation framing.

- Ran tms_server again

    - Realized that there are two config files!
    - When doing cargo run it will use the one in ~/.tms if it exist over what is in tms_server
    - In the config file, I enable the test tenant and changed `http_addr` to not include the secure part
    - This allowed me to create and list new keys (creds)

- Was able to get tms_loadtest working once I had enabled mvp
- Continued to build tms_min

    - Slowed down to better understand every line I was writing
    - Switched to poem to match tms_server

## Road Blockers
- tms_server

    - I was able to create and list keys, but I couldn’t figure out how to delete it

## What’s Next!
- Figure out how to delete keys in tms_server
- Run more rigorous test on tms_loadtest
- Continue building tms_min

