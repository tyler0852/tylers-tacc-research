# Instructions For Running The Loadtest  

## Local Testing

- In system root, clear `rm -rf .tms`
- In tms_server or whatever branch of it modify `tms.toml`
    - Remove tls from `http_addr` and `server_urls`
    - `enable_mvp = true`
    - `enable_test_tenant = true`
- Go to root of tms_server

```bash
./target/release/tms_server --install
cargo run --release
```

- Test server
```bash
curl http://localhost:3000/v1/tms/hello
```

### Running tms_loadtest
```bash
cargo run --release -- --host http://127.0.0.1:3000 --users 1 --iterations 1000 --scenarios createkey
```

### loadtesting suite locally
```bash
export X_TMS_TENANT=test
export X_TMS_ADMIN_ID="~~admin"
export X_TMS_ADMIN_SECRET="2e6ef3c29ea6d72df752ce75589b8dc9e6f68af8ad9ca25a"
```
- Do a create key

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

- If hitting local server
```bash
export TMS_URL="http://127.0.0.1:3000"
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
