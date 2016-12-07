# Project Wycheproof
https://github.com/google/wycheproof

*Project Wycheproof is named after
[Mount Wycheproof](https://en.wikipedia.org/wiki/Mount_Wycheproof), the smallest
mountain in the world. The main motivation for the project is to have a goal
that is achievable. The smaller the mountain the more likely it is to be able to
climb it.*

## Introduction

Project Wycheproof tests crypto libraries against known attacks. It is developed
and maintained by [members of Google Security Team](AUTHORS.md), but it is not
an official Google product.

At Google, we rely on many third party cryptographic software libraries.
Unfortunately, in cryptography, subtle mistakes can have catastrophic
consequences, and we found that libraries fall into such implementation
pitfalls much too often and for much too long. Good implementation guidelines,
however, are hard to come by: understanding how to implement cryptography
securely requires digesting decades' worth of academic literature. We recognize
that software engineers fix and prevent bugs with unit testing, and we found
that cryptographic loopholes can be resolved by the same means.

These observations have prompted us to develop Project Wycheproof, a collection
of unit tests that detect known weaknesses or check for expected behaviors of
some cryptographic algorithm. Project Wycheproof provides tests for most
cryptographic algorithms, including RSA, elliptic curve crypto and
authenticated encryption. Our cryptographers have systematically surveyed the
literature and implemented most known attacks. We have over 80 test cases which
have uncovered more than [40 bugs](doc/bugs.md). For
example, we found that we could recover the private key of widely-used DSA and
ECDHC implementations.

While we are committed to develop as many attacks as possible, Project
Wycheproof is by no means complete. Passing the tests does not imply that the
library is secure, it just means that it is not vulnerable to the attacks that
Project Wycheproof tests for. Cryptographers are also constantly discovering
new attacks. Nevertheless, with Project Wycheproof developers and users now can
check their libraries against a large number of known attacks, without having
to spend years reading academic papers or become cryptographers themselves.

For more information on the goals and strategies of Project Wycheproof, please
check out our [doc](doc/).

### Coverage

Project Wycheproof has tests for the most popular crypto algorithms, including

 - AES-EAX
 - AES-GCM
 - [DH](doc/dh.md)
 - DHIES
 - [DSA](doc/dsa.md)
 - [ECDH](doc/ecdh.md)
 - ECDSA
 - ECIES
 - [RSA](doc/rsa.md)

The tests detect whether a library is vulnerable to many attacks, including

 - Invalid curve attacks
 - Biased nonces in digital signature schemes
 - Of course, all Bleichenbacher’s attacks
 - And many more -- we have over 80 test cases

Our first set of tests are written in Java, because Java has a common
cryptographic interface. This allowed us to test multiple providers with a
single test suite. While this interface is somewhat low level, and should not
be used directly, we still apply a "defense in depth" argument and expect that
the implementations are as robust as possible. For example, we consider weak
default values to be a significant security flaw. We are converting as many
tests into sets of test vectors to simplify porting the tests to other
languages. We provide ready-to-use test runners for Java Cryptography
Architecture providers such as [Bouncy Castle](http://bouncycastle.org),
[Conscrypt](https://conscrypt.org) and the default providers in
[OpenJDK](http://openjdk.java.net/).

### Usage

Project Wycheproof uses the [Bazel](https://bazel.build/) build system, you need
to [install](https://bazel.build/versions/master/docs/install.html) Bazel to run
the tests.

Check out the tests

```
git clone https://github.com/google/wycheproof.git
```

To test latest stable version of Bouncy Castle:

```
bazel test BouncyCastleAllTests
```

To test other versions, e.g., v1.52:

```
bazel test BouncyCastleAllTests_1_52
```

To test all known versions (warning, will take a long time):

```
bazel test BouncyCastleAllTests_*
```

ConscryptAllTests and OpenJDKAllTests run the tests against
[Conscrypt](https://conscrypt.org) and [OpenJDK](http://openjdk.java.net/),
respectively.

Some of the tests take a very long time to run. If you want to exclude those
tests, use either BouncyCastleTest, ConscryptTest or OpenJDKTest -- these
targets exclude all slow tests (i.e., those annotated with @SlowTest).

### Hall of Bugs

Here are some of the notable vulnerabilities that are uncovered by
Project Wycheproof:

 - OpenJDK's SHA1withDSA leaks private keys > 1024 bits
   - Test: testBiasSha1WithDSA in
[DsaTest](https://github.com/google/wycheproof/blob/master/java/com/google/security/wycheproof/testcases/DsaTest.java).
   - This bug is the same as
[CVE-2003-0971 - GnuPG generated ElGamal signatures that leaked the private key]
(https://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2003-0971).

 - Bouncy Castle's ECDHC leaks private keys
   - Test: testModifiedPublic in
[EcdhTest](https://github.com/google/wycheproof/blob/master/java/com/google/security/wycheproof/testcases/EcdhTest.java).

### Contact and mailing list

If you want to contribute, please read [CONTRIBUTING](CONTRIBUTING.md) and send
us pull requests. You can also report bugs or request new tests.

If you'd like to talk to our developers or get notified about major new
tests, you may want to subscribe to our
[mailing list](https://groups.google.com/forum/#!forum/wycheproof-users). To
join, simply send an empty mail to wycheproof-users+subscribe@googlegroups.com.
