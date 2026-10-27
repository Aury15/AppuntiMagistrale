# Random Bit Generation and Stream Ciphers


## Introduction to Random Numbers

>[!QUOTE]
>"The generation of random numbers is too important to be left to chance"

Random numbers are fundamental to cryptography. They are used whenever a system must create values that an attacker should not be able to guess, such as cryptographic
keys, session keys, nonces, salts, and keystreams.


##### Why cryptography needs random numbers
Suppose Alice and Bob use a symmetric encryption scheme and periodically establish new session keys. If an adversary has observed previous session keys, the next key should still be extremely difficult to predict. More formally, the probability that an attacker correctly predicts the next session key should be negligible. A random-number generator used for cryptography must therefore do more than produce values that look irregular. It must protect against an adversary who knows the algorithm, has observed previous outputs, and may know some information about the system.

> [!important] Statistical appearance is not enough A sequence can
> pass statistical tests and still be insecure if an attacker can
> predict its next value. Cryptographic randomness requires both
> suitable statistical behaviour and computational unpredictability.

High-quality cryptographic random generation can be expensive because the system must collect entropy, protect internal state, and use algorithms designed to resist prediction.

### Two Main Ways to Generate Randomness

### 1. True or physical random-number generation
A **random-number generator** (**RNG**) can measure a physical phenomenon expected to contain randomness. Examples include timing variations, electrical noise, and other environmental events. The measurement process may be biased, so processing can be needed to reduce that bias. The resulting randomness comes from a physical source rather than being completely determined by a known initial value.

### 2. Pseudorandom generation
A **pseudorandom number generator** (**PRNG**) uses a deterministic algorithm to expand an initial value called a **seed** (or key). The seed may itself have been collected from a physical entropy source. Given the same seed and the same algorithm, a deterministic generator produces the same sequence every time.
The objective is to produce a long sequence that looks random and, for cryptographic use, is computationally difficult to predict.

> [!definition] Seed
>  A **seed** is the initial value supplied to a deterministic pseudorandom generator. It determines the generated sequence. If the seed has too little entropy or is exposed to an attacker, the output may become predictable even if the generator algorithm is otherwise well designed.

##### Important clarifications
-   A **deterministic computation** cannot create new physical randomness
    from nothing. Its outputs are determined by its inputs and internal
    state.
-   **Distinguishing a well-designed pseudorandom sequence** from a truly
    random sequence can be very difficult when only the outputs are
    available.
-   **Pseudorandom generators** can replace physical random sources in many
    applications, provided they are appropriate for the task.
-   **Statistical analysis** is useful for detecting weaknesses, but passing
    statistical tests alone does not prove cryptographic security.

## Entropy Collection and Initialization

A common design is to collect information from system activity and
external sources, combine it, and use it to initialize a PRNG. Once
initialized, the generator expands the seed into a longer sequence.

Possible sources mentioned in the lecture include:

-   System and network data.
-   The system clock.
-   Free disk space and the number of files on disk.
-   Operating-system information, such as I/O queues and buffer states.
-   User activity, including window information.
-   Packet inter-arrival times.
-   Keyboard and mouse timing.

These sources do not necessarily contain much unpredictable information.
An attacker may know or estimate many of their values. Their usefulness
depends on how much uncertainty they contribute, not simply on how many
different measurements are collected.

### How many random bits are available?

The number of possible values of a source is not the same as the amount
of entropy it contributes. For example, a clock may have a fine-grained
timestamp, but an attacker may know the approximate time. If only the
low-order milliseconds are uncertain, the effective uncertainty is much
smaller than the apparent size of the timestamp.

The lecture gives the example that milliseconds may provide around 10
bits of randomness in a particular setting, whereas the day, hour, and
minute are often easy to guess.

Mixing several sources with cryptographic functions can be advantageous,
because it can combine uncertainty from different inputs. However,
mixing predictable values does not magically make them unpredictable. A
secure design must account for the quality of the entropy sources and
the construction used to combine them.

> [!important] Entropy is about uncertainty to an attacker A value can
> be large, complex-looking, or represented by many bits without
> containing much actual unpredictability. Security depends on how many
> possibilities remain plausible to an attacker.

## Common Mistakes in Random-Number Generation

### Using a small seed

A seed of 16 bits has only

\[ 2\^{16}=65,536 \]

possible values. An attacker can potentially enumerate every candidate,
generate the corresponding output sequence, and compare it with observed
output.

The weakness is not necessarily the length of the output sequence. A
generator can produce millions of bits from a seed with only 16 bits of
uncertainty, but the attacker still has only 65,536 possible starting
points to test.

### Using the current time

