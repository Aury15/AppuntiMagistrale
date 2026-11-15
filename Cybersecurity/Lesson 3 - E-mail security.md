## E-mail security

> [!DEFINITIONS]  E-mail
> **E-mail** is a method of exchanging digital messages from an author
to one or more recipients, operating across the Internet/intranet. Its the oldest service available on the internet.

Modern e-mail systems are based on a store-and-forward. The model are like that: e-mail servers accept, forward, store and deliver messages. This architecture has many components, divided in three different groups:
1. Message author;
2. Message handling system;
3. Message recipient. 
![[Pasted image 20261005094240.png|701]]

The architecture relies on specific agents defined under RFC 5598:
- **Message user agent** (<u>MUA</u>): is the mail client, where the sender writes the e-mail. The MUA formats and builds the e-mail, then forwards the message to the MSA (using Sp, a variant of SMTP);
- **Mail submission agent** (<u>MSA</u>): receives the message from the MUSA via the submission protocol, validating formatting before handing it over;
- **Mail transfer agent** (MTA): the backbone relay engine running both client and server sides of SMTP to route emails hop by hop across intermediate network;
- **Mail delivery agent** (MDA): receives messages from the final inbound MTA and places them into the recipient's storage mailbox.

#### The workflow:
1. A sender composes a message via MUA and transmits it to the local MSA/MTA via SMTP;
2. The sending MTA queries DNS for the destination domain’s **MX (Mail Exchanger)** records;
3. The sender connects to the remote MTA over SMTP to deliver the envelope and message payload;
4. The destination MUA retrieves the message from the mailbox store using pull protocols: **POP3** (download/offline usage) or **IMAP4** (server-synchronized, multi-device management).
### SMTP
The protocol responsible for transmitting e-mail across IP network is **SMTP** (simple mail transfer protocol). An SMTP message is made of two separated parts that travel together: the envelope and the body (the actual message).  
>[!definitions] 
>**user@domain** the part before @ is the local part, the part after @ is the domain

The **envelope** contains the delivery parameters and is used by the servers to understand where the message must be **delivered**. 

#### Internet message format (IMF)
The internet message format is defined by RFC 5322 (use MIME: multipurpose internet mail extensions so uses no-ASCII characters and image etc). An internet e-mail messages consist of two major sextions:
1. **Header**: structured intro fields such as From, To, CC, Subject, Date, Message-ID. Each message has exactly one header, which is structured into fields. Each field has a name and a value. Field names and values are restricted to 7-bit ASCII characters. Non-ASCII values may be represented using MIME encoded words.
2. **Body**: the content.
The header is separated from the body by a blank line.
>[!example] 
>MAIL FROM:<john@doe.com>          ← envelope
RCPT TO:<boss@workplace.com>      ← envelope
DATA
From: "John Doe" <john@doe.com>   ← header
To: Boss Workplace <boss@...>     ← header
Subject: Sick note                ← header
Hello boss, ...                   ← body

Each message is identified through two kinds of IDs:
1. **Message-ID**: associated to the message content and is globally unique. Its format is similar to that of a mailbox, with two parts separated by @. The MUA or MSA assign the message ID. *Example*: <20241001102233.12345@example.com>. 
2. **ENVID**: stands for envelope, is assigned to the enveloper by the first MTA. Used for message-tracking purposes. 

To access their mail box accounts, MUAs usally uses:
- The **Post Office Protocol** (POP) for mail downloading and offline usage;
- The **internet message access protocol** (IMAP) for online mail reading.

>[!important] the envelope and the header are independent. The `To:` field in the header is not necessarily where the message is delivered (the real list is in RCPT TO), and the `From:` field does not have to be the real sender. This is the base of many attacks (spoofing).
#### Operation overview
1. Alice composes a message using her MUA, she enters the e-mail address of her correspondent and hits the send button;
2. The MUA builds the message in email format and uses a variant of SMTP to send the message to the local MSA;
3. The MSA looks at the destination address provided in the SMTP protocol and finds the domain;
4. The DNS server for the b.org domain responds with any Mx record listing the mail exchange servers for that domain, in this case a MTA server run by Bob's ISP.
5. smtp.a.org sends the message to mx.b.org using SMTP;
6. Bob presses the get mail button in his MUA, which picks up the message using either the POP3 or the IMAP4.
![[Pasted image 20261007185255.png|658]]

