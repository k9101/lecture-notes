# Lecture 1 - May 2, 2018

## What is security?

1. Confidentiality: limit access to authorized parties
2. Integrity: A user recieves the "correct" data
  - Data was not modified by unauthorized parties
3. Availability: Systems are available when you want them
  - apps, systems, networks, etc.

A system is "usually" secure if the have these 3 things...
- There are somethings that you can't control (a power outage, which then effects availability)
- **The least important of the 3 for security**: Availability
  - Sometimes you must not compromise on 1 or 2, and this has implications on availability
  - Need to weigh the concerns for the system your building, what's important?
  - Example: Smart locks, communicates to the user's devices via bluetooth. If the signal was under heavy load, the door could be unlocked. Making trade offs in confidentiality for availability.

A secure system is reliable:
1. keeps your personal data confidential
  - Confidentiality
2. allow only authorized access / modifications to resources
  - This is integrity
3. Any produced results are correct
  - Integrity
4. Gie you correct and meaningful results whenever you want them
  - Integrity and Availability

### Privacy

"Informational Self-Determination"
- You get to control information about you
- "Control"
  - who can see it
  - who can use it
  - what they can use it for
  - who can they give it to
- Facebook + CA: Took advantage of `who can use the data` and `what they can use it for`

#### PIPEDA
- Personal Information Protection Electronic Documents Act
- Ensure the private sectors respect user's data
  - purpose of collection
  - consent
  - limit collection, use, disclosure, retention
  - provide safeguards
  - be accurate
  - Provide users information about data breaches
  - Provide recourse for breaches

### Security vs. Privacy
- Sometimes considered as mutually exclusive
  - CCTV cameras / Cell Phone Carriers: They claim it provides security, but violates your privacy
  - Airports
  - NSA:
- Do you have to make the trade-off (can you keep both)

### Adversaries

Bad actors that try to break systems

Types of Adversaries
- Murphy:
  - Based on Murphy's Law (Anything that can go wrong will)
  - Example: Private keys, they should keep private. Developers (Uber) committed private keys to a git repo
- Amateurs
  - Not much technical skill
  - They have access to some system, obtain access to other people's data
- Script Kiddies
  - Some technical skill
  - Get some source code, try to run some attack
  - Example: Attempted HeartBleed on CRA
- Cracker
  - "Traditional Hacker"
  - Explicitly try to break into a system
- Organized Crime
  - Collective group that have some agenda
- Government "Cyberwarriors"
  - NSA
- Terrorists

#### Which of these is the most serious threat?
- Governments: There is tyipcally some form of accountability
- Organized Crime: Not really any accountability

The threat depends on where you are.
- Working at a university (student), governments probably not a threat
- Working in a government: other governments, political actors may be

## Terminology

### Assets

Things we want to protect against attackers
- Hardware
- Software
- Data

### Vulnerabilities
- Weakness in a system that can be exploited to cause harm to someone

### Threats
- The outcome of some attack, the actual harm
- Example: A user's files get leaked
- Types
  1. Interception
  2. Interruption: Interupt a real user's service
  3. Modification
  4. Fabrication

#### Threat Models
- Set of threats we are defending against
- Whom do we want to prevent from doing what
- Some public facing companies require security certifications to operate
  -

### Attack
- An action, exploit a vulnerability, execute a threat
- Example: Tell a server you are a different user in an attempt to read / access another user's file

### Control / Defense
- REmoving / Reducing a vulnerability
- control a *vulnerability* to prevent an *attack*, and *defend* against a *threat*

#### Methods of Defence
We will want to use many things to defend against the same threat (**Depth of Defense**)

- **Prevent** the attack
  - Immobilizer
- **Deter it**: make an attack harder
  - Store you car in a secure parking facility
- **Deflect it**: make yourself less attractive
  - Have a car alarm example, hide valuables.
  - Trying to steal my car will cause you problems
- **Detect**: Notice an attack is occuring
  - Car alarms, OnStar
- **Recover from it**: mitigate the effects of an attack
  - Call your Insurance Company

Above example: **Threat: your car may get stolen**

#### How secure should we make it?

**Principal of Easiest Penetration**
- A system is only as strong as it's weakest link
- secure a system by thinking like an attacker
- Use Depth of defense: multiple defenses

**Principal of Adequate Protection**
- Economic principal
- It doesn't make sense to spend a Million dollars to protect a system that can only cause a thousand dollars in damages.

#### Defense of computer systems
- Goal is to protect our assets
- Many ways
  - crypto
    - Make data unreadable by unauthorized parties
    - Digital signatures to provide data integrity, authentication
  - software controls
    - Passwords , pin codes, gesture codes
    - Separate user accounts (operating systems)
    - Static Analysis of source code: give insights into potential threats as the system is being developed.
  - hardware controls
    - Typically a separate hardware device, to protect some system
    - Fingerprint readers
    - physical tokens
  - Physical Controls (example for a server room)
    - Physical access to hardware
    - locks, guards
    - off-site backups
    - protect datacentres from natural disasters
  - Policies and Procedures (the human aspect)
    - non-technical means to protect against some attacks
    - Rules about choosing passwords
    - Training employees/users in best security practices
      - incentives to follow them