Time is often a poor seed when an attacker can estimate when the value
was generated. The lecture considers a clock with a granularity of 10
milliseconds. If the attacker knows the approximate hour, the uncertain
part may be only the minute, second, and hundredth of a second:

\[ 60 `\times 60`{=tex} `\times 100`{=tex} = 360,000 \]

possible choices. This is a relatively small search space for a modern
attacker.

The general lesson is that a timestamp is not a strong secret merely
because it is expressed as a long integer.

### Revealing the seed indirectly

A seed does not have to be printed explicitly to be compromised. The
lecture describes an implementation that selected a per-message
encryption key using the time of day, but also included that time in the
message's unencrypted header. The same information intended to provide
unpredictability was therefore disclosed to the attacker.

> \[!example\] Predictable seed Imagine that a generator uses the
> current time as its seed and sends that timestamp in a public protocol
> header. An attacker can recover the seed from the header and reproduce
> the generated sequence. The generator may be deterministic and
> implemented correctly, yet its use is insecure because the seed is not
> secret or unpredictable.

## Case Study: Netscape 1.1 Seed and Key Generation (1996)

The slides present historical source code illustrating how a seemingly
complicated procedure can still produce a weak cryptographic seed.

The code uses the time of day, process identifiers, and a function named
`mklcpr`, then combines values using MD5:

``` c
/* Netscape 1.1 seeding & key generation (1996) */

global variable seed; /* not cryptographically significant */

RNG_CreateContext()
    (seconds, microseconds) = time of day;
    /* Time elapsed since 1970 */
    pid = process ID;
    ppid = parent process ID;
    a = mklcpr(microseconds);
    b = mklcpr(pid + seconds + (ppid << 12));
    seed = MD5(a, b);

mklcpr(x)
    return ((0xDEECE66D * x + 0x2BBB62DC) >> 1);

RNG_GenerateRandomBytes()
    x = MD5(seed);
    seed = seed + 1;
    return x;
```

The essential structure is:

1.  Collect values such as seconds, microseconds, the process ID
    (`pid`), and the parent process ID (`ppid`).
2.  Transform some of those values with a simple arithmetic function.
3.  combine the results using MD5 to obtain an initial seed.
4.  Generate output by hashing the seed and incrementing the state.

Hashing does not compensate for a seed that has too little uncertainty.
If the possible input values are drawn from a small set, an attacker may
enumerate them and calculate the same hashes.

### Assessing the randomness of Netscape 1.1

The slides explain that an attacker without an account on the affected
UNIX machine might not initially know `pid` and `ppid`. Nevertheless,
these values were constrained and could sometimes be estimated.

-   On many UNIX systems, `pid` and `ppid` were 15-bit quantities.
-   The expression `pid + (ppid << 12)` has at most 27 bits of range in
    the analysis presented by the lecture, rather than 30 independent
    bits.
-   If the seconds value is known, the value used to calculate `a` has
    about 20 unknown bits, while `b` has at most 27 unknown bits.
-   This leaves at most 47 bits of uncertainty in the secret key under
    those assumptions.
-   Additional operating-system behaviour can make the process
    identifiers easier to infer.

For example, `ppid` is often 1 when a user starts Netscape from a
graphical desktop menu. Otherwise, it may be close to the process ID.

The lecture also describes how outgoing mail software such as `sendmail`
could expose information about process identifiers through message IDs.
Sending email to an invalid user could cause a bounce message containing
a Message-ID generated using a process ID. If Netscape had started
recently and the machine was not heavily loaded, this could provide a
useful estimate of Netscape's process ID.

> \[!important\] Why this example matters Applying a cryptographic hash
> to predictable inputs does not create entropy. Security depends on the
> uncertainty of the seed and on whether the generator protects its
> state---not on whether the code contains a hash function or several
> arithmetic operations.

## The Debian Linux Random-Number Bug

The lecture describes a serious OpenSSL random-number-generation
vulnerability affecting Debian and related distributions, announced in
2008 and originating in a code change made in 2006.

A line was removed from `md_rand.c`:

``` c
MD_Update(&m, buf, j); /* purify complains */
```

The removal was motivated by warnings from debugging tools such as
Valgrind and Purify about the use of uninitialized data in software
linked with OpenSSL.

However, the line had contributed data to the random-generation process.
After its removal, the random values depended essentially on the current
process ID rather than on the intended mixture of random data.

On Linux systems where the default maximum process ID was 32,768, the
number of possible process-ID-based seed values was very small. This
made generated keys and other values vulnerable to enumeration.

The important distinction is between fixing a warning and preserving a
security property. Uninitialized memory should not be used carelessly,
but removing a source of input from a cryptographic generator without
replacing its entropy contribution can destroy security.

