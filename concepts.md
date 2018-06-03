#concepts

## Three Goals
Often computer security objectives (or goals) are described in terms of three overall objectives:

- Confidentiality (also known as secrecy), meaning that the computing system’s assets can be read only by authorized parties.
- Integrity, meaning that the assets can only be modified or deleted by authorized parties in authorized ways.
- Availability, meaning that the assets are accessible to the authorized parties in a timely manner (as determined by the systems requirements). The failure to meet this goal is called a denial of service.

    >Some people define additional major security objectives, while others lump those additional goals as special cases of these three. For example, some separately identify non-repudiation as an objective; this is the ability to “prove” that a sender sent or receiver received a message (or both), even if the sender or receiver wishes to deny it later. Privacy is sometimes addressed separately from confidentiality; some define this as protecting the confidentiality of a user (e.g., their identity) instead of the data. Most objectives require identification and authentication, which is sometimes listed as a separate objective. Often auditing (also called accountability) is identified as a desirable security objective. Sometimes “access control” and “authenticity” are listed separately as well.

## Why insecure code?

- Programming books/classes do not teach secure/safe programming techniques. Indeed, until recently there were no books on how to write secure programs at all (this book is one of those few).
- No one uses formal verification methods.
- C is an unsafe language, and the standard C library string functions are unsafe. This is particularly important because C is so widely used - the “simple” ways of using C permit dangerous exploits.
- Programmers do not think “multi-user.”
- Programmers are human, and humans are lazy. Thus, programmers will often use the “easy” approach instead of a secure approach - and once it works, they often fail to fix it later.
- Most programmers are simply not good programmers.
- Most programmers are not security people; they simply don’t often think like an attacker does.
- Most security people are not programmers. This was a statement made by some Bugtraq contributors, but it’s not clear that this claim is really true.
- Most computer security models are terrible.
- There is lots of “broken” legacy software. Fixing this software (to remove security faults or to make it work with more restrictive security policies) is difficult.
- Consumers don’t care about security. (Personally, I have hope that consumers are beginning to care about security; a computer system that is constantly exploited is neither useful nor user-friendly. Also, many consumers are unaware that there’s even a problem, assume that it can’t happen to them, or think that that things cannot be made better.)
- Security costs extra development time.
- Security costs in terms of additional testing (red teams, etc.).

>There is no curriculum that addresses computer security in most schools. Even when there is a computer security curriculum, they often don’t discuss how to write secure programs as a whole. Many such curriculum only study certain areas such as cryptography or protocols. These are important, but they often fail to discuss common real-world issues such as buffer overflows, string formatting, and input checking. I believe this is one of the most important problems; even those programmers who go through colleges and universities are very unlikely to learn how to write secure programs, yet we depend on those very people to write secure programs.

## The Common Criteria for Information Technology Security Evaluation (yaddayadda)

- International Standard ISO/IEC 15408:1999
- outrageously expensive for devs

### Protection Profile & Security Target

Although it can be used in other ways, the CC is typically used to create two kinds of documents, a “Protection Profile” (PP) or a “Security Target” (ST). 

- A “protection profile” (PP) is a document created by group of users (for example, a consumer group or large organization) that identifies the desired security properties of a product. Basically, a PP is a list of user security requirements, described in a very specific way defined by the CC. If you’re building a product similar to other existing products, it’s quite possible that there are one or more PPs that define what some users believe are necessary for that kind of product (e.g., an operating system or firewall). 
- A “security target” (ST) is a document that identifies what a product actually does, or a subset of it, that is security-relevant. An ST doesn’t need to meet the requirements of any particular PP, but an ST could meet the requirements of one or more PPs.

>The vast majority of the CC’s text is used to define standardized functional requirements and assurance requirements. In essence, the majority of the CC is a “chinese menu” of possible security requirements that someone might want. PP authors pick from the various options to describe what they want, and ST authors pick from the options to describe what they provide.

## Security functionality requirements

Here are the major classes of CC security requirements, along with the 3-letter CC abbreviation for that class: 

- *Security Audit (FAU)*. Perhaps you’ll need to recognize, record, store, and analyze security-relevant activities. You’ll need to identify what you want to make auditable, since often you can’t leave all possible auditing capabilities enabled. Also, consider what to do when there’s no room left for auditing - if you stop the system, an attacker may intentionally do things to be logged and thus stop the system.

- *Communication/Non-repudiation (FCO)*. This class is poorly named in the CC; officially it’s called communication, but the real meaning is non-repudiation. Is it important that an originator cannot deny having sent a message, or that a recipient cannot deny having received it? There are limits to how well technology itself can support non-repudiation (e.g., a user might be able to give their private key away ahead of time if they wanted to be able to repudiate something later), but nevertheless for some applications supporting non-repudiation capabilities is very useful.

- *Cryptographic Support (FCS)*. If you’re using cryptography, what operations use cryptography, what algorithms and key sizes are you using, and how are you managing their keys (including distribution and destruction)?

- *User Data Protection (FDP)*. This class specifies requirement for protecting user data, and is a big class in the CC with many families inside it. The basic idea is that you should specify a policy for data (access control or information flow rules), develop various means to implement the policy, possibly support off-line storage, import, and export, and provide integrity when transferring user data between TOEs. One often-forgotten issue is residual information protection - is it acceptable if an attacker can later recover “deleted” data?

