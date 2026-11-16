---
title: A Sortable Base64 Alphabet
abbrev: base64-sort
category: std

docname: draft-brown-davis-base64-sort-02
submissiontype: IETF
date: 2026
consensus: true
v: 3
workgroup: Network Working Group
keyword:
  - encoding
  - base64
pi: [toc, sortrefs, symrefs]
venue:
  group: "Dispatch (dispatch)"
  type: Working Group
  mail: dispatch@ietf.org
  arch: https://mailarchive.ietf.org/arch/browse/dispatch/
  github: kyzer-davis/base64-sort-ietf-draft
  latest: https://raw.githubusercontent.com/kyzer-davis/base64-sort-ietf-draft/refs/heads/main/draft-brown-davis-base64-sort.md

author:
- name: Kyzer R. Davis
  email:
    - kydavis@cisco.com
    - kyzer.davis@outlook.com
  org: Cisco Systems
- name: Dillon Brown
  email: dillbrow@cisco.com
  org: Cisco Systems

normative:
  RFC20:
  RFC4648:

informative:
  RFC9562:
  Base58:
    target: https://github.com/bitcoin/bitcoin/blob/master/src/base58.cpp
    title: Bitcoin Base58 Encoding
    author:
    - org: Bitcoin
    date: 2008-11
    seriesinfo:
      commit: fae71d3
  RFC4648_Usage_Report:
    target: https://gitlab.com/julian.reschke/base-encodings-terminology/-/blob/main/classifcation.md?ref_type=heads
    title: RFC4648 Alphabet Usage Report
    authors:
    - name: Julian Reschke
    - name: Kyzer Davis
    date: 2025-11
    seriesinfo:
      commit: de0a2760
  new-uuid-encoding-techniques-ietf-draft:
    target: https://datatracker.ietf.org/doc/draft-davis-uuidrev-alt-uuid-encoding-methods/
    title: Alternate UUID Encoding Methods
    author:
    - name: Kyzer Davis

--- abstract

This document details a formal specification for a new Base64 alphabet which follows the US-ASCII ordering and sorts the same as binary.
This serves as an alternative variant to Base64 and Base64url when lexicographically sortable outputs are required.
The alphabet re-uses the Base64url special characters and is designed to be compatible with existing Base64 implementations, while providing a new option for applications that require sorted output.

--- middle

# Introduction

Base64 ({{RFC4648, Section 4}}) and Base64url ({{RFC4648, Section 5}}) have been widely implemented and tested across the industry; however, if one requires a lexicographically sortable output they will need to use either Base32hex, Base36, or {{Base58}}.

There is no standardized sortable Base64 alphabet.
Since the Base64 alphabets from RFC4648 see far more industry usage than the other previously mentioned alphabets there is a real need and requirement for a sortable Base64 Alphabet in the industry.
See: {{RFC4648_Usage_Report}} for the breakdown of RFC citations for RFC4648 alphabets.

This gap was made further apparent while examining Base64 usage with Universally Unique IDentifiers (UUIDs) defined in {{RFC9562}} to create {{new-uuid-encoding-techniques-ietf-draft}} which asserts that UUIDv6 and UUIDv7 benefit greatly from lexicographically sortable alphabets like Base64 Sortable.

This document specifies a new alphabet that follows the US-ASCII ({{RFC20}}) ordering, re-uses the Base64url special characters and sorts the same as binary. This document is created to serve as a reference for new libraries and for future RFCs to cite.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# The Alphabet {#alphabet}

This Base64 Sortable alphabet, which can be referenced as "Base64sort" or sometimes "Base64lex", is a variant of the Base64url alphabet ({{RFC4648, Section 5}}).

While it utilizes the same set of 64 URL-safe characters as Base64url, its fundamental distinction lies in the assignment of values to these characters by aligning the character values with their US-ASCII ({{RFC20}}) ordering to ensure that the encoded output sorts lexicographically in the same order as the underlying binary data.

Specifically, the numeric characters (0-9) have been moved before the uppercase and lowercase characters while the special symbol characters, hyphen (-) and underscore (_), are placed at the beginning and middle of the alphabet as they appear in the US-ASCII character set.

{{alphabetTable}} details the alphabet characters along with their respective decoding and encoding values.

