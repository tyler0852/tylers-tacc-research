# Instructions For Running The Loadtest  

## VM Address

```bash
tms-loadtest.tacc.cloud
```

## Needed Environment variables

```bash
export TMS_URL=https://tms-server-dev.tacc.utexas.edu:3000
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

## Endpoints

- `getversion`
- `getclient`
- `getkey`
- `createkey`

## Running a Test

- Long version:
  
```bash
./target/release/tms_loadtest --users 10 --iterations 20000 --scenarios getclient --host $TMS_URL --report- file ~/tms-loadtest_local_getclient_perf-debug.html
```
- Short version

```bash
./target/release/tms_loadtest --users 1 --iterations 1 --scenarios get --host $TMS_URL
```