- *Identification and authentication (FIA)*. Generally you don’t just want a user to report who they are (identification) - you need to verify their identity, a process called authentication. Passwords are the most common mechanism for authentication. It’s often useful to limit the number of authentication attempts (if you can) and limit the feedback during authentication (e.g., displaying asterisks instead of the actual password). Certainly, limit what a user can do before authenticating; in many cases, don’t let the user do anything without authenticating. There may be many issues controlling when a session can start, but in the CC world this is handled by the "TOE access" (FTA) class described below instead.

- *Security Management (FMT)*. Many systems will require some sort of management (e.g., to control who can do what), generally by those who are given a more trusted role (e.g., administrator). Be sure you think through what those special operations are, and ensure that only those with the trusted roles can invoke them. You want to limit trust; ideally, even more trusted roles should be limited in what they can do.

- *Privacy (FPR)*. Do you need to support anonymity, pseudonymity, unlinkability, or unobservability? If so, are there conditions where you want or don’t want these (e.g., should an administrator be able to determine the real identity of someone hiding behind a pseudonym?). Note that these can seriously conflict with non-repudiation, if you want those too. If you’re worried about sophisticated threats, these functions can be hard to provide.

    Protection of the TOE Security Functions/Self-protection (FPT). Clearly, if the TOE can be subverted, any security functions it provides aren’t worthwhile, and in many cases a TOE has to provide at least some self-protection. Perhaps you should "test the underlying abstract machine" - i.e., test that the underlying components meet your assumptions, or have the product run self-tests (say during start-up, periodically, or on request). You should probably "fail secure", at least under certain conditions; determine what those conditions are. Consider phyical protection of the TOE. You may want some sort of secure recovery function after a failure. It’s often useful to have replay detection (detect when an attacker is trying to replay older actions) and counter it. Usually a TOE must make sure that any access checks are always invoked and actually succeed before performing a restricted action.

- *Resource Utilization (FRU)*. Perhaps you need to provide fault tolerance, a priority of service scheme, or support resource allocation (such as a quota system).

- *TOE Access (FTA)*. There may be many issues controlling sessions. Perhaps there should be a limit on the number of concurrent sessions (if you’re running a web service, would it make sense for the same user to be logged in simultaneously, or from two different machines?). Perhaps you should lock or terminate a session automatically (e.g., after a timeout), or let users initiate a session lock. You might want to include a standard warning banner. One surprisingly useful piece of information is displaying, on login, information about the last session (e.g., the date/time and location of the last login) and the date/time of the last unsuccessful attempt - this gives users information that can help them detect interlopers. Perhaps sessions can only be established based on other criteria (e.g., perhaps you can only use the program during business hours).

- *Trusted path/channels (FTP)*. A common trick used by attackers is to make the screen appear to be something it isn’t, e.g., run an ordinary program that looks like a login screen or a forged web site. Thus, perhaps there needs to be a "trusted path" - a way that users can ensure that they are talking to the "real" program.



## Security Assurance Measure

Here are some assurance measures that can increase the confidence others have in your software:

- *Configuration management (ACM)*. At least, have unique a version identifier for each TOE release, so that users will know what they have. You gain more assurance if you have good automated tools to control your software, and have separate version identifiers for each piece (typical CM tools like CVS can do this, although CVS doesn’t record changes as atomic changes which is a weakness of it). The more that’s under configuration management, the better; don’t just control your code, but also control documentation, track all problem reports (especially security-related ones), and all development tools.

- *Delivery and operation (ADO)*. Your delivery mechanism should ideally let users detect unauthorized modifications to prevent someone else masquerading as the developer, and even better, prevent modification in the first place. You should provide documentation on how to securely install, generate, and start-up the TOE, possibly generating a log describing how the TOE was generated.

- *Development (ADV)*. These CC requirements deal with documentation describing the TOE implementation, and that they need to be consistent between each other (e.g., the information in the ST, functional specification, high-level design, low-level design, and code, as well as any models of the security policy).

- *Guidance documents (AGD)*. Users and administrators of your product will probably need some sort of guidance to help them use it correctly. It doesn’t need to be on paper; on-line help and "wizards" can help too. The guidance should include warnings about actions that may be a problem in a secure environemnt, and describe how to use the system securely.

- *Life-cycle support (ALC)*. This includes development security (securing the systems being used for development, including physical security), a flaw remediation process (to track and correct all security flaws), and selecting development tools wisely.

- *Tests (ATE)*. Simply testing can help, but remember that you need to test the security functions and not just general functions. You should check if something is set to permit, it’s permitted, and if it’s forbidden, it is no longer permitted. Of course, there may be clever ways to subvert this, which is what vulnerability assessment is all about (described next).

- *Vulnerability Assessment (AVA)*. Doing a vulnerability analysis is useful, where someone pretends to be an attacker and tries to find vulnerabilities in the product using the available information, including documentation (look for "don’t do X" statements and see if an attacker could exploit them) and publicly known past vulnerabilities of this or similar products. This book describes various ways of countering known vulnerabilities of previous products to problems such as replay attacks (where known-good information is stored and retransmitted), buffer overflow attacks, race conditions, and other issues that the rest of this book describes. The user and administrator guidance documents should be examined to ensure that misleading, unreasonable, or conflicting guidance is removed, and that secrity procedures for all modes of operation have been addressed. Specialized systems may need to worry about covert channels; read the CC if you wish to learn more about covert channels.

- *Maintenance of assurance (AMA)*. If you’re not going through a CC evaluation, you don’t need a formal AMA process, but all software undergoes change. What is your process to give all your users strong confidence that future changes to your software will not create new vulnerabilities? For example, you could establish a process where multiple people review any proposed changes.


