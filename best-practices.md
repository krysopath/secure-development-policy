# secure code development principles

## reasons for security incidents
### conceptual problems
1. Not identifying security requirements up front
2. Creating conceptual designs that have logic errors
3. Using poor coding practices that introduce technical vulnerabilities
4. Deploying the software improperly
5. Introducing flaws during maintenance or updating

### environmental problems
1. The software and its associated information
2. The operating systems of the associated servers
3. The backend database
4. Other applications in a shared environment
5. The user's system
6. Other software that the user interacts with
7. Networking infrastucture


## mitigation for security incidents

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL
NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and
"OPTIONAL" in this document are to be interpreted as described in
[RFC 2119](https://www.ietf.org/rfc/rfc2119.txt)

The project MUST have at least one primary developer who knows how to design secure software.

>A "primary developer" is anyone who is familiar with the project's code base, is comfortable making changes to it, and is acknowledged as such by most other participants in the project. A primary developer would typically make a number of contributions over the past year (via code, documentation, or answering questions). Developers would typically be considered primary developers if they initiated the project (and have not left the project more than three years ago), have the option of receiving information on a private vulnerability reporting channel (if there is one), can accept commits on behalf of the project, or perform final releases of the project software. If there is only one developer, that individual is the primary developer.

- At least one of the project's primary developers MUST know of common kinds of errors that lead to vulnerabilities in this kind of software, as well as at least one method to counter or mitigate each of them.



### knowledge
SUGGESTED read http://web.mit.edu/Saltzer/www/publications/protection/

###### key-take-aways:

- economy of mechanism (keep the design as simple and small as  practical, e.g., by adopting sweeping simplifications)

- fail-safe defaults (access decisions should deny by default, and projects' installation should be secure by default)

- complete mediation (every access that might be limited must be checked for authority and be non-bypassable)

- open design (security mechanisms should not depend on attacker ignorance of its design, but instead on more easily protected and changed information like keys and passwords)

- separation of privilege (ideally, access to important objects should depend on more than one condition, so that defeating one protection system won't enable complete access. E.G., multi-factor authentication, such as requiring both a password and a hardware token, is stronger than single-factor authentication)

- least privilege (processes should operate with the least privilege necessary)

- least common mechanism (the design should minimize the mechanisms common to more than one user and depended on by all users, e.g., directories for temporary files)

- psychological acceptability (the human interface must be designed for ease of use - designing for "least astonishment" can help)

- limited attack surface (the attack surface - the set of the different points where an attacker can try to enter or extract data - should be limited)

- input validation with whitelists (inputs should typically be checked to determine if they are valid before they are accepted; this validation should use whitelists (which only accept known-good values), not blacklists (which attempt to list known-bad values)).



## best practices

### technical design choices
#### open & exellent
- The software produced by the project MUST use, by default, only cryptographic protocols and algorithms that are publicly published and reviewed by experts (if cryptographic protocols and algorithms are used)

#### crypto bullshit forbidden
- If the software produced by the project is an application or library, and its primary purpose is not to implement cryptography, then it SHOULD only call on software specifically designed to implement cryptographic functions; it SHOULD NOT re-implement its own.

#### floss compatibility
- All functionality in the software produced by the project that depends on cryptography MUST be implementable using FLOSS.

#### keylength
- The security mechanisms within the software produced by the project MUST use default keylengths that at least meet the NIST minimum requirements through the year 2030 (as stated in 2012).

>These minimum bitlengths are: symmetric key 112, factoring modulus 2048, discrete logarithm key 224, discrete logarithmic group 2048, elliptic curve 224, and hash 224 (password hashing is not covered by this bitlength. See http://www.keylength.com for a comparison of keylength recommendations from various organizations. The software MAY allow smaller keylengths in some configurations (ideally it would not, since this allows downgrade attacks, but shorter keylengths are sometimes necessary for interoperability).

- It MUST be possible to configure the software so that smaller keylengths are completely disabled.


#### technical debt

- The external dependencies of the project SHOULD be upgraded to recent stable within 60 days

- The default security mechanisms within the software MUST NOT depend on broken cryptographic algorithms (e.g., MD4, MD5, single DES, RC4, Dual_EC_DRBG), or use cipher modes that are inappropriate to the context, unless they are necessary to implement an interoperable protocol (where the protocol implemented is the most recent version of that standard broadly supported by the network ecosystem, that ecosystem requires the use of such an algorithm or mode, and that ecosystem does not offer any more secure alternative).

- The documentation MUST describe any relevant security risks and any known mitigations if these broken algorithms or modes are necessary for an interoperable protocol.

>ECB mode is almost never appropriate because it reveals identical blocks within the ciphertext as demonstrated by the ECB penguin, and CTR mode is often inappropriate because it does not perform authentication and causes duplicates if the input state is repeated. In many cases it's best to choose a block cipher algorithm mode designed to combine secrecy and authentication, e.g., Galois/Counter Mode (GCM) and EAX. Projects MAY allow users to enable broken mechanisms (e.g., during configuration) where necessary for compatibility, but then users know they're doing it.

#### broken standards
- The default security mechanisms within the software produced by the project SHOULD NOT depend on cryptographic algorithms or modes with known serious weaknesses (e.g., the SHA-1 cryptographic hash algorithm or the CBC mode in SSH).

>https://www.kb.cert.org/vuls/id/958563

#### perfect forward secrecy
- The security mechanisms within the software produced by the project SHOULD implement perfect forward secrecy for key agreement protocols so a session key derived from a set of long-term keys cannot be compromised if one of the long-term keys is compromised in the future.

#### credentials storage
- If the software produced by the project causes the storing of passwords for authentication of external users, the passwords MUST be stored as iterated hashes with a per-user salt by using a key stretching (iterated) algorithm (e.g., PBKDF2, Bcrypt or Scrypt)

>This criterion applies only when the software is enforcing authentication of users using passwords, such as server-side web applications. It does not apply in cases where the software stores passwords for authenticating into other systems (e.g., the software implements a client for some other system), since at least parts of that software must have often access to the unhashed password.

#### randomness
- The security mechanisms within the software produced by the project MUST generate all cryptographic keys and nonces using a cryptographically secure random number generator, and MUST NOT do so using generators that are cryptographically insecure.

>A cryptographically secure random number generator may be a hardware random number generator, or it may be a cryptographically secure pseudo-random number generator (CSPRNG) using an algorithm such as Hash_DRBG, HMAC_DRBG, CTR_DRBG, Yarrow, or Fortuna. Examples of calls to secure random number generators include Java's java.security.SecureRandom and JavaScript's window.crypto.getRandomValues. Examples of calls to insecure random number generators include Java's java.util.Random and JavaScript's Math.random.

#### secure delivery channel

- The project MUST use a delivery mechanism that counters MITM attacks. Using https or ssh+scp is acceptable.

>An even stronger mechanism is releasing the software with digitally signed packages, since that mitigates attacks on the distribution system, but this only works if the users can be confident that the public keys for signatures are correct and if the users will actually check the signature.

- A cryptographic hash (e.g., a sha1sum) MUST NOT be retrieved over http and used without checking for a cryptographic signature.

- It is SUGGESTED to `git tag --sign <semver>` for each release

### vulnerabilities

- The project MUST publish the process for reporting vulnerabilities on the project site. (URL required)

- The project's initial response time for any vulnerability report received in the last 6 months MUST be less than or equal to 14 days.

#### public

- There MUST be no unpatched vulnerabilities of medium or high severity that have been publicly known for more than 60 days.

#### internal

- The vulnerability MUST be patched and released by the project itself (patches may be developed elsewhere).

>A vulnerability becomes publicly known (for this purpose) once it has a CVE with publicly released non-paywalled information (reported, for example, in the National Vulnerability Database) or when the project has been informed and the information has been released to the public (possibly by the project).

>A vulnerability is medium to high severity if its CVSS 2.0 base score is 4 or higher. Note: this means that users might be left vulnerable to all attackers worldwide for up to 60 days. This criterion is often much easier to meet than what Google recommends in Rebooting responsible disclosure, because Google recommends that the 60-day period start when the project is notified even if the report is not public.


### leaking creds

- The public repositories MUST NOT leak a valid private credential (e.g., a working password or private key) that is intended to limit public access.

- A project MAY leak "sample" credentials for testing and unimportant databases, as long as they are not intended to limit public access.

### static code analysis

- It is SUGGESTED that static source code analysis occur on every commit or at least daily.

### CI/CD

- At least one static code analysis tool (beyond compiler warnings and "safe" language modes) MUST be applied to any proposed major production release of the software before its release, if there is at least one FLOSS tool that implements this criterion in the selected language.

- It is SUGGESTED that at least one of the static analysis tools used for this include rules or approaches to look for common vulnerabilities in the analyzed language or environment.

### timely fixes

- All medium and high severity exploitable vulnerabilities discovered with static code analysis MUST be fixed in a timely way after they are confirmed.

>A vulnerability is medium to high severity if its CVSS 2.0 is 4 or higher.

### dynamic analysis

- It is SUGGESTED that at least one dynamic analysis tool be applied to any proposed major production release of the software before its release.

>A dynamic analysis tool examines the software by executing it with specific inputs. For example, the project MAY use a fuzzing tool (e.g., American Fuzzy Lop) or a web application scanner (e.g., OWASP ZAP or w3af). In some cases the OSS-Fuzz project may be willing to apply fuzz testing to your project. For purposes of this criterion the dynamic analysis tool needs to vary the inputs in some way to look for various kinds of problems or be an automated test suite with at least 80% branch coverage. The Wikipedia page on dynamic analysis and the OWASP page on fuzzing identify some dynamic analysis tools.

- It is SUGGESTED that if the software produced by the project includes software written using a memory-unsafe language (e.g., C or C++), then at least one dynamic tool (e.g., a fuzzer or web application scanner) be routinely used in combination with a mechanism to detect memory safety problems such as buffer overwrites. (ASAN Address Sanitizer, Memmory Sanitizer, valgrind)

- It is SUGGESTED that the software produced by the project include many run-time assertions that are checked during dynamic analysis.
