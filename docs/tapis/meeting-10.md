# Meeting 10: April 20th, 2026

## What Got Done

### Got tapis-CLI Working!

- Got a working version of the CLI

- Can run it with 

```bash
cargo run -- get-token
```

- Example work flow:
```bash
tyler@Tylers-MacBook-Pro tapis_cli % cargo run -- get-token
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.27s
     Running `target/debug/tapis-cli get-token`

To authorize this application, please follow these steps:
Go to: https://tacc.tapis.io/v3/oauth2/device?client_id=tapis_cli
Enter this code: oMbhbSly
Waiting for authorization...
...
...
...


Authorization successful.

access_token: eyJhbG...

access_token_expires_at: 2026-05-12T17:32:29.834250+00:00

access_token_expires_in: 2592000

refresh_token: eyJhbG...

refresh_token_expires_at: 2027-04-12T17:32:29.880455+00:00

refresh_token_expires_in: 31536000
```

- Found current issue with the token endpoint
- Copied Slack message about it: 
    - I noticed maybe a flaw in the current tokens endpoint. The normal steps would be:

    1. Call the device/code endpoint to get a device_code, user_code, and verification_uri
    2. Go to the url in your browser
    3. Login and enter the provided user_code
    4. Enter how many days you want to allow this authorization
    
    - The potential flaw is that when we try to hit the tokens endpoint before step 3, you get the expected output "device code not ready". However, once you've done step 3, regardless if you have entered how many days you want to allow the authorization, once you call the tokens endpoint it will give you your token. And since the endpoint is designed that once you get your token you shouldn't be able to use it anymore, if they user calls the tokens endpoint in between steps 3 and 4, it gives you an error when you try to enter how many days you want to allow the authorization. How this affects my current program is that since it it polling the tokens endpoint every few seconds, it will pretty much always hit the tokens endpoint before the user can enter how many days and give a default value of 1 day.

### Started Looking Into This Issue

- I first started by identifying the current workflow

- It is using a PostgreSQL database and the SQLAlchemy Python library 

#### Step 1: CLI asks for a device code

- Starts at line 1165
- Apart of `class DeviceCodeResource(Resource):`
- Submits a post request
- Makes sure it is a valid request
- Looks up the client in the database
- Builds URL used for verification URI
- Creates device code object and saves it in the database
- Returns a response to the user
- After this step the database row is:
```bash
code = long device code
user_code = short browser code
status = "Created"
username = None
access_token_ttl = default 30 days
```

## What's Next
- Finish working out the authenticator flow
- See if I can make changes and test locally

