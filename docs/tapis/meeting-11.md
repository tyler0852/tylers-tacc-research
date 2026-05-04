# Meeting 11: May 4th, 2026

## What Got Done

### Potentially Fixed Error in Device Code Flow

- Before fix, the status would go from `Created` to `Entered`
- `Entered` meant both:
    - The user entered the user code
    - The CLI can give the user their token

- The new work flow is `Created` to `Entered` to `Authorized` where
    - `Created`: The CLI requested a device code
    - `Entered`: The user entered the user code
    - `Authorized`: The user submitted the TTL form and can exchange give the user their token

- Made two changes that accomplished this:
- After the TTl was entered, created a new status (controllers.py line 1575)
```bash
device_code.status = "Authorized"
```
- Changed the condition that exchanges the token from `Entered` to `Authorized` (models.py line 550)
- Orginal:
```
device_code.status = "Entered"
```
- Updated:
```
device_code.status = "Authorized"
```

### Moved TMS Loadtesting code inside the tapis-project

## Road Blockers
- I tried to test my changes by running the authenticator locally but couldn't figure that out

## What's Next
- Test fix
- Add some quality of life improvments to the load test code
