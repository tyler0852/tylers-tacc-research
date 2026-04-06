# Meeting 6: April 6th, 2026

## What Got Done

### Got All Current Endpoints Working

- apps healthcheck
```bash
cargo run -- apps healthcheck
```

- auth hello
```bash
cargo run -- auth hello
```

- Generate tokens with password grant
```bash
cargo run -- --base-url https://tacc.tapis.io auth generate-tokens --grant-type password --username "$TAPIS_USERNAME" --password "$TAPIS_PASSWORD"
```

- Generating device code
```bash
cargo run -- auth generate-device-code --client-id tylers_client
cargo run -- --base-url https://tacc.tapis.io auth generate-device-code --client-id tylers_client
```

## Road Blockers

- Not sure if I should be using dev or tacc tenant
- On tacc tenant, can't do device_code grant

## Next Steps

- Add client creation endpoints
- Add automated device code flow

