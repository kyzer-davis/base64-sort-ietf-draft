---
title: A Sortable Base64 Alphabet
abbrev: base64-sort
category: std

docname: draft-brown-davis-base64-sort-00
submissiontype: IETF
date: 2026
consensus: true
v: 3
keyword:
  - encoding
  - base64
pi: [toc, sortrefs, symrefs]
workgroup: Independent

author:
  - name: Dillon Brown
    email: dillbrow@cisco.com
    org: Cisco Systems
  - name: Kyzer R. Davis
    email: kydavis@cisco.com
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
    target: https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft
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

This document serves as a standards-track RFC to provide a new alphabet that follows the US-ASCII ({{RFC20}}) ordering, re-uses the Base64url special characters and sorts the same as binary. This document is created to serve as a reference for new libraries and for future RFCs to cite.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# The Alphabet {#alphabet}

This Base64 Sortable alphabet, which can be referenced as "Base64sort", is a variant of the Base64url alphabet ({{RFC4648, Section 5}}).

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

Base64sort follows the same padding logic as Base64url ({{RFC4648, Section 5}}).
That is, padding MAY be used with Base64sort.

Although the equal sign (=) padding character is positioned between the numeric characters and the uppercase letters in the US-ASCII character set; it does not cause any issues with sorting since the padding is always in the right-most, least significant position of the encoded string.

Note that this assumes the sorted data is either entirely all padded or un-padded encodings.
Mixing padded and un-padded encodings in the same sorted set may lead to unexpected results since the padding character would be treated as a significant character in the sort order.

Implementations MAY utilize a padding character that is not part of the Base64sort alphabet and does not interfere with sorting, such as a tilde (~), to maintain the integrity of the sort order of mixed datasets while still indicating padding.

# Decoding {#decoding}

The decoding process for Base64sort follows the principles outlined in ({{RFC4648, Section 5}}), for decoding Base64url.

# Security Considerations {#security_considerations}

This document has no security considerations.

# IANA Considerations {#iana_considerations}

This document has no IANA actions.

--- back

# Changelog {#changelog}

{:removeInRfc}

draft-00:

{: spacing="compact"}

- Initial Release

# Test Vectors {#test_vectors}

The following test vectors start with a base of those found in {{RFC4648}} and then include additional values for testing the new alphabet and its sorting properties.

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
| `0123456789`  | `B23mBnFpCYRsDF~~` | Input with Tilde Padding |
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
| `B23mBnFpCYRsDF~~` | `0123456789`  |
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
