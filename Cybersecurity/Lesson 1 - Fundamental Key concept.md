**Corso/Professore:** Riccardo Lazzeretti, Sapienza Università di Roma.
## Fundamental Key Concepts

> [!warning] Rule of the Weakest Link The security of a system is equivalent to the security of its least secure component ("rule of the weakest link").

- **Process, not a product:** Security is not just a "product" (e.g., a firewall); it is rather a "process", which needs to be managed properly.
- **No absolute security:** Nothing is 100% secure. We must evaluate how much security is needed and at what cost.
- **No Security by Obscurity:** Security by obscurity never works.
- **Cryptography:** Cryptography is a powerful tool, but it is not enough on its own.
- **Human Factor:** Do not rely on users
## The CIA Triad 

According to the NIST Computer Security Handbook, computer security is defined as the protection afforded to an automated information system to preserve three main objectives for hardware, software, data, and telecommunications:

> [!abstract] Confidentiality Prevents unauthorized disclosure of information.
> 
> - **Data confidentiality:** Assures that private or confidential information is not made available to unauthorized individuals.
>     
> - **Privacy:** Assures that individuals control or influence what information related to them may be collected and stored and to whom it may be disclosed.
>     
> - **Task Loss:** Unauthorized disclosure of information and potential data exposure.
>     

> [!abstract] Integrity Prevents unauthorized modification or destruction of information.
> 
> - **Data integrity:** Assures that information and programs are changed only in a specified and authorized manner.
>     
> - **System integrity:** Assures that a system performs its intended function in an unimpaired manner, free from deliberate or inadvertent unauthorized manipulation.
>     
> - **Task Loss:** Unauthorized modification or destruction of information.
>     

> [!abstract] Availability Ensures timely and reliable access to information.
> 
> - Assures that systems work promptly and service is not denied to authorized users.
>     
> - **Task Loss:** Disruption of access to or use of information or an information system.
>     

## Auxiliary Security Properties

In addition to the CIA Triad, other fundamental security properties include:

- **Authentication:** The property of being genuine and being able to be verified and trusted.
- **Non-repudiation:** To prevent false denial of performed actions.
- **Authorisation:** "What Alice can do".
- **Auditing:** To securely record evidence of performed actions.
- **Attack-tolerance:** Ability to provide some degree of service after failures or attacks.
- **Disaster Recovery:** Ability to recover a safe state.
- **Others:** Key-recovery, key-escrow, Digital Forensics.
## Vulnerabilities, Threats, and Attacks

> [!danger] Security Terminology (RFC 4949) **Owners** impose **countermeasures** to reduce **vulnerabilities**, which would otherwise increase the **risk** to their **assets**. **Threat agents** give rise to **threats** that exploit those vulnerabilities.

### Threat Consequences

Threats lead to four primary consequences:
1. **Unauthorized Disclosure:** Exposure, Interception, Inference, Intrusion.
2. **Deception:** Masquerade (e.g., Man in the Middle), Falsification, Repudiation.
3. **Disruption:** Incapacitation, Corruption, Obstruction.
4. **Usurpation:** Misappropriation, Misuse.
### Types of Network Attacks

> [!example] Classification
> 
> - **Passive Attacks:** Include _eavesdropping_ and traffic analysis. Because they are hard to detect, the main goal is to **prevent** them.
>     
> - **Active Attacks:** Include _masquerade_, _replay_, data modification, and _Denial of Service (DoS)_. Because they are hard to prevent, the main goal is to **detect** them.
>     

## Case Studies

### Bots and Botnets (Mirai Case)

- A **Bot** (also known as a _Zombie_ or _drone_) is a program that secretly takes over another Internet-attached computer to launch attacks that are difficult to trace to the bot's creator.
- **Common uses:** Spreading new malware, distributed denial-of-service (DDoS) attacks, spamming, keylogging, sniffing traffic.
- **Mirai Malware:** A malware that turns networked devices running Linux (primarily consumer IoT devices like IP cameras and home routers) into remotely controlled bots.
- **The Dyn Attack (2016):** A massive DDoS attack targeting the DNS provider Dyn, launched from IoT devices running Mirai. Mirai operates in three stages: 1) Infect the device, 2) Protect itself, 3) Launch attack.
    

### BGP and Prefix Hijacking (Pakistan Telecom / YouTube Case)

- **BGP (Border Gateway Protocol)** is the Internet's routing protocol, but it has a major vulnerability: it does not verify if an Autonomous System (AS) is authorized to originate a prefix.
- **The Incident:** To block an offensive video, the Pakistan Telecommunication Authority ordered ISPs to block YouTube's IPs.
- **The Error:** Pakistan Telecom executed a _sub-prefix hijack_, announcing via BGP to the rest of the world that it was the legitimate destination for YouTube's IPs. This hijacked global traffic to Pakistan, making YouTube completely unreachable.
    

## Strategy and Design Principles

Computer security strategy is divided into three phases:
1. **Specification/Policy:** What is the security scheme supposed to do?
2. **Implementation/Mechanisms:** How does it do it? (Prevention, detection, response, recovery).
3. **Correctness/Assurance:** Does it really work? (Assurance, evaluation).
    
> [!success] Fundamental Security Design Principles Key principles for designing secure systems:
> 
> - Economy of mechanism
>     
> - Fail-safe defaults
>     
> - Complete mediation
>     
> - Open design
>     
> - Separation of privilege
>     
> - Least privilege
>     
> - Least common mechanism
>     
> - Psychological acceptability
>     
> - Isolation, Encapsulation, Modularity, Layering
>     
> - Least astonishment
>