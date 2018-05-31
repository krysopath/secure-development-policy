# rationale

We have a fundamental need for security. It follows, that we take responsibility of at least our own needs. For larger groups of humans new requirements appear. And we take care of this via an open dialog on security.

Info security is a fundamental scientific, social and engineering challenge, that interacts strongly with many other common challenges, which humans tend to have in their life. 

One of the bigger misunderstanding about people selling computer to customers is, that they there is actually one computer being sold. In truth, a computer consists of many of computers which run software interdependently. Each executes offloaded work from the cpu. All are `fast==trusted==insecure` and have to be taken into acount.

Xzibits Iron Law of Computer Architecture:
> Yo dawg, I heard you like computers, so I put a computer in your computer, so you can compute while you compute


## reasons for security incidents
### technical
#### conceptual problems
1. Not identifying security requirements up frona
2. Creating conceptual designs that have logic errors
3. Using poor coding practices that introduce technical vulnerabilities
4. Deploying the software improperly
5. Introducing flaws during maintenance or updating

#### environmental problems
1. The software and its associated information
2. The operating systems of the associated servers
3. The backend database
4. Other applications in a shared environment
5. The user's system
6. Other software that the user interacts with
7. Networking infrastucture

### anthropolgical
#### cognition

Human beings generally are cognitive misers, which means they follow routines and try to find easy patterns, that they can use to fallback into some `default` behaviour. We do that since we need all the cognitive resources, that we can muster to complete the current conscious task. All the other details are being intentionally flushed. This enables us to focus on important details.

However, we can easily become blind towards details, that are not directly connected to the current center of attention. That is the reason why it is so easy to social-engineer people, by providing something inconspiciously `trusted` looking. (see spear phishing, - the most successful attack versus organisations)

When we are feeling familier with the environment and the job or problem, we tend to anchor fast, and are more likely to neglect unrelated details that do not fit the picture.


#### automaticity

Additionally, we build scripts for everything: I am talking about behavioural scripts, that are working exactly like those scripts we write. They are being defined by experiences of common events and the memory how it was easiest to solve. 

- manners
- waking up routine
- most social interactions
- learned workflows

In all systems I can think of, automaticity can be abused. 

- delivering malware via build pipelines
- social engineer someone into helping you


#### suspicion

Getting supicious about some weird event is easier, when assuming less about the situation. Getting suspicious can save networks!

Getting suspicious depends strongly on the ability to audit and introspect. 

