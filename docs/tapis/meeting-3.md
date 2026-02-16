# Meeting 3: February 16th, 2026

## What Got Done

### `tms_loadtesting_suite`

- If you want to run against the dev server
```bash
export TMS_URL=https://tms-server-dev.tacc.utexas.edu:3000
```

- If you want to run against a local instance of `tms_server`
```bash
export TMS_URL="http://127.0.0.1:3000"
```

- Shared exports
```bash
export TMS_PARSE_RESPONSE=true
export X_TMS_CLIENT_ID=testclient1
export X_TMS_CLIENT_SECRET=secret1
export X_TMS_TENANT=test
export TMS_PUBKEY_FINGERPRINT="SHA256:Y1nwRJ82LwfEwr2cxe48pB5fzqBK3DoKK4DuH584pDw"
export TMS_PUBKEY_HOST="testhost1"
export TMS_PUBKEY_KEYTYPE="ed25519"
export TMS_PUBKEY_USER="testhostaccount1"
export TMS_PUBKEY_USERID="1"
```

- If running locally, in `tms.toml`
  - `enable_mvp = true`
  - `enable_test_tenant = true`
 
- A caveat...
  - When running locally, `getkey` wouldn't work
  - What I tried was:
    - Creating a client locally
    - Then creating a pubkey
    - When I tried making the pubkey it gave me an error about not finding delegation record
    - When doing this process before I had never run into this and had already spent more time here that I probably should've so I moved on
   
- Initial findings:
- `tms_server` still hangs
- Shows signs of handeling database concurrency better
- Results may be much worse, could be due to VM vs Mac though

### 

## Road Blockers
- Getting `getkey` to work locally

## Next Steps
- Continue learning about OAuth and Device Authorization Flow
- At some endpoints talking to Tapis in CLI