### ESMTP
ESMPT its an extension of the SMTP protocol (RFC 5321) and it is backward-compatible with it: a client that uses plain SMTP is still served. The greeting command for ESMTP is **EHLO** (not **HELO** like the SMTP) which gets a response listing the supported commands:
Main commands/extensions:
- **STARTTLS**: in SMTP all the text of the message travels in clear; with this command the connection is switched to an **encrypted (TLS) connection**;
- **AUTH**: the client must authenticate to the server before sending, so only authorized users can send mail through it (this helps against spam);
- **8BITMIME**: allows 8-bit data transmission (plain SMTP only allows 7-bit ASCII);
- **SIZE**: the client declares the size of the message, so the server can refuse it if it is too big;
- **CHUNKING**: the message can be sent in chunks;
- **DSN**: Delivery Status Notification;
- **SMTPUTF8**: allows UTF-8 in mailbox names and header fields;

### MIME: Multipurpose internet mail extensions
MIME is an internet standard that extends the format of email to support:
- Text in character sets other than ASCII;
- Non-text attachments;
- Message bodies with multiple parts;
- Header information in non-ASCII character sets.
All human-written internet emails are transmitted via SMTP in MIME format. MIME specifies how messages must be formatted so they can be exchanged between different email systems. MIME messages can contain text, images, audio, video, spreadsheets, word documents etc. 
**ASCII scheme**:
![[Pasted image 20261007191838.png]]

**MIME Headers**:
![[Pasted image 20261007192033.png|708]]

![[Pasted image 20261007192416.png]]

#### No text content
Non-textual content is encoded in ASCII for transmission and decoded back to its original format for display upon receipt. MIME uses a base 64 encoding $\rightarrow$
**Scheme**:
▪ take 3 bytes of data, put into a 24 bit buffer
▪ extract 4 six-bits values
▪ use each value as an index into:
ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/
▪ this yields 4 ASCII characters
▪ use zero, one or two = symbols for padding (at the end)
![[Pasted image 20261007193311.png|586]]

#### Quoted-printable encoding
**Quoted-Printable Encoding** is a binary-to-text encoding scheme defined in MIME (RFC 2045) designed to transmit arbitrary data across email transfer channels that only reliably support 7-bit ASCII. It is particularly efficient for content that is predominantly ASCII text with occasional non-ASCII or special characters (such as accented characters in Western European languages).
- **Standard ASCII Characters:** Standard printable ASCII characters with decimal values from 33 to 126 (excluding decimal 61, the equals sign `=`) are left untouched and transmitted as-is.
- **Non-ASCII and Special Bytes:** Any byte value outside the standard printable range—as well as the literal `=` character itself—is escaped and encoded using three characters: an equals sign (`=`) followed by two hexadecimal digits (0–9, A–F) representing the byte’s numeric value.
- **Whitespace Handling:** Space (ASCII 32) and horizontal tab (ASCII 9) may remain unencoded unless they appear at the very end of a line, where they must be encoded (e.g., `=20` for space) to prevent mail transfer agents from stripping trailing whitespace.

>[!VS]
>**Base64 vs quoted-printable**: quoted-printable is better for text with few non-ASCII characters (small and readable); base64 is better for binary data like images or attachments (with quoted-printable almost every byte would become 3 characters).

### Multipart subtypes
In the original email standard (RFC 822/5322), the body of a message was intended to be a single, continuous block of plain ASCII text. Multipart emails (introduced with the MIME standard in RFCs 2045-2049) were designed to overcome this limitation, allowing a single message to contain multiple distinct parts, each with its own data type, format, or encoding. When a message is multipart, the main header of the emails defines two key elements:
1. Content-type : multipart;
2. boundary: a text string that will act as a visual delimiter between one part and another.
In the body each type has a sub-header. The parts are separated by a **boundary** string, defined as a parameter of the Content-Type:
- each part starts with `--` + boundary
- the end of the last part is marked by `--` + boundary + `--`
Main subtypes:
- **mixed** - the parts have different Content-Types (EX. body text + attachment); their order matters;
- **parallel** - like mixed, but the order of the parts does not matter;
- **digest** - to send multiple text messages together; similar to mixed, but the default type of each part is `message/rfc822`;
- **alternative** - each part is a different version of the same content (EX. text/plain + text/html); the client shows the best version it can display;
- **message** type (not multipart, but used together with it): `message/rfc822` contains a whole e-mail message with its headers (EX. a forwarded mail).

>[!example]
>From: Some One <someone@example.com>
MIME-Version: 1.0
Content-Type: multipart/mixed;
boundary="XXXXboundary text"
This is a multipart message in MIME format.
--XXXXboundary text
Content-Type: text/plain
this is the body text
--XXXXboundary text
Content-Type: text/plain;
Content-Disposition: attachment;
filename="test.txt"
this is the attachment text
--XXXXboundary text--


