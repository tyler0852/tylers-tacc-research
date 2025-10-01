# Meeting 4 - September 31st, 2025

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
- Public, not secret variables:
    - A
    - B
    - p
    - g
- Private, secret variables:
    - a
    - b
    - s
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

$$A = g^a \pmod p$$

$$B = g^b \pmod p$$

$$s = B^a \pmod p$$

$$s = A^b \pmod p$$

- Use Cases
    - TLS
    - SSH
    - VPNs
    - Messaging apps
    - The frequency in which a Diffie-Hellman occurs will change on the use case. For things like TLS and SSH, it will most likely do a Diffie-Hellman everytime you make a connection. For something like messaging apps, it will be used repeatedly, could be every message, could be longer until it is invoked again.

#### The Discrete Logarithm Problem (DLP)

- This is what makes Diffie-Hellman work
- The DLP states:
    - Given: A prime modulus p, a generator g, and a value A = g^a mod p
    - Find: a using A, g, and p
- With a prime number and a good generator, g^a mod p cycles through almost all possible values up to p
- This makes A look random, but there is no way to find a other than brute force

#### Elliptic Curve Diffie-Hellman (ECDH)

- ECDH is the elliptic curve version of Diffie-Hellman
- Instead of using exponentiation in integers mod p, it uses scalar multiplication on points of an elliptic curve
- Domain Parameters (public):
    - p: field (modulo p)
    - a, b: curve parameters, where the curve is defined as y² = x³ + ax + b
    - G: generator point (base point) on the curve
    - n: order of G (size of subgroup)
    - h: cofactor (|E(Zp)| / n, ideally h = 1)
- Private, secret variables:
    - a: Alice’s private key
    - b: Bob’s private key
- Public, not secret variables:
    - A = aG (Alice’s public key, point on curve)
    - B = bG (Bob’s public key, point on curve)
- Shared Secret:
    - Alice computes: S = aB = abG
    - Bob computes: S = bA = abG
    - Both arrive at the same point S
- Why elliptic curves?
    - Much smaller encryption keys use fewer memory and cpu resources
    - Has the same security
    - Faster, requires less bandwidth, and more efficient overall
- Use Cases:
    - TLS (modern versions use ECDHE for forward secrecy)
    - SSH
    - VPNs (e.g., WireGuard)
    - Messaging apps (Signal, WhatsApp, etc.)

#### Other Public Key Encryption Methods

- **RSA (Rivest–Shamir–Adleman)**  
    - Based on the difficulty of factoring large integers.  
    - Widely used in older TLS handshakes, digital certificates, and PGP.  
    - Being phased out in favor of elliptic-curve methods due to large key sizes.  

- **DSA (Digital Signature Algorithm)**  
    - Based on the discrete logarithm problem.  
    - Used for digital signatures; less common today.  

- **ECDHE (Elliptic Curve Diffie–Hellman [Ephemeral])**  
    - Based on the elliptic curve discrete logarithm problem.  
    - ECDHE provides forward secrecy by generating new keys for each session.  

- **ECDSA (Elliptic Curve Digital Signature Algorithm)**  
    - Elliptic-curve version of DSA.  
    - Used for digital signatures in TLS certificates, SSH, and blockchains.  

- **EdDSA (Edwards-curve Digital Signature Algorithm)**  
    - Modern elliptic-curve signature scheme (e.g., Ed25519, Ed448).  
    - Faster and safer against side-channel attacks than ECDSA.  

- **Post-Quantum Algorithms (PQCrypto)**  
    - Based on hard lattice problems and other quantum-resistant math.  
    - Examples: Kyber (key exchange), Dilithium (signatures).  
    - Currently being standardized by NIST for future use.  

---