> \[!important\] Implementation details can be security-critical A small
> source-code change can drastically reduce the effective entropy of a
> generator. Security testing must consider the consequences of code
> changes, not merely whether the resulting program runs or passes
> ordinary debugging checks.

The slides quote Bruce Schneier's commentary from the *Crypto-Gram
Newsletter* of June 15, 2008. His main point is that random numbers are
used throughout cryptography, from short-term to long-term protection,
while random-number-generator flaws can be easy to introduce and hard to
discover later. He also notes that reducing a generator's entropy was a
technique historically used to weaken commercial cryptography.

## Randomness Demonstrations in the Era of AI

The middle slides show examples and follow-up questions involving AI
assistants (ChatGPT, Claude, and Gemini), with slides labelled "Random
Generator in the Era of AI," "Non-Random Generator in the Era of AI,"
"Follow-up with ChatGPT," and "New Question."

The teaching point of this section is the distinction between something
that appears random and something that is genuinely generated by a
suitable random process. Text or numbers produced by an AI system should
not automatically be treated as cryptographic randomness. A language
model generates responses according to its model and decoding process;
it is not, by that fact alone, a cryptographic random-number generator.

The slide images are illustrative demonstrations rather than a formal
security proof. The core concepts developed in the subsequent slides are
the difference between ordinary PRNGs and cryptographically secure
PRNGs, and the fact that statistical appearance is not equivalent to
unpredictability.

## PRNGs and Cryptographically Secure PRNGs

A conventional pseudorandom number generator may be well implemented and
still be unsuitable for cryptography.

-   **Ordinary PRNG:** primarily aims to produce sequences with good
    statistical properties, for example for simulations, modelling,
    testing, or procedural generation in games.
-   **Cryptographically secure PRNG (CS-PRNG or CSPRNG):** aims to
    generate output that is both statistically plausible and
    computationally infeasible to predict or reconstruct without the
    necessary secret information.

  -----------------------------------------------------------------------
  Aspect                  Ordinary PRNG           Cryptographically
                                                  secure PRNG
  ----------------------- ----------------------- -----------------------
  Main goal               Simulation, modelling,  Secure, unpredictable
                          games                   values

  Randomness requirement  Statistical properties  Statistical properties
                          such as distribution    plus resistance to
                          and low correlation     cryptanalysis

  Predictability          If the algorithm and    Prediction must be
                          state are known, future computationally
                          output may be           infeasible without
                          computable              secret state

  Security guarantees     Statistical tests do    Designed to provide
                          not guarantee security  cryptographic security
                                                  properties

  Typical applications    Simulation, testing,    Keys, nonces, salts,
                          procedural generation   cryptographic bit
                                                  streams

  Seed                    May be fixed or         Should come from
                          predictable             high-entropy sources;
                                                  reseeding may be used

  Evaluation              Statistical test suites Statistical tests and
                                                  cryptographic analysis
  -----------------------------------------------------------------------

The table in the lecture associates cryptographically secure generators
with forward and backward security properties. Terminology varies
between sources, so the precise state-compromise properties should be
defined explicitly for a given construction.

### A hash-chain challenge

The lecture asks whether the following generator is an ordinary PRNG or
a cryptographically secure PRNG:

``` text
Input: hash function H and random initial seed s
y = s
for i = 1 to n:
    y = H(y)
    output(y)
```

Each output is obtained by hashing the previous value. This design is
deterministic, and knowing the current state allows calculation of later
outputs. If an attacker learns `y` at some point, the attacker can
compute all subsequent values by repeatedly applying `H`.

The hash function alone does not make the sequence cryptographically
secure. The answer depends on the exact security goal, what the attacker
sees, and whether the seed or internal state can be recovered. In
particular, this simple construction does not provide protection against
compromise of the current state: once the state is known, future outputs
are easy to compute.

> \[!important\] Evaluate the construction, not just the primitive A
> secure hash function does not automatically make every generator built
> from it secure. The whole design---including state evolution, output
> generation, seeding, and compromise behaviour---must be analysed.

## BSI Evaluation Criteria for Deterministic Random Generators

The lecture introduces the German Federal Office for Information
Security, **Bundesamt für Sicherheit in der Informationstechnik (BSI)**,
and four criteria used to evaluate deterministic random-number
generators.

-   **K1 --- Long identical runs:** the probability of observing
    excessively long runs of identical bits should be low.
-   **K2 --- Statistical properties:** the output should be
    indistinguishable from true random numbers according to specified
    statistical tests.
-   **K3 --- Resistance to state/output inference:** from an output
    subsequence, an attacker should not be able to deduce past or future
    outputs or the internal state.
-   **K4 --- Protection of past outputs and states:** from a current
    internal state, an attacker should not be able to deduce past
    outputs or past states.

