# Meeting 1 — September 3rd, 2025

## What got Done
- Got a better understanding of the TMS workflow. My current understanding:

    - TMS exists for multiple tenants to use
    - A tenant is like a company such as TACC
    - Within each tenant exist user, apps, and different hosts
    - As a user of TACC, I will be given a set of creds that defines which hosts I have access to and links every account I have any every host to my single TACC account (which is a tenant)
    - Clients exist to connect different apps that the tenant offers to TMS

        - We give apps a client as a way to tell TMS this app is trusted and that we can use it
        - This prevents any app from just asking TMS what my creds are
        - Each app would need to have a client to TMS. This is done from the dev side, no user of TMS would ever need to touch clients
        - The devs would also need to configure the app login to automatically communicate with TMS to check if the user has access to this app on which hosts

- Ran TMS Server

    - Did basic curl commands
    - Created a test client
    - Located SQLite DB and found the test client inside

- Ran the load test (kinda) and understand how it works

    - Need to start tms_server
    - The load test has 4 different scenarios (createkey, getclient, getkey, getversion)
    - The load test mimics being a user or many clients and hits tms_server with a bunch of requests

- Created a new server ‘tms_min’

    - Right now it only has one route that says hello from tms and states the current tenant

## Road Blockers and Questions
- When do you use the host passwords it gives you when you start TMS?
- I struggled a bit in testing the public key creation

    - I couldn't figure out how to enable the test tenant, only the default tenant was enabled
    - Because of this, the client I created was in the default tenant
    - This caused issues later when trying to create creds for the client because I didn’t have authorization to give it MFA

- I ran into many issues dealing with certificate issues

    - When just doing things on the TMS server I could get around by doing `curl -k`
    - I couldn’t use this when doing the load test because of how it is built using Goose
    - I tried to get around this issue by modifying main but was unsuccessful. Is there an easy fix?

## What’s Next!
- Enable the default tenant so that I can create a test client and create a public key
- Run different load test and see actual results
- Continue to build tms_min