| Value | Encoding | Value | Encoding | Value | Encoding | Value | Encoding |
|-------|----------|-------|----------|-------|----------|-------|----------|
| 0     | -        | 16    | F        | 32    | V        | 48    | k        |
| 1     | 0        | 17    | G        | 33    | W        | 49    | l        |
| 2     | 1        | 18    | H        | 34    | X        | 50    | m        |
| 3     | 2        | 19    | I        | 35    | Y        | 51    | n        |
| 4     | 3        | 20    | J        | 36    | Z        | 52    | o        |
| 5     | 4        | 21    | K        | 37    | _        | 53    | p        |
| 6     | 5        | 22    | L        | 38    | a        | 54    | q        |
| 7     | 6        | 23    | M        | 39    | b        | 55    | r        |
| 8     | 7        | 24    | N        | 40    | c        | 56    | s        |
| 9     | 8        | 25    | O        | 41    | d        | 57    | t        |
| 10    | 9        | 26    | P        | 42    | e        | 58    | u        |
| 11    | A        | 27    | Q        | 43    | f        | 59    | v        |
| 12    | B        | 28    | R        | 44    | g        | 60    | w        |
| 13    | C        | 29    | S        | 45    | h        | 61    | x        |
| 14    | D        | 30    | T        | 46    | i        | 62    | y        |
| 15    | E        | 31    | U        | 47    | j        | 63    | z        |
{: #alphabetTable title='The Base64sort Alphabet as table'}

The Alphabet as a continuous text input can be found in {{alphabetText}}.

~~~
-0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ_abcdefghijklmnopqrstuvwxyz
~~~
{: #alphabetText title='The Base64sort Alphabet as text'}

## Padding {#padding}

Base64sort uses the same equal sign (=) padding character as Base64url ({{RFC4648, Section 5}}).
As allowed by {{RFC4648, Section 3.2}}, padding MAY be used with Base64sort but SHOULD be omitted when the encoded output is used for sorting.

The equal sign (=) padding character is positioned between the numeric characters and the uppercase letters in the US-ASCII character set.
Since the padding is always in the right-most, least significant position of the encoded string, it does not cause any issues with sorting when every input in the sorted set is the same length, such as 128-bit UUIDs.

When inputs of different lengths are sorted together, padding can cause the encoded output to sort differently than the binary.
For example, the single octet 0x00 encodes to `--==` while the two octets 0x00 0x00 encode to `---=`.
Since the equal sign (=) sorts after the hyphen (-), the shorter input sorts after the longer input even though it sorts before it as binary.
Un-padded encodings do not have this issue since `--` sorts before `---`.

Implementations SHOULD NOT use a padding character other than the equal sign (=).
Characters that sort after the Base64sort alphabet, such as a tilde (~), have the same sorting issue as the equal sign (=) and may not be accepted by other Base64sort decoders.

# Decoding {#decoding}

The decoding process for Base64sort follows the principles outlined in ({{RFC4648, Section 5}}), for decoding Base64url.

# Security Considerations {#security_considerations}

The security considerations found in {{RFC4648, Section 12}} also apply to Base64sort.

Base64sort uses the same 64 characters as Base64url, so data encoded with one alphabet can be decoded with the other without any error.
The result is incorrect binary data rather than a decoding failure.
Applications SHOULD clearly identify which alphabet is in use, such as in the protocol or data format specification, rather than attempting to detect the alphabet from the encoded data.

Encoders MUST set the pad bits to zero as described in {{RFC4648, Section 3.5}}.
Otherwise the same binary data can have more than one encoding and these encodings will not sort or compare as equal.
Decoders MAY reject encodings where the pad bits are not zero.

Base64sort is an encoding and does not provide any confidentiality.
Since the encoded output keeps the same sort order as the binary, any ordering information in the data, such as the timestamp found in UUIDv6 and UUIDv7 ({{RFC9562}}), is still visible after encoding.

# IANA Considerations {#iana_considerations}

This document has no IANA actions.

--- back

# Changelog {#changelog}

{:removeInRfc}

draft-00:

{: spacing="compact"}

- Initial Release

draft-01:

{: spacing="compact"}

- Update frontmatter venue and Alt encoding link

draft-02:

{: spacing="compact"}

- Update venue since uuidrev has concluded
- Correct padding guidance for sorting mixed length inputs
- Remove tilde padding option and test vectors
- Add Security Considerations

# Test Vectors {#test_vectors}

The following test vectors start with a base of those found in {{RFC4648}} and then include additional values for testing the new alphabet and its sorting properties.
Encoded data is shown without padding unless noted.

## Encoding {#test_vectors_encode}

| Input         | Encoded Data       | Input Type         |
|---------------|--------------------|--------------------|
| `f`           | `OV`               | Text               |
| `fo`          | `Oaw`              | Text               |
| `foo`         | `Oaxj`             | Text               |
| `foob`        | `OaxjNV`           | Text               |
| `fooba`       | `OaxjNa3`          | Text               |
| `foobar`      | `OaxjNa4m`         | Text               |
| `test`        | `S5KnS-`           | Text               |
| `Hello World` | `H5KgQ5wVKqxmQ5F`  | Text               |
| `-`           | `AF`               | Hyphen             |
| `0`           | `B-`               | Digit              |
| `_`           | `Mk`               | Underscore         |
| `A`           | `FF`               | Uppercase Letter   |
| `a`           | `NF`               | Lowercase Letter   |
| `=`           | `EF`               | Equal Sign         |
| `~`           | `UV`               | Tilde              |
| `0123456789`  | `B23mBnFpCYRsDF==` | Input with Equal Padding |
{: #encodeTable title='Base64 Sortable Encode Table'}

## Decoding {#test_vectors_decode}

| Input              | Decoded Data  |
|--------------------|---------------|
| `OV`               | `f`           |
| `Oaw`              | `fo`          |
| `Oaxj`             | `foo`         |
| `OaxjNV`           | `foob`        |
| `OaxjNa3`          | `fooba`       |
| `OaxjNa4m`         | `foobar`      |
| `S5KnS-`           | `test`        |
| `H5KgQ5wVKqxmQ5F`  | `Hello World` |
| `AF`               | `-`           |
| `B-`               | `0`           |
| `Mk`               | `_`           |
| `FF`               | `A`           |
| `NF`               | `a`           |
| `EF`               | `=`           |
| `UV`               | `~`           |
| `B23mBnFpCYRsDF==` | `0123456789`  |
{: #decodeTable title='Base64 Sortable Decode Table'}

# Industry Tests {#industry_tests}

To validate the lexicographical sortability of the Base64sort alphabet in common computing environments, a series of tests were conducted across various programming languages and database systems.

The objective was to observe how a mixed list of characters from the proposed alphabet would sort using their default string comparison mechanisms.

## Programming Language Results {#programming_language_tests}

Tests were performed using Python, C, C++, Java, JavaScript, Delphi/Object Pascal, Perl, and Go.

In these languages, a list containing a subset of the Base64sort alphabet characters (`A`, `a`, `b`, `7`, `3`, `B`, `C`, `c`, `E`, `z`, `-`, `_`) was sorted.

The results consistently demonstrated sorting according to the US-ASCII character order: `-`, `3`, `7`, `A`, `B`, `C`, `E`, `_`, `a`, `b`, `c`, `z`.

This indicates that the chosen character set and their relative ASCII values are respected by the default string sorting algorithms in these environments.

However, C# and Visual Basic exhibited a different sorting behavior, placing `_` before `3` and sorting uppercase letters after their lowercase counterparts (e.g., `a` before `A`).

This suggests that their default string comparison routines may employ locale-aware or cultural sorting rules rather than strict ordinal (ASCII) comparison.

## Database Test Results {#database_test_results}

Database tests were conducted on PostgreSQL, MongoDB, and Redis.

With PostgreSQL, when using `ORDER BY val ASC`, the default collation resulted in underscore (_) appearing before the hyphen (-), and lowercase letters before uppercase letters.
However, explicitly specifying `ORDER BY val COLLATE "C"` (which enforces a C locale, often equivalent to strict ASCII ordering) produced the desired sort order: `-`, `3`, `7`, `A`, `B`, `C`, `E`, `_`, `a`, `b`, `c`, `z`.

MongoDB's default sort behavior for the test set aligned with the desired US-ASCII order, matching the results observed in most programming languages.

Redis's `SORT ... ALPHA` command also showed a deviation, placing `_` before `3` and lowercase letters before uppercase, similar to the default SQL, C#, and Visual Basic results.

## Conclusion

The testing confirms that the Base64 Sortable alphabet generally achieves its goal of producing lexicographically sortable output when standard US-ASCII or C-locale string comparison rules are applied.

Implementers should be aware that some programming languages and database systems may employ locale-aware or alternative default collation rules that can alter the sort order.

To ensure consistent binary-equivalent sorting, explicit specification of ordinal or C-locale collation may be necessary in environments where such options are available.