The lecture states that, for cryptographic applications, generators
meeting the K4 standard are acceptable. The important conceptual
distinction is that statistical quality (K1/K2) is not sufficient by
itself; criteria such as K3 and K4 address what an attacker can infer.

### K2 statistical tests

The slides mention several examples:

-   **Monobit test:** checks whether the numbers of zeroes and ones are
    approximately equal.
-   **Poker test:** a particular use of the chi-square
    ((`\chi`{=tex}\^2)) test to examine the frequencies of patterns.
-   **Runs test:** counts runs of consecutive identical bits and
    compares their frequencies with expectations.
-   **Longruns test:** checks for excessively long runs; the slide
    refers to runs of length 34 or more in a sequence of 20,000 bits.
-   **Autocorrelation test:** examines relationships between bits
    separated by a specified distance.

These tests look for statistical patterns that would be unusual in a
random sequence. They do not prove that a sequence is unpredictable to a
computationally powerful attacker.

For a fair-looking binary sequence, one expects roughly equal numbers of
zeros and ones. Also, after a run of zeros or ones, the next bit should
be one or zero with probability approximately one-half under the ideal
random model. A selected subsequence should not reveal reliable
information about future elements.

## The Next-Bit Test and Cryptographic Security

The lecture defines a cryptographically secure pseudorandom number
generator as one that satisfies the requirements of an ordinary PRNG and
also meets a stronger **next-bit test**.

> \[!definition\] Next-bit test Given the first (k) bits of a generated
> sequence, no polynomial-time algorithm should be able to predict the
> ((k+1))-th bit with success probability meaningfully better than
> (50%), under the security assumptions of the construction.

The (50%) baseline corresponds to guessing one of two possible bits
without useful information. The test is computational: an attacker may
theoretically be able to enumerate possibilities, but the required
computation should be infeasible.

The lecture also discusses **state-compromise extensions**:

-   If part or all of the generator's internal state is revealed or
    correctly guessed, it should not be possible to reconstruct the
    stream generated before the compromise.
-   If fresh entropy is supplied while the generator runs, knowledge of
    the input state should not make it feasible to predict future
    generator states.

These are security goals related to what are often called backtracking
resistance and prediction resistance. Exact guarantees depend on the
design and how reseeding is performed.

## Uses of Random Binary Numbers

Cryptographic network-security algorithms and protocols use random
binary numbers in several settings:

-   Key-distribution and mutual-authentication schemes.
-   Session-key generation.
-   Generation of keys for RSA public-key encryption.
-   Generation of bit streams for symmetric stream encryption.

Two distinct requirements must be kept separate:

1.  **Randomness:** the output has suitable statistical properties.
2.  **Unpredictability:** an attacker cannot feasibly forecast the
    output.

A sequence can look random yet be predictable if it was generated from a
small seed or a recoverable internal state. Conversely, a generator's
security design must not be judged from statistical appearance alone.

## Statistical Randomness: Uniformity and Independence

The lecture presents two criteria used to assess whether a sequence is
random in a statistical sense.

### Uniform distribution

For a binary sequence, zeroes and ones should occur approximately
equally often over a sufficiently long sample. This does not mean every
short block must contain exactly the same number of each bit; random
variation is expected. The requirement is about frequencies over
appropriate samples.

### Independence

One part of the sequence should not allow another part to be inferred.
In an ideal independent sequence, knowing earlier bits does not provide
a predictive advantage for the next bit.

> \[!example\] Balanced but not independent The repeating sequence
> `010101010101...` has equal numbers of zeros and ones over an
> even-length sample, but its next bit is perfectly predictable. It
> satisfies a simple frequency check while failing to behave like an
> independent random sequence.

This example demonstrates why one statistical test is never enough.

## Unpredictability

Cryptographic applications require more than statistical randomness:
successive values must be unpredictable to an opponent.

For truly random values under an ideal model, each value is independent
of the others. Physical random sources have practical limitations,
however, including inefficiency and the need for specialised hardware or
processing. Cryptographic systems therefore commonly use deterministic
algorithms to expand an initial seed into a long sequence that appears
random.

The generator must be designed so that an adversary cannot use earlier
outputs to predict future outputs. This is a security requirement, not
merely a visual or statistical property.

## Random and Pseudorandom Generator Models

The slides compare three conceptual constructions.

### True random number generator (TRNG)

A physical entropy source is converted into a binary stream. Processing
may be required to remove bias.

### Pseudorandom number generator (PRNG)

A seed is fed to a deterministic algorithm, which produces a
pseudorandom bit stream. Reusing the same seed with the same algorithm
reproduces the same output.

### Pseudorandom function (PRF)

A context-specific seed or key is supplied to a deterministic function
to produce a pseudorandom value or string, often of a fixed length.