| Algorithm | Security Basis | Typical Key Size | Strengths | Weaknesses | Common Uses |
|-----------|----------------|------------------|-----------|------------|-------------|
| **RSA**   | Integer factorization | 2048–4096 bits | Well-studied, widely deployed | Slow, large key sizes | TLS (older), PGP, digital certificates |
| **DSA**   | Discrete logarithm    | 2048+ bits     | First digital signature standard | Needs good randomness, less efficient | Legacy signatures |
| **ECDH**  | Elliptic curve discrete log (key exchange) | 256–521 bits | Secure key exchange, efficient | No forward secrecy if static | TLS, VPNs, messaging |
| **ECDHE** | Elliptic curve discrete log (ephemeral key exchange) | 256–521 bits | Forward secrecy, widely adopted | Slightly more overhead | TLS 1.3, SSH, Signal, WireGuard |
| **ECDSA** | Elliptic curve discrete log (signatures) | 256–521 bits | Efficient signatures, widely used | More complex math | TLS certs, SSH, Bitcoin/Ethereum |
| **EdDSA** | Elliptic curve discrete log (Edwards curves) | 256–448 bits | Very fast, side-channel resistant | Newer, less legacy support | TLS 1.3, SSH, modern apps |
| **Post-Quantum (e.g. Kyber, Dilithium)** | Lattice problems (PQC) | Varies (much larger than ECC) | Resistant to quantum attacks | Still experimental, larger signatures | Future TLS, VPNs, secure messaging |

#### Certificates (X.509 / SSL/TLS)

- A certificate is basically a digital document that links an identity (like a website or business) to a public key
- Certificates follow the X.509 standard which defines their structure
- They are used in SSL/TLS to let two parties verify each other and then set up an encrypted connection
- At a high level, they act like digital ID cards for systems on the internet

- Key terms:
    - **X.509**: The standard that defines certificate format and fields
    - **SSL (Secure Sockets Layer)**: Old protocol for encrypted web traffic (now obsolete)
    - **TLS (Transport Layer Security)**: The modern protocol that replaced SSL, what we really use today
    - **Issuer**: The entity that signs and vouches for the certificate
    - **CA (Certificate Authority)**: A trusted third party that issues certificates
    - **Self-signed certificate**: A certificate where the subject signs itself; valid locally, but not globally trusted
    - **CA root certificate**: A self-signed certificate trusted because it comes pre-installed in OS and browsers

- What’s included in a certificate:
    - Domain name or identity
    - Public key
    - Issuer
    - Issuer’s digital signature
    - Validity period (currently max 13 months)
    - Extensions (subject alt names, allowed key usages, etc.)

- Types of certificates:
    - **DV (Domain Validated)**: Proves control of the domain. Easiest, often free.
    - **OV (Organization Validated)**: Adds verified business info.
    - **EV (Extended Validation)**: Strongest vetting, used where assurance is critical.
    - **Wildcard**: Covers a domain and all subdomains.
    - **Multi-domain (SAN)**: Covers multiple domains in one certificate.

- Certificate trust model:
    - Systems and browsers ship with a trust store of CA root certificates
    - A site cert is trusted if it chains back to a root in that trust store
    - Example: Root CA (self-signed) → Intermediate CA → Leaf certificate (your website)

- Let’s Encrypt:
    - A free, open, automated CA
    - Issues DV certificates at no cost
    - Certificates are short-lived (90 days) but can auto-renew
    - Uses the ACME protocol (e.g., certbot tool) to automate issuing and renewal
    - Typical flow: install certbot → prove you control the domain → cert issued → server configured for HTTPS

- Localhost certificates:
    - Let’s Encrypt can’t issue certs for “localhost” since nobody owns it
    - For dev/testing you generate your own (self-signed) and trust it locally
    - Tools like `openssl` or `mkcert` can create a local root + leaf certs for a smoother workflow

#### Core PKI Entities

- **CA (Certificate Authority)**  
    - The trusted party that issues and signs certificates  
    - Root CAs are built into operating systems and browsers  
    - They are what make a certificate trusted outside of your own system  

- **RA (Registration Authority)**  
    - Handles identity checks before a certificate is issued  
    - Works with the CA to verify that the requester is who they claim to be  

- **VA (Validation Authority)**  
    - Provides a way to check if a certificate is still valid  
    - Helps make sure expired or untrusted certificates can’t be used  


## Road Blockers and Questions

## What's Next!
