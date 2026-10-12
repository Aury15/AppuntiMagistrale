# Email system and security challenges

### Email spoofing
>[!definitions]
>It consists in sending an email with a fake sender in order to steal confidential data or deliver malware to the victim. In general, this kind of attack doesn't directly break a system: its success depends on the victim's trust.
>**Email spoofing** is an attack in which the attacker **forges the apparent identity of the sender** of an email. The attacker manipulates the email headers so that the message appears to come from a trusted person or organization, even though it was not actually sent by that person.<br>

>[!example]
>![[Pasted image 20261008093814.png]]
>
>- **Envelope vs. Header Mismatch:** The actual recipient (`Delivered-To` via SMTP `RCPT TO` / `Bcc`) does not match the visible `To:` header (`polizia-cr@info.it`), indicating mass delivery with a decoy recipient.
>- **Fake Threading:** The artificial `In-Reply-To` header mimics an ongoing conversation thread to bypass heuristic spam filters and exploit user trust.
>- **Display Name Spoofing:** The sender display name (`Polizia criminale`) is impersonated while using a compromised institutional account (`alessandro.lazzaro@uniroma1.it`) to pass SPF/DKIM checks.
>- **`Reply-To` Hijacking:** Directs any reply away from the legitimate sender to the attacker's mailbox (`servicecepol@gmail.com`), breaking the communication flow to carry out the scam.

Most of these threats stem from the lack of adequate security guarantees in the standard email architecture and its protocols. In particular:
- No sender authentication means no way to trace the origin of an email
- No end-to-end encryption means no way to enforce content confidentialit

#### Sender authentication
When SMTP was designed, there was no authentication mechanism: anyone could connect to a server and send an email pretending to be any address. To address this problem, several cooperative standards emerged.

**Sender policy framework** (*SPF*): verifies if the sending server is authorized to send emails on behalf of the domain. <u>How it works</u>: the owner of the domain publishes a TXT type DNS record  containing the list of IP addresses or hosts authorized to send email on his behalf. When receiving an email, the recipient's mail server extracts the domain from the SMTP envelope address (MAIL FROM / RETURN-PATH) and queries the DNS. If the sender's IP is on the list, the check is passed; otherwise, it fails.
>[!example]
>Bob owns domain example.net. He also sometimes sends mail through his gmail account and contacted gmail's support to identify the correct SPF record for gmail. Since he often receives bounces about messages he didn't send, he decides to publish an SPF record in order to reduce the abuse of his domain in e-mail envelopes: `example.net TXT ""v = spf1 mx a:pluto.example.net include:aspmx.googlemail.com -all` :
> - *example.net TXT*: means that the rule is published in DNS as a txt record releted to the domain;
> - *v=spf1*: tag that identify the protocol version;
> - *mx*: mechanism that automatically authorize all all incoming mail server IP addresses defined by the MX records of the example.net domain. In other words: "The servers that receive mail for my domain are also authorized to send it."
> - *a:pluto.example.net*: mechanism that specifically authorizes the IP address corresponding to the A/AAAA record of the individual host (pluto).
> - *include:aspmx.googlemail.com*: since Bob also uses Google/GMail to send mail, he formally delegates Google's infrastructure;
> - -*all*: means: "Any server or IP attempting to send an email with MAIL FROM: ...@example.net that does NOT match one of the previous mechanisms (mx, pluto, or Google) should be considered UNauthorized and rejected."
> 
>then check spf record `dig +noall +answer uniroma1.it txt` It is the Linux command line command to query DNS and examine real SPF records in the field.
>![[Pasted image 20261008100014.png]]

**Limits of SPF**
1. *Email forwarding*: when an email is forwarded, the SPF check can fail. The forwarder’s IP address may not be included in the original sender’s SPF record;
2. *Shared or third-party email services*: Organizations often use third-party services (like CRM platforms, marketing tools, or cloud services) to send emails on their behalf. These services may not be listed in the domain’s SPF record.
3. *Mailing lists*: Emails sent through mailing lists often pass through multiple servers before reaching recipients. Mailing list software sometimes modifies the email headers, including the sender’s information, which can break SPF validation.
4. *SPF record size limitations*: The size limit for an SPF record is 450 characters minus the length of the domain name and the length of any other TXT record value. This is the upper exclusive limit recommended in RFC 7208, the document that specifies SPF.
5. *No protection for email body*: SPF only authenticates the sending server’s IP address and does not protect the email's content or prevent modification of the message body or attachments.
6. *No full sender verification*: SPF only verifies the sending server’s IP address against the envelope sender (the "MAIL FROM" address), not the From: header that the user sees.
7. *Misconfiguration*: Incorrectly configured SPF records, such as syntax errors or forgetting to include authorized sending IPs, can cause legitimate emails to fail SPF checks.
8. *Complex mail routing*: In some scenarios with complex email routing (e.g., email relays, hybrid systems), SPF checks can fail because the originating IP doesn’t match the IP specified in the SPF record.
9. *Outbound-Only Security*: SPF is mainly used to authenticate outbound email servers, but it doesn’t offer any protection or guarantee about the inbound email a domain receives.