The distinction between a PRNG and a PRF is primarily the interface and
intended output: a PRNG usually produces an open-ended sequence, whereas
a PRF produces an output of a specified length for an input or context.
In practice, the construction and security definition determine how it
should be used.

## True Random Number Generators (TRNGs)

A TRNG takes input from a source that is effectively random. This source
is called an **entropy source** and is drawn from the computer's
physical environment.

Examples given in the lecture include:

-   Keystroke timing patterns.
-   Electrical activity associated with the disk.
-   Mouse movements.
-   Instantaneous system-clock values.

One or more sources can feed an algorithm that produces random binary
output. A simple TRNG may convert an analogue source to binary. A more
elaborate design may perform additional processing to overcome bias or
otherwise condition the raw measurements.

A physical source should not automatically be assumed to be unbiased or
unpredictable. It must be evaluated and processed appropriately.

## Pseudorandom Number Generators (PRNGs)

A PRNG takes a fixed input value---the seed---and produces output bits
with a deterministic algorithm. The seed is often generated by a TRNG.

The output stream is determined by the input value or values. Therefore,
an adversary who knows both the algorithm and the seed can reproduce the
entire stream.

Two forms are highlighted in the lecture:

### Pseudorandom bit generator

An algorithm designed to produce an open-ended sequence of pseudorandom
bits. This is a common way to supply the keystream for a symmetric
stream cipher.

### Pseudorandom function (PRF)

A construction used to produce a pseudorandom string of a fixed length.
Examples of uses include deriving symmetric encryption keys and
generating nonces.

Other than the amount or format of output produced, the lecture notes
that there is no fundamental difference in the high-level purpose of
these two interfaces. Their exact cryptographic definitions and
permitted uses still matter.

## PRNG Requirements

For a PRNG or PRF used in cryptography, the basic requirement is that an
adversary who does not know the seed must be unable to determine the
pseudorandom string in a feasible way.

The lecture groups the requirements into three areas:

-   Randomness.
-   Unpredictability.
-   Characteristics and quality of the seed.

All three matter. A strong algorithm cannot reliably compensate for a
seed that is easy to guess, and a strong seed cannot rescue a generator
whose outputs reveal its state.

## Testing Randomness: NIST SP 800-22

The lecture refers to NIST Special Publication 800-22, which specifies a
suite of statistical tests for random and pseudorandom bit streams. The
slides identify three broad characteristics to seek:

-   **Uniformity:** frequencies and patterns should be compatible with
    the expected random model.
-   **Scalability:** the statistical behaviour should remain suitable as
    the amount of data grows.
-   **Consistency:** the sequence should continue to exhibit the
    expected properties across tested portions or samples.

No single test can conclusively establish that a PRNG is random.
Instead, multiple tests are used to look for different kinds of
statistical defects. Passing a suite is evidence against particular
detectable patterns, not a proof of cryptographic unpredictability.

### Examples of statistical tests

The lecture says that SP 800-22 lists 15 separate tests and highlights
three examples.

**Frequency test**

This is the most basic test and should be included in a test suite. It
checks whether the number of ones and zeros is approximately what would
be expected for a random sequence.

**Runs test**

A run is an uninterrupted sequence of identical bits bounded before and
after by a bit of the opposite value. For example, in `001110011`, the
runs are `00`, `111`, `00`, and `11`. The test examines whether the
total number and lengths of runs are plausible for a random sequence.

**Maurer's universal statistical test**

This test examines the distances between matching patterns. Its purpose
is to detect sequences that can be significantly compressed without loss
of information. A highly compressible sequence contains regularity and
is considered non-random under the test's model.

> \[!important\] Statistical tests have limits A sequence may pass
> statistical tests while still being cryptographically weak. Tests can
> reveal patterns, bias, and correlations, but cryptographic analysis
> must also examine whether an attacker can infer the seed, state, or
> future output.

## Forward and Backward Unpredictability

The lecture separates unpredictability into two forms.

### Forward unpredictability

If the seed is unknown, the next output bit should remain unpredictable
even when an attacker knows the previous output bits.

In practical terms, observing a long prefix should not let the attacker
predict the next output better than is computationally feasible under
the security model.

### Backward unpredictability

It should not be feasible to determine the seed from generated values.
There should be no exploitable correlation between the seed and any
output, and each bit should appear to be the outcome of an independent
event with probability (1/2).

The lecture notes that randomness tests can also provide some evidence
about unpredictability, because a sequence with strong correlations may
be easier to predict. However, passing statistical tests is not a
sufficient proof that the seed is unrecoverable.

> \[!info\] Terminology note "Forward" and "backward" unpredictability
> are used differently across cryptographic references. In these slides,
> forward unpredictability concerns predicting upcoming output from
> earlier bits, while backward unpredictability concerns recovering the
> seed from generated values. State-compromise properties are a related
> but separate issue.

