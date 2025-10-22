# Meeting 6 - October 22nd, 2025

## What Got Done

### More Load Testing

- To see full results of load testing. Go to the load test runs tab

- `getversion`
    - Never fails

- `getclient`
    - Fails at 10 users after around 60k req
    - When throttled, fails somewhere between 100-500 req/s at 10 user
    - At 500 req/s, still failed after 60k req

- `createkey`
    - Fails with 10 users, no throttle, between 2500-3000 iterations
    - Throttling helps, but will still freeze eventually

- `getkey`
    - Need help with environment variables

### Looking at System while running

The test I chose to repeat while looking at system was `createkey`, 10 users, 20,000 iterations, 250 req/s. When I last ran this, it failed after ~60K iterations.

The only difference I made in the test was increased iteration to 50,000

I used `top` and isolated tms_server's PID to view diagnostics 

Results:

- Got through almost 200k iterations, I did clear the sqlite db inbetween these runs. I expect that it was less full during this run. Maybe thats why it was able to get through almost 200K as opposed to 60K
- Lasted 7 minutes of CPU runtime
- Was sleeping more that it was running
- MEM stable at 15M
- CPU usage ranged between 20-60% the entire time until it crashed and instantly dropped to 0%

Thoughts:

- My inital thoughts are that because tms_server is sleeping much more than it is running. The server is I/O bounded writing things to sqlite and not CPU or memory bounded
- I'm guessing that it can compute much faster than it can output the results, a queue forms, then at some point the queue gets too long for sqlite to handle and then crashes
- This would explain why throttling helps because it gives tms_server more time to output results
- I feel like this also explains why regardless of certain factors, different test will fail in similar spots (i.e. different test both failing after 50k iterations)
- All tests for 1 user, regardless of how many iterations, never failed. Also supports this hypothesis
- Once we add more users, only 1 can write the sqlite at a time, others wait, queue forms
- I then removed throttling and it failed after about ~8 seconds of CPU run time, further supports current hypothesis

### A Solution?

- After this, I thought about if there would be an obtainable solution if this is the problelm
- I thought about the extreme case, say this server becomes immesly popular
- I ran a test with 1000 users, all trying to do 100000 iterations, but limited them to 10 req/s
- The server runs with no issues, even with that many concurrent users as long as we limit the amount of requests they do
- Can we make a global/server max amount of requests that can happen, and then spread that among the current users doing requests?

## Road Blocker and Questions

- Need environment variables for `getkey`

## Next Steps

- If we think this is the issue, modifying tms_server to limit the amount of global requests it takes
- This would require more testing to more accuretly figuring how much it can handle
- We would need to note if adding things like tls back in the picture would affect this limit
- Would also need to account for testing being done locally, not where the server would be hosted at TACC
- Would be interesting to test this on a TACC cluster, which would likly have much better I/O than I am getting running locally
