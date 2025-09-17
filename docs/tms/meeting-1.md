# Meeting 1 — 09/03/2025

## What Got Done
- Gained a better understanding of the TMS workflow. Current understanding:
  - TMS exists for multiple tenants to use.
  - A tenant is like a company such as TACC.
  - Within each tenant exist users, apps, and different hosts.
  - As a TACC user, I am given a set of credentials that defines which hosts I have access to and links every account I have on any host to my single TACC account (the tenant).
  - Clients connect different apps that the tenant offers to TMS.
  - Apps are given a client so TMS knows the app is trusted. This prevents any app from freely asking TMS for credentials.
  - Each app needs a client in TMS. This is handled by developers; regular users never touch clients.
  - Developers configure app logins to automatically communicate with TMS to check which apps a user can access on which hosts.
- Ran TMS server.
- Performed basic `curl` commands.
- Created a test client.
- Located the SQLite DB and confirmed the test client inside.
- Ran the load test (partially) and learned how it works:
  - Must start `tms_server`.
  - Load test has 4 scenarios: `createkey`, `getclient`, `getkey`, `getversion`.
  - The load test mimics one or many users hitting TMS server with requests.
- Created a new server `tms_min`:
  - Currently only has one route that says “hello from tms” and states the current tenant.

## Road Blockers and Questions
- When do you use the host passwords provided when starting TMS?
- Struggled with testing public key creation.
- Could not figure out how to enable the **test** tenant—only the default tenant was enabled.
  - Created client in the default tenant.
  - This caused issues when creating credentials for the client (no authorization to assign MFA).
- Encountered certificate issues:
  - Could bypass with `curl -k` directly on TMS server.
  - Could not bypass when using load test with Goose.
  - Tried modifying `main.rs` to skip TLS but was unsuccessful. Is there an easy fix?

## What’s Next
- Enable the default tenant so that I can create a test client and a public key.
- Run different load tests and capture actual results.
- Continue building out `tms_min`.