## Seed Requirements

The seed supplied to a PRNG must itself be secure and unpredictable. It
should be a random or pseudorandom value and is typically generated
using a TRNG.

A seed's security depends on its entropy and on how well it is
protected. A long representation does not guarantee high entropy: a
timestamp stored in 64 bits may still have only a small number of
plausible values if the generation time is known approximately.

### Seed-generation pipeline

The lecture's diagram can be described as the following pipeline:

1.  An **entropy source** gathers unpredictable physical information.
2.  A **true random number generator (TRNG)** processes it into a seed.
3.  The seed is passed to a **pseudorandom number generator (PRNG)**.
4.  The PRNG expands it into a pseudorandom bit stream.

The TRNG and PRNG perform different jobs: the TRNG collects or
conditions physical entropy, while the PRNG expands the seed
deterministically.

## PRNG Algorithm Design

The lecture divides generator designs into two broad categories.

### Purpose-built algorithms

These are designed specifically and solely to generate pseudorandom bit
streams. Their state transitions and output rules are created for this
purpose.

### Algorithms based on existing cryptographic primitives

These use existing cryptographic algorithms to randomise input data. The
lecture identifies three broad families commonly used to construct
PRNGs:

-   Symmetric block ciphers.
-   Asymmetric ciphers.
-   Hash functions and message authentication codes (MACs).

The fact that a familiar cryptographic primitive is used does not remove
the need to analyse the generator construction as a whole.

## Linear Congruential Generator (LCG)

The linear congruential generator, first proposed by Lehmer, is a
classic deterministic method. It is parameterised by four values:

-   (m): the modulus, where (m\>0).
-   (a): the multiplier, where (0\<a\<m).
-   (c): the increment, where (0`\leq `{=tex}c\<m).
-   (X_0): the starting value or seed, where (0`\leq `{=tex}X_0\<m).

The sequence is generated iteratively:

\[ X\_{n+1} = (aX_n+c)`\bmod `{=tex}m. \]

If the parameters and seed are integers, each generated value is an
integer in the range

\[ 0`\leq `{=tex}X_n\<m. \]

### How the recurrence works

At each step:

1.  Multiply the current state (X_n) by (a).
2.  Add the increment (c).
3.  Take the remainder modulo (m).
4.  Use the result as the next state (X\_{n+1}).

Because the state can take only (m) possible values, the sequence must
eventually repeat. The period and quality of the sequence depend
strongly on the choices of (a), (c), and (m).

> \[!example\] One LCG step Suppose (m=16), (a=5), (c=3), and (X_0=2).
> Then
>
> \[ X_1=(5`\cdot 2`{=tex}+3)`\bmod 16`{=tex}=13. \]
>
> The next value is
>
> \[ X_2=(5`\cdot 13`{=tex}+3)`\bmod 16`{=tex}=68`\bmod 16`{=tex}=4. \]
>
> This illustrates the recurrence only; these small parameters are not
> suitable for cryptographic use.

LCGs are simple and can be useful for basic simulation tasks when chosen
appropriately, but their linear structure makes them predictable and
unsuitable for cryptographic security. The lecture emphasises that
parameter selection is critical even for the generator's basic
statistical quality.

## Blum Blum Shub (BBS) Generator

Blum Blum Shub is presented as a purpose-built cryptographic
pseudorandom bit generator with a strong theoretical basis. The lecture
describes it as having perhaps the strongest public proof of
cryptographic strength among purpose-built generators.

A **cryptographically secure pseudorandom bit generator (CSPRBG)** is
defined in the slides through the next-bit test: no polynomial-time
algorithm, given the first (k) output bits, should predict the
((k+1))-th bit with probability significantly greater than (1/2).

The security of BBS is based on the computational difficulty of
factoring the modulus (n).

### Basic idea

BBS repeatedly squares a state modulo (n), then outputs the least
significant bit of the resulting state. In its standard form, (n) is
constructed as the product of two suitable large primes, and the initial
seed must satisfy the construction's requirements.

The state update is commonly written as

\[ X\_{i+1}=X_i\^2`\bmod `{=tex}n, \]

and the output bit is

\[ B_i = X_i `\bmod 2`{=tex}. \]

The lecture's diagram shows the general flow:

1.  Initialise using a seed (s).
2.  Generate the next state by squaring modulo (n).
3.  Select the least significant bit.
4.  Emit that bit, which is either (0) or (1).
5.  Repeat to produce a bit sequence.

The modulus and seed must be chosen correctly. The theoretical security
argument depends on the number-theoretic assumptions and on using the
construction as specified; it does not mean that any arbitrary choice of
parameters is secure.