**DomainKeys identified mail** (*DKIM*): Provides a cryptographic signature to ensure the email's integrity and authenticity. Message recipients (or agents acting in their behalf) can verify the signature by querying the signer's domain to retrieve the public key. The DKIM guarantees email content integrity, domain authentication, non repudation. <u>How it works</u>: the sender's mail server computes a hash of the message body and specific headers. It signs this digest with its private key and appends the result to the DKIM-signature header. The sender publishes the corresponding public key in a DNS TXT record that can be found using a selector (can indicate the names of the office locations, the date or the individual user) combined with the domain. The destination server receives the email, downloads the public key from the DNS of the domain indicated in the d= tag, decrypts the signature, and recalculates the hashes. If they match, it is mathematically certain that the body and signed headers have not been tampered with. So, this signature contains information like:
- The hashing algorithm used;
- The domain that is taking responsibility for the email;
- The email parts that were signed;
- The signature itself (base64 encoded value).
###### DKIM VS SPF
SPF authenticates the sending server's IP address against the domain’s SPF
record. DKIM authenticates the email content by verifying the signature against a public
key stored in DNS.
**Limits of DKIM**
1. DKIM doesn’t authenticate the visible From address. It only verifies the email's
authenticity from the domain that signed the message;
2. Email forwarding can break DKIM if the forwarding server alters the content;
3. It doesn’t provide encryption or privacy—only authenticity and integrity.

>[!important]
>**Canonization**: intermediate mail servers often reformat whitespace or line breaks. To prevent trivial changes from invalidating the signature, DKIM normalizes the text before calculating the hash (with simple or relaxed mode).
>**Selectors** (s=): allow a single domain to manage multiple key pairs simultaneously (for example, for different offices, to rotate them over time, or to delegate external services like Google Workspace or Mailchimp without giving away the master key).

>[!example]
>![[Pasted image 20261008103926.png]]

**Domain-based message authentication, reporting & conformance** (*DMARC*): Enforces policies based on SPF and DKIM and sends reports back to domain owners on authentication failures. DMARC allows an organization to publish a policy that defines its email authentication practices and provides instructions to receiving mail servers for how to enforce them. Specifically, DMARC establishes a method for a domain owner to: 
- Publish its email authentication practices;
- State what actions should be taken on mail that fails authentication checks;
- Enable reporting of these actions taken on mail claiming to be from its domain.
- **Puts together SPF and DKIM.** 
How does DMARC works?
1. *Domain Alignment*: This is the fundamental requirement. For DMARC to pass, it is not enough for SPF or DKIM to be valid in theory; the verified domain must match the domain shown in the From: field.  
	1. For *SPF*: the domain in Return-Path must match the domain in From:. 
	2. For *DKIM*: the signing domain (d=) must match the domain in From:.
2. *Policy enforcement* (p=): The domain owner instructs recipients on what to do in the event of failure:
	1. *none*: monitoring only, the email is delivered normally.
	2. *quarantine*: accept the message but move it to spam or isolate it.
	3. *reject*: reject the message at the SMTP gateway.
3. *Reporting*: Receiving servers periodically send reports to the sender
**Limits of DMARC**
If an organization uses p=reject, legitimate emails sent via the mailing list are at risk of being blocked: the mailing list rewrites the IP (breaking SPF) and adds the tag to the subject or footer (breaking DKIM).

**Authenticated Receiver Chain** (*ARC*): allows for maintaining the validity of DMARC policies across intermediaries. ARC helps preserve the authentication status of an email that passes through multiple intermediaries or forwarding services.
- *Preserves Authentication*: ARC ensures that if an email passes SPF, DKIM, and DMARC checks at the original source, this authenticated status can be carried forward to subsequent servers or recipients.
- *Accountability for Intermediaries*: Each intermediary in the email flow adds its own ARC authentication results, creating a verifiable chain of who has handled the message and whether they altered the original email.
- *Improved Email Deliverability*: ARC can improve deliverability for emails sent through mailing lists, email forwarding services, or resending platforms by helping them pass DMARC checks that would otherwise fail.

>[!important]
>**ARC does not replace DMARC** but works alongside it. ARC is concerned with preserving the email’s authentication chain.

The ARC idea: when there are one or more intermediaries, each of them writes a declaration inside the mail (the result of the checks it performed when it received it), signed with its private key. So at the end the mail carries a chain of declarations. **ARC (Authenticated Received Chain)** is an email authentication mechanism designed to preserve the **authentication history of an email** as it passes through multiple intermediate mail servers. The main problem ARC addresses is that a message can be **legitimate and correctly authenticated when it is first received**, but its authentication can later fail because the message is forwarded or modified by an intermediary. ARC allows intermediate servers to record and **cryptographically authenticate** the results of the authentication checks they performed.
> **Key idea:** ARC does not directly authenticate the original sender. It preserves the authentication results obtained by previous mail servers.

