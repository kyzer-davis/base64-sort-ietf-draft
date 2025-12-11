---
title: Base64 Sortable
abbrev: base64-sort
category: info

docname: draft-base64-sort
submissiontype: IETF
date: 2025
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
  RFC4648:

--- abstract

This document details a formal specification for a new Base64 alphabet that follows the US-ASCII ordering and is sorts the same as binary.

--- middle

# Introduction

Base64 ({{RFC4648, Section 4}}) and Base64url ({{RFC4648, Section 5}}) have been widely implemented and tested accross the industry; However, if we need a lexicographical sortable ID we have to use Base32hex, Base36, or Base58. There is not a unified way of doing this for Base64. 

This document serves as an informational RFC to provide a new alphabet that follows the US-ASCII ordering, re-uses the Base64url special characters and sorts the same as binary. 

# The Alphabet {#alphabetTable}

This Base64 Sortable alphabet is a variant of the Base64url alphabet ({{RFC4648, Section 5}}). While it utilizes the same set of 64 URL-safe characters as Base64url, its fundamental distinction lies in the assignment of values to these characters. This alphabet is specifically designed such that its encoded output sorts lexicographically in the same order as the underlying binary data, by aligning the character values with their US-ASCII ordering.

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

The Alphabet as a continuous text input is as follows:
`-0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ_abcdefghijklmnopqrstuvwxyz`

## Padding {#padding}

Consistent with Base64url ({{RFC4648, Section 5}}), the padding character = is optional in Base64 Sortable encoded data. When padding is omitted, the decoder must infer the original data length based on the length of the encoded string. If padding is present, the = character is used. Implementers should note that the = character is not URL-safe and would require percent-encoding (e.g., %3D) if the encoded string is to be directly embedded within certain URL components. The Base64 Sortable encoding process itself does not perform this percent-encoding.

# Decoding {#decoding}

The decoding process for Base64sort follows the principles outlined in ({{RFC4648, Section 5}}), for decoding Base64url. An encoded string is first processed by removing any optional padding characters (=). If padding is absent, the decoder infers the original data length from the encoded string length. Each character from the (potentially unpadded) encoded string is then mapped directly to its corresponding 6-bit value using the Base64 Sortable alphabet defined in {{alphabetTable}}. These 6-bit values are concatenated to form a continuous bit stream, which is subsequently reassembled into 8-bit octets to reconstruct the original binary data. Any partial octets resulting from the padding removal are discarded.

# Industry Tests {#industry_tests}

To validate the lexicographical sortability of the Base64 Sortable alphabet in common computing environments, a series of tests were conducted across various programming languages and database systems. The objective was to observe how a mixed list of characters from the proposed alphabet would sort using their default string comparison mechanisms.

## Programming Language Results

Tests were performed using Python, C, C++, Java, JavaScript, Delphi/Object Pascal, Perl, and Go. In these languages, a list containing a subset of the Base64 Sortable alphabet characters (`A`, `a`, `b`, `7`, `3`, `B`, `C`, `c`, `E`, `z`, `-`, `_`) was sorted. The results consistently demonstrated sorting according to the US-ASCII character order: `-`, `3`, `7`, `A`, `B`, `C`, `E`, `_`, `a`, `b`, `c`, `z`. This indicates that the chosen character set and their relative ASCII values are respected by the default string sorting algorithms in these environments.

However, C# and Visual Basic exhibited a different sorting behavior, placing `_` before `3` and sorting uppercase letters after their lowercase counterparts (e.g., `a` before `A`). This suggests that their default string comparison routines may employ locale-aware or cultural sorting rules rather than strict ordinal (ASCII) comparison.

## Database Test Results

Database tests were conducted on SQL (PostgreSQL), MongoDB, and Redis.

*   **SQL**: When using `ORDER BY val ASC`, the default collation resulted in `_` appearing before `-`, and lowercase letters before uppercase (e.g., `a` before `A`). However, explicitly specifying `ORDER BY val COLLATE "C"` (which enforces a C locale, often equivalent to strict ASCII ordering) produced the desired sort order: `-`, `3`, `7`, `A`, `B`, `C`, `E`, `_`, `a`, `b`, `c`, `z`.
*   **MongoDB**: MongoDB's default sort behavior for the test set aligned with the desired US-ASCII order, matching the results observed in most programming languages.
*   **Redis**: Redis's `SORT ... ALPHA` command also showed a deviation, placing `_` before `3` and lowercase letters before uppercase, similar to the default SQL, C#, and Visual Basic results.

## Conclusion

The testing confirms that the Base64 Sortable alphabet generally achieves its goal of producing lexicographically sortable output when standard US-ASCII or C-locale string comparison rules are applied. Implementers should be aware that certain programming languages (e.g., C#, Visual Basic) and database systems (e.g., default SQL collations, Redis) may employ locale-aware or alternative default collation rules that can alter the sort order. To ensure consistent binary-equivalent sorting, explicit specification of ordinal or C-locale collation may be necessary in environments where such options are available.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# IANA Considerations {#iana_considerations}

This document has no IANA actions.

--- back

# Changelog {#changelog}

{:removeInRfc}

draft-00:

{: spacing="compact"}

- Initial Release

# Test Vectors {#test_vectors}

The following text vectors start with a base of those found in {{RFC4648}} and then add additional values that illustrate decoding of specific values and the usage of the checksum.

## Encoding {#test_vectors_encode}

| Input       | Encoded Data | Checksum? | Input Type |
|-------------|--------------|-----------|------------|
| `""`        | `""`         | N         | Text       |
| `f`         | `OV`         | N         | Text       |
| `fo`        | `Oaw`        | N         | Text       |
| `foo`       | `Oaxj`       | N         | Text       |
| `foobar`    | `OaxjNa4m`   | N         | Text       |
| `Hello World` | `H5KgQ5xr74SjRalZ` | N         | Text       |
{: #encodeTable title='Base64 Sortable Encode Table'}

## Decoding {#test_vectors_decode}

| Input         | Decoded Data  | Test Info                       |
|---------------|---------------|---------------------------------|
| `""`          | `""`          | Empty string                    |
| `OV`          | `f`           | Single byte input               |
| `Oaw`         | `fo`          | Two bytes input                 |
| `Oaxj`        | `foo`         | Three bytes input               |
| `OaxjNa4m`    | `foobar`      | Original 'foobar' example       |
| `H5KgQ5xr74SjRalZ` | `Hello World` | Common phrase                   |
{: #decodeTable title='Base64 Sortable Decode Table'}