### Example operation in the slides

The final table gives a sequence of states (X_i) and corresponding
output bits (B_i). It begins with (X_0=20749). The listed output bits
include:

    \(i\)    (X_i)   (B_i)
  ------- -------- -------
        1   143135       1
        2   177671       1
        3    97048       0
        4    89992       0
        5   174051       1
        6    80649       1
        7    45663       1
        8    69442       0
        9   186894       0
       10   177046       0
       11   137922       0
       12   123175       1
       13     8630       0
       14   114386       0
       15    14863       1
       16   133015       1
       17   106065       1
       18    45870       0
       19   137171       1
       20    48060       0

The output bit is obtained from the parity of the state: even states
give (0), odd states give (1). The initial row in the slide shows the
seed state but no output bit.

> \[!important\] BBS versus an ordinary PRNG BBS is designed with a
> cryptographic security argument tied to a hard number-theoretic
> problem. An ordinary generator such as a simple LCG is generally
> judged mainly by speed and statistical behaviour and should not be
> treated as a secure source of cryptographic keys.

## PRNGs Using Block-Cipher Modes of Operation

The final slide introduces two approaches that use a block cipher to
construct a pseudorandom generator:

-   **Counter (CTR) mode**, recommended in the lecture with references
    to NIST SP 800-90, ANSI X9.82, and RFC 4086.
-   **Output Feedback (OFB) mode**, referenced with ANSI X9.82 and RFC
    4086.

At a high level, a block cipher can be used to transform a changing
input or feedback state into output blocks. In CTR-style generation, a
block cipher is applied to successive counter values under a key; in
OFB-style generation, output from one encryption step feeds the next
step.

These modes are not interchangeable in all contexts, and a secure
implementation must follow the relevant standard's requirements for
keys, state, counters, and reseeding. The lecture's point is that
established cryptographic primitives can be used as building blocks for
generating pseudorandom output, provided the complete construction is
appropriate.

## Stream Ciphers

The lecture introduces stream ciphers early, after describing
symmetric-key cryptography.

### Symmetric-key setting

Alice and Bob share:

-   An encryption protocol or algorithm (E).
-   A secret symmetric key (K).

They communicate using (E) and (K). The adversary may know the algorithm
and some exchanged messages, but does not know the secret key. This
reflects the standard principle that security should depend on the
secrecy of the key, not on keeping the algorithm secret.

The lecture distinguishes two broad approaches to symmetric encryption:

-   Stream ciphers.
-   Block ciphers.

### Basic stream-cipher construction

A stream cipher uses a secret key as a seed to generate a sequence of
bytes or bits called the **keystream**. The plaintext is combined with
this keystream using bitwise exclusive OR (XOR) to produce the
ciphertext.

For bits (x) and (y), XOR is defined by:

    \(x\)   \(y\)   (x`\oplus `{=tex}y)
  ------- ------- ---------------------
        0       0                     0
        0       1                     1
        1       0                     1
        1       1                     0

XOR outputs 1 when its inputs differ and 0 when they are the same.

If the plaintext is (P), the keystream is (Z), and the ciphertext is
(C), encryption is

\[ C=P`\oplus `{=tex}Z. \]

Decryption uses the same keystream:

\[ P=C`\oplus `{=tex}Z. \]

This works because XOR is self-inverse:

\[
(P`\oplus `{=tex}Z)`\oplus `{=tex}Z=P`\oplus`{=tex}(Z`\oplus `{=tex}Z)=P`\oplus 0`{=tex}=P.
\]

The keystream must be generated identically at both ends, and it must be
kept secret and unpredictable. Reusing the same keystream for multiple
messages can expose relationships between the plaintexts, so secure
stream-cipher use requires careful key and nonce/initialisation
handling.

### Synchronous stream ciphers

In a **synchronous stream cipher**, the keystream depends only on the
secret key (and, in practical designs, any required initialisation value
or nonce), not on previous ciphertext bytes.

Conceptually:

$[ Z_i=f(K,i) ]$

or, more generally, the keystream generator advances its own internal
state in synchrony at sender and receiver.

Encryption:

\[ C_i=P_i`\oplus `{=tex}Z_i. \]

Decryption:

\[ P_i=C_i`\oplus `{=tex}Z_i. \]

The sender and receiver must remain synchronised: they must generate the
same keystream position for each plaintext or ciphertext bit. If they
become misaligned, decryption will produce incorrect output until
synchronisation is restored according to the design.

### Asynchronous (self-synchronising) stream ciphers

In an **asynchronous stream cipher**, the keystream generation depends
on the key and previous ciphertext bytes. The lecture describes the
(i)-th byte as a function of the key and the first (i-1) ciphertext
bytes.