Email authentication mechanisms such as **SPF, DKIM, and DMARC** can encounter problems when messages are forwarded.
Consider:
```
Sender → Mail Server A → Forwarder → Recipient
```
The message may initially pass authentication:
```
SPF   = PASS
DKIM  = PASS
DMARC = PASS
```
However, the forwarding server may modify the message. For example, a mailing list may add information to the subject:
```
Original:
Subject: Meeting tomorrow

After mailing list:
Subject: [Mailing List] Meeting tomorrow
```
This modification can invalidate the original DKIM signature. At the final recipient, the results may therefore become:
```
SPF   = FAIL
DKIM  = FAIL
DMARC = FAIL
```
The recipient might incorrectly conclude that the message is malicious.
ARC solves this problem by preserving the authentication results obtained **before the message was modified or forwarded**.
>[!example] ## Example: Email Forwarding
Suppose Alice sends an email: Alice -> Gmail -> Mailing list -> Bob
Gmail receives Alice's message and performs authentication: 
SPF   = PASS
DKIM  = PASS
DMARC = PASS
Gmail can create an ARC record containing these results. Conceptually:
ARC-Authentication-Results:
spf=pass
dkim=pass
dmarc=pass
The mailing list receives the message and modifies it.
For example: Subject: [ML] Meeting tomorrow
The original DKIM signature may now be invalid. Therefore:
DKIM = FAIL
The mailing list can nevertheless preserve the authentication history through ARC.
Bob may see:
SPF   = FAIL
DKIM  = FAIL
DMARC = FAIL
ARC   = PASS
ARC tells Bob that a previous trusted server had already authenticated the message successfully.
Bob can therefore take the previous authentication results into account when deciding whether to accept the message.

ARC uses three main types of headers:
1. *ARC-Authentication-Results* (AAR): The **ARC-Authentication-Results** header records the authentication results obtained by a server when it received the message.
2. *ARC-Message-Signature* (AMS): The **ARC-Message-Signature** is a cryptographic signature associated with the message and the relevant ARC information.
3. *ARC-Seal* (AS): The **ARC-Seal** protects the integrity of the ARC chain itself.
<u>Together they allow a receiving server to determine:</u>
- What authentication results were obtained?
- Was the relevant message information signed?
- Has the ARC chain remained intact?
**Limits of ARC**
ARC does not replace SPF, DKIM or DMARC $\rightarrow$ it works alongside them. ARC provides additional information that can help when the original authentication results are no longer directly valid.
Steps to check an ARC chain: 
- *Verify the ARC-Seal*: — Ensure the cryptographic signature in the seal matches the domain and selector used by the intermediate server.
- *Verify the ARC-Message-Signature*: — Check that the signature in the ARC-Message-Signature correctly matches the email’s original content and headers.
- *Check Authentication Results*: — Review the ARC-Authentication-Results to see if SPF, DKIM, and other checks passed for the original email.
- *Evaluate the Chain* — Continue verifying the chain of ARC headers through all the intermediaries. Each hop adds another ARC-Seal, which must be validated to maintain the integrity of the chain.

### Pretty good privacy (PGP)
**Pretty Good Privacy** is a standard created by Phil Zimmermann in 1991. 
SPF, DKIM, DMARC and ARC are managed by the servers and authenticate only the domain, but none of them solves the second problem seen at the beginning: there is no end-to-end encryption, so the content of the email can be read by anyone who intercepts it.<br>PGP works directly between the users: the sender's client signs and encrypts the email, and only the receiver's client can decrypt it. <u>How it works?</u>
- **Authentication / Digital signature**:
	- The sender computes the hash of the message $M$ (SHA-1).
	- Encrypt the hash with his private key ($PR_a$), creating the signature.
	- The block is compressed (via ZIP) before being sent or encrypted.
	- The recipient unpacks, decrypts the hash with the sender's public key ($PU_a$), recalculates the hash on $M$, and compares.
	- ![[Pasted image 20261008181800.png]]
- **Confidentiality**:
	- Sender generates a random symmetric session key ($K_s$, 128-bit).
	- Compresses message $M$ and encrypts it symmetrically with $K_s$.
	- Encrypt the key $K_s$ with the recipient's public key ($PU_b$).
	- The recipient uses their private key ($PR_b$) to retrieve $K_s$ and then decrypt the compressed text.
	- ![[Pasted image 20261008181958.png]]
- **Confidentiality + Authentication**:
	- Can use both services on same message. So create a signature & attach to message, encrypt both message & signature, attach RSA/EL-Gmail encrypted session key.
	- ![[Pasted image 20261008182113.png]]
#### Compression & Key management
But if PGP uses private and public key, <u>how we manage all these keys? How we can know if the public key actually belongs to that person? </u>PGP uses a **public-key ring**: it is a structure that contains the public keys of the people we want to communicate with. 
>[!example] Public Key Ring
>Public Key Ring
│
├── Alice's public key
├── Bob's public key
├── Carol's public key
└── David's public key
Alice
  |
  | looks up Bob's public key
  v
Public Key Ring
  |
  v
Bob's public key
Alice uses Bob's public key to cipher the session key

Also exist the **private-key ring** that contains the public keys of the digital signature or for the decryption. 
<u>How we can identify a key?</u> PGP uses a **Key ID** for every key. Also exist the key fingerprint that is like a digital fingerprint of the public key. The key fingerprint is calculated by using an hash function to the key. 

