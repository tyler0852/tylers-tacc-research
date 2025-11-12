# Meeting 8 - November 12th, 2025

## What Got Done

### Added GitHub Links Page

- [GitHub Links](https://tylers-tacc-research.readthedocs.io/en/latest/other/githublinks/)

### Learned About Connection Pooling

- In order for our rust server and the database to be able to talk, they need to form a connection
- Without connection pooling, every request trys to open a new connection
    - This eventually leds to the "too many files open" error I was getting
- With connection pooling, there is a set amount of connections made that the requests share and wait for

### Implemented Connection Pooling in tms_min

- Create a new file `db_init.db` that establishes the connection pooling that is used by `main.rs`
- This fixed the "too many files open" error

### Ran tms_min_loadtest

| Scenario | Users | Iterations | %CPU | MEM Usage | State / Behavior | Notes |
|:-----------|:------|:-----------|:------|:------------|:------------------|:--------|
| writeheavy | 10 | 1,000 | — | — | - | - |
| writeheavy | 10 | 10,000 | — | — | - | - |
| writeheavy | 10 | 100,000 | 100–120 | ~5 MB | Alternates between running/sleeping/stuck | Indicates periodic I/O stalls during sustained writes. |
| writeheavy | 1000 | 100,000 | 100–120 | ~50 MB peak | Running steadily | mem steadily increased until peak |
| readheavy | 10 | 1,000 | — | — | - | - |
| readheavy | 10 | 10,000 | — | — | - | - |
| readheavy | 10 | 100,000 | ~800 Peak | — | - | - |
| readheavy | 1000 | 100,000 | ~830 Peak | — | Running continuously | Got to 100 users before other users started completing |
| readheavy (pool = 12) | 1000 | 100,000 | ~1000 Peak | — | Running continuously | Got to around 70 users before other user started completing |

- Note: For the first 8 test, the max amount of connection pools was 8, I increased it to 12 which is how many cores I have on my laptop for the last test
- From these test, I could not reproduce the error
- My next train of thought was to investigate how the tms_server endpoints interact with the database.
- Are my endpoint too lightweight to repoduce the error or is SQLite not the problem???

### CreateKey Endpoint Summary

- **Purpose:** Handles SSH key creation and storage for a client.  
- **Flow:**  
  1. **Authorization:** Validates client credentials (`clients` table).  
  2. **MVP Dependency Creation:** If enabled, auto-creates required records (`user_mfa`, `delegations`, `user_hosts`) with `ON CONFLICT DO NOTHING`.  
  3. **Dependency Validation:** Ensures all dependencies are active and not expired.  
  4. **Key Generation:** Generates the SSH key pair (CPU-only, no DB access).  
  5. **Public Key Storage:** Inserts the new key into `pubkeys` along with metadata.  

- **Database Activity:**  
  - ~5–8 total SQL operations per request (mix of reads and inserts).  
  - Involves up to five tables: `clients`, `user_mfa`, `delegations`, `user_hosts`, and `pubkeys`.  

- **Transactions:**  
  - Most logic uses transactions, but MVP creation performs **each insert in its own transaction**, increasing write-lock contention in SQLite.  

- **Key Insight:**  
  - The endpoint’s multiple small transactions across several tables likely contribute to the **load-related stalls** observed in `tms_server`.

### GetClient Endpoint Summary

- **Purpose:** Retrieves client information with authorization and tenant isolation.  
- **Flow:**  
  1. **Authorization:**  
     - *ClientOwn* – Validates client credentials (`clients` table, `GET_CLIENT_SECRET`).  
     - *TenantAdmin* – Validates admin credentials (`admin` table, `GET_ADMIN_SECRET`).  
  2. **Client Retrieval:**  
     - Fetches client record from `clients` using `GET_CLIENT`.  
     - Returns details like ID, tenant, app info, and timestamps.  
  3. **Consistency Check:**  
     - Verifies the authorized `client_id` matches the one in the request path.  

- **Database Activity:**  
  - 2 total SQL operations per request:  
    - 1 authorization query (`clients` or `admin`),  
    - 1 data retrieval query (`clients`).  
  - Involves up to two tables: `clients` and `admin`.  

- **Transactions:**  
  - Uses a standard transaction pattern (`begin → queries → commit`).  

- **Security Model:**  
  - Clients can only access their own records.  
  - Admins can access any client within their tenant.  
  - Tenant isolation enforced with `WHERE tenant = ?` in all queries.  

- **Key Insight:**  
  - Much simpler than `createkey` — a read-only operation with minimal transactions and low contention.


## Road Blockers and Questions

## Next Steps
