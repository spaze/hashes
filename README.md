# Magic hashes – PHP hash "collisions"

Register with *password 1* and then sign in with *password 2*. If you're in then the storage uses specified algorithm to hash the password and PHP uses `==` to compare them (for MD5, SHA-1, and plaintext).

## [MD5](md5.md), [SHA-1](sha1.md), [SHA-224](sha224.md), [SHA-256](sha256.md) and others
For **MD5**, **SHA-1** and **SHA-2 family**, it uses the long-known trick (it actually is a documented feature, see [*PHP type comparison tables*](https://php.net/types.comparisons) & [*Floating point numbers*](https://php.net/types.float)) that for PHP `'0e1' == '00e2' == '0'`, it just uses it for *practical* purposes. Any password *matches* any other password from the list. This is a different trick than *integral strings overflowing into floating point numbers*, just spot the difference between [these two lines](https://3v4l.org/581R5).

These are all the algorithms with magic hashes:
- [CRC32](crc32.md)
- [CRC32b](crc32b.md)
- [FNV-1a/32](fnv1a32.md)
- [FNV-1a/64](fnv1a64.md)
- [FNV-1/32](fnv132.md)
- [FNV-1/64](fnv164.md)
- [HAVAL-128,3](haval128,3.md)
- [HAVAL-128,4](haval128,4.md)
- [HAVAL-128,5](haval128,5.md)
- [HAVAL-160,3](haval160,3.md)
- [HAVAL-160,4](haval160,4.md)
- [HAVAL-160,5](haval160,5.md)
- [JOAAT](joaat.md)
- [MD2](md2.md)
- [MD4](md4.md)
- [MD5](md5.md)
- [RIPEMD-128](ripemd128.md)
- [RIPEMD-160](ripemd160.md)
- [SHA-1](sha1.md)
- [SHA-224](sha224.md)
- [SHA-256](sha256.md)
- [Tiger/128,3](tiger128,3.md)
- [Tiger/128,4](tiger128,4.md)
- [Tiger/160,3](tiger160,3.md)
- [Tiger/160,4](tiger160,4.md)
- [Tiger/192,3](tiger192,3.md)

To quote @0xb0bb, "there are other applications for magic hashes other than password comparisons (such as caching layers or data derived from the output of a hash function) where these known insecure, lesser known and pseudo-hash algorithms can be found more readily."

## [Plaintext](plaintext.md)
For **plaintext**, it uses various conversion tricks. First password will *match* just the second one. Tricks are grouped by PHP versions allowing them.

## [bcrypt](bcrypt.md)
**bcrypt** truncates passwords to a maximum length of 72 characters. The passwords *match* if the first 72 characters of both passwords match.

## [PBKDF2-HMAC-SHA1](pbkdf2-sha1.md), [PBKDF2-HMAC-SHA224](pbkdf2-sha224.md), [PBKDF2-HMAC-SHA256](pbkdf2-sha256.md)
If you use a password longer than 64 bytes and hash it with **PBKDF2-HMAC-SHA1**, it is first *pre-hashed* with SHA1, so `PBKDF2-HMAC-SHA1(password1) === PBKDF2-HMAC-SHA1(password2)` because `sha1(password1) === bin2hex(password2)`. The similar *pre-hashing* is applied in case of **PBKDF2-HMAC-SHA224** and **PBKDF2-HMAC-SHA256**.

## [Tiger/192,3](tiger192,3.md)
Right now there's just one magic hash in each thanks to [Norbert Tihanyi](https://twitter.com/TihanyiNorbert), more will be hopefully added in the future.

### Conclusion
Use `===` when comparing anything* in PHP, not `==`. And use [`password_hash()`](https://php.net/function.password-hash) and [`password_verify()`](https://php.net/function.password-verify) for password hashing in PHP, don't use MD5 or SHA-1. *Use [`hash_equals()`](https://php.net/function.hash-equals) when comparing hashes.

### History
It all started with [this tweet](https://twitter.com/spazef0rze/status/439352552443084800), I've generated `QNKCDZO` and `240610708` in February 2014 and it has since spread all over the intertubes. Just [google it](https://www.google.cz/search?q=%22QNKCDZO%22).

- MD5: [Tweet](https://twitter.com/spazef0rze/status/439352552443084800), [code](http://3v4l.org/2vrMi), contributions by [roycewilliams](https://github.com/spaze/hashes/pull/4), [Norbert Tihanyi](https://twitter.com/TihanyiNorbert), [ethernetlord](https://github.com/ethernetlord), [0xb0bb](https://github.com/0xb0bb)
- SHA-1: [Tweet](https://twitter.com/spazef0rze/status/523010190900469760), [code](http://3v4l.org/tT4l8), contributions by [roycewilliams](https://github.com/spaze/hashes/pull/4), [0xb0bb](https://github.com/0xb0bb)
- Plaintext: [Tweet](https://twitter.com/spazef0rze/status/522882677452832768), [code](http://3v4l.org/K3ljr)
- bcrypt: [code](https://3v4l.org/2qc8j)
- PBKDF2-HMAC-SHA1 by [Christian "CodesInChaos" Winnerlein](https://twitter.com/CodesInChaos/status/422073818228613121), [as explained by Mathias Bynens](https://mathiasbynens.be/notes/pbkdf2-hmac), [code](https://3v4l.org/RROMo)
- PBKDF2-HMAC-SHA224 by [Norbert Tihanyi](https://twitter.com/TihanyiNorbert/status/1158643999089053696), [code](https://3v4l.org/kuAPb)
- PBKDF2-HMAC-SHA256 by [Jens 'atom' Steube](https://twitter.com/hashcat/status/1158788752657047552), [code](https://3v4l.org/p4AqD)
- Tiger/192,3 by [Norbert Tihanyi](https://twitter.com/TihanyiNorbert/status/1133436583183568901)
- SHA-224 by [Norbert Tihanyi](https://twitter.com/TihanyiNorbert/status/1138075224010833921), [hops](https://github.com/spaze/hashes/pull/2), [0xb0bb](https://github.com/0xb0bb)
- SHA-256 by [Norbert Tihanyi](https://twitter.com/TihanyiNorbert/status/1148586399207178241), [Chick3nman](https://github.com/spaze/hashes/pull/3), [roycewilliams](https://github.com/spaze/hashes/pull/4), [matlink](https://github.com/spaze/hashes/pull/13)
- CRC32, CRC32b, FNV-1a/32, FNV-1a/64, FNV-1/32, FNV-1/64, HAVAL-128,3, HAVAL-128,4, HAVAL-128,5, HAVAL-160,3, HAVAL-160,4, HAVAL-160,5, JOAAT, MD2, MD4, RIPEMD-128, RIPEMD-160, Tiger/128,3, Tiger/128,4, Tiger/160,3, Tiger/160,4 by [0xb0bb](https://github.com/0xb0bb)

### How to calculate your own
I've used my laptop, few `for` (or `foreach`?) loops, many CPU cycles and [an external fan](https://twitter.com/spazef0rze/status/1150086642113437697) back in 2014 but today you can/should use a GPU and a modified hashcat for that. See [this write-up](https://grocid.net/2019/08/03/finding-magic-hashes-with-hashcat/) by [Carl Löndahl](https://twitter.com/carllondahl) and [0xb0bb](https://twitter.com/0xb0bb).

Chick3nman & co. is also [working](https://twitter.com/Chick3nman512/status/1157748868823621638) on their version of hashcat, stay tuned.