This dependence allows the receiver's state to be influenced by received
ciphertext, so some forms of lost or inserted data can be handled
differently from synchronous designs. The exact error and recovery
behaviour depends on the construction.

> \[!important\] Do not confuse the two designs - **Synchronous:**
> keystream generation depends on the key and generator state, rather
> than the previous ciphertext. - **Asynchronous/self-synchronising:**
> keystream generation also depends on preceding ciphertext.
>
> Both encrypt by combining plaintext with a keystream, but their state
> and synchronisation behaviour differ.

### Stream-cipher encryption and decryption diagrams

The slides show the same XOR structure for encryption and decryption.

**Encryption:** the key feeds the keystream generator. The keystream is
XORed with plaintext to produce ciphertext.

\[ `\boxed{P\oplus Z=C}`{=tex} \]

**Decryption:** the same key and matching keystream are used. XORing the
ciphertext with the keystream recovers the plaintext.

\[ `\boxed{C\oplus Z=P}`{=tex} \]

No separate inverse transformation of the keystream generator is
required; XOR itself reverses the operation when the same keystream is
available.

## Historical and Practical Examples of Stream Ciphers

The lecture lists several historical or practical examples:

-   Many ciphers used before 1940.
-   **Enigma**, used by Germany during World War II.
-   **A5**, used in GSM cellular communications to protect the radio
    link between a mobile phone and a base station.
-   **WEP**, used in IEEE 802.11 wireless networks.
-   **RC4** ("Ron's Code"), used by WEP.

These examples are historical context, not endorsements of current
security. Enigma, A5 variants, WEP, and RC4 have important limitations
or known vulnerabilities; old deployment does not imply modern
cryptographic security.

## A5/1 Stream Cipher

A5/1 was developed in 1987 for over-the-air privacy in the GSM cellular
telephone standard.

The slides highlight the historical and political context:

-   In the mid-1980s, NATO signal-intelligence agencies disagreed about
    whether GSM encryption should be strong or deliberately weakened.
-   A5/1 was used in Europe and the United States.
-   A related version, A5/2, was intentionally weakened for export to
    some regions.
-   The design was initially kept secret.
-   It leaked in 1994 and was fully reverse-engineered by Marc Briceno
    in 1999.
-   Multiple serious vulnerabilities were subsequently identified.

This case illustrates why keeping an algorithm secret is not a reliable
substitute for a strong design. Once the algorithm became public and
could be analysed, its weaknesses could be studied and exploited.

## Putting the Concepts Together

The topics in the lecture form one connected security chain:

1.  Cryptographic protocols require unpredictable values for keys,
    nonces, session keys, and keystreams.
2.  Physical entropy sources can supply unpredictable input, but their
    output may be biased or partially predictable.
3.  A TRNG collects and conditions physical entropy.
4.  A seed derived from adequate entropy initialises a deterministic
    PRNG or cryptographically secure PRNG.
5.  Statistical tests can reveal bias or structure, but they do not
    prove resistance to prediction.
6.  A cryptographically secure generator must protect against attacks on
    its outputs, seed, and internal state.
7.  A stream cipher uses a key-driven keystream and XOR to encrypt and
    decrypt data.
8.  Weak seeds, state exposure, flawed implementations, or insecure
    constructions can undermine the security of the entire protocol.

> \[!abstract\] Final distinction to remember **Randomness** concerns
> the statistical behaviour of values. **Unpredictability** concerns
> whether an attacker can forecast them. **Cryptographic security**
> requires a construction and implementation that protect against
> feasible attacks, including attacks that exploit the seed, internal
> state, or previous outputs.

## Review Questions

-   Why is a deterministic PRNG not a source of new physical randomness?
-   Why can a generator with a long output still be insecure if it has a
    small seed space?
-   Why is the current time often a poor seed?
-   What did the Netscape and Debian examples teach about entropy and
    implementation mistakes?
-   What is the difference between an ordinary PRNG and a
    cryptographically secure PRNG?
-   What does the next-bit test require?
-   What do the monobit/frequency, poker, runs, longruns,
    autocorrelation, and Maurer tests examine?
-   How do uniformity and independence differ?
-   What is the difference between a TRNG, a PRNG, and a PRF?
-   What are forward and backward unpredictability in the terminology
    used by the slides?
-   What is the LCG recurrence, and why is it unsuitable for
    cryptographic use?
-   What is the basic state update and output rule of Blum Blum Shub?
-   How does a stream cipher use XOR for encryption and decryption?
-   How do synchronous and asynchronous stream ciphers differ?
-   Why does the history of A5/1 illustrate the limits of relying on
    secrecy of design?
-   Which block-cipher modes are mentioned as building blocks for PRNGs?
