# Meeting 4 - September 24th, 2025

## What Got Done

#### Learned how Diffie-Hellman works

- Reviewed the famous Alice and Bob example
- In short, the process looks like:
    1. Two people will have privates keys
    2. Each person will create a public key using their private that they share with the other person
    3. Each person then combines their own private key with the public key that was given to them by the other person
    4. Using this combination, they each create a new secret that they both know
 - Variables
    - a: Person 1's private key
    - b: Person 2's private key
    - A: Persons 1's public key
    - B: Persons 2's public key
    - p: A large prime number
    - g: A number less than p, often reffered to as the generator
    - s: The secret that person 1 and person 2 will share after doing Diffie-Hellman
- Conditions
    - p:
        - Must be a large prime number
        - Typically at least 2048 bits
        - Defines the finite field Zp (integers mod p)
    - g:
        - Must be an element of (Zp)^* (the multiplicative group, nonzero)
        - Chosen so its powers cycle through most or all of the values modulo p
    - a&b:
        - Must be in the range [1,p-2]
        - Need to be less than p because computations are modulo p
    - A&B:
        - Always between 1 and p-1
- Variable Definitions:
$$
A = g^a \pmod p \\
B = g^b \pmod p \\
s = g^{ab} \pmod p
$$
       

## Road Blockers and Questions

## What's Next!
