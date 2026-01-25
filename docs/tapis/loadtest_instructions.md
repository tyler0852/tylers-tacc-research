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
```

## Variables for getkey

```bash
TMS_PUBKEY_FINGERPRINT
TMS_PUBKEY_HOST
TMS_PUBKEY_KEYTYPE
TMS_PUBKEY_USER
TMS_PUBKEY_USERID
```

## Endpoints

- `getversion`
- `getclient`
- `getkey`
- `createkey`

## Running a Test

```bash
./target/release/tms_loadtest --users 10 --iterations 20000 --scenarios getclient --host $TMS_URL --report-file ~/tms-loadtest_local_getclient_perf-debug.html
```
