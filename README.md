# Magic hashes – PHP hash "collisions"

Register with *password 1* and then sign in with *password 2*. If you're in then the storage uses specified algorithm to hash the password and PHP uses `==` to compare them (for MD5, SHA-1, and plaintext).

## [MD5](md5.md), [SHA-1](sha1.md), [SHA-224](sha224.md), [SHA-256](sha256.md)
For **MD5**, **SHA-1** and **SHA-2 family**, it uses the long-known trick (it actually is a documented feature, see [*PHP type comparison tables*](https://php.net/types.comparisons) & [*Floating point numbers*](https://php.net/types.float)) that for PHP `'0e1' == '00e2' == '0'`, it just uses it for *practical* purposes. Any password *matches* any other password from the list. This is a different trick than *integral strings overflowing into floating point numbers*, just spot the difference between [these two lines](https://3v4l.org/581R5).

## [Plaintext](plaintext.md)
For **plaintext**, it uses various conversion tricks. First password will *match* just the second one. Tricks are grouped by PHP versions allowing them.

## [bcrypt](bcrypt.md)
**bcrypt** truncates passwords to a maximum length of 72 characters. The passwords *match* if the first 72 characters of both passwords match.

## [PBKDF2](pbkdf2.md)
If you use a password longer than 64 bytes and hash it with **PBKDF2-HMAC-SHA1**, it is first *pre-hashed* with SHA1, so `PBKDF2-HMAC-SHA1(password1) === PBKDF2-HMAC-SHA1(password2)` because `sha1(password1) === bin2hex(password2)`.

## [Tiger/192,3](tiger192,3.md)
Right now there's just one magic hash in each thanks to [Norbert Tihanyi](https://twitter.com/TihanyiNorbert), more will be hopefully added in the future.

### Conclusion
Use `===` when comparing anything* in PHP, not `==`. And use [`password_hash()`](https://php.net/function.password-hash) and [`password_verify()`](https://php.net/function.password-verify) for password hashing in PHP, don't use MD5 or SHA-1. *Use [`hash_equals()`](https://php.net/function.hash-equals) when comparing hashes.

### History
It all started with [this tweet](https://twitter.com/spazef0rze/status/439352552443084800), I've generated `QNKCDZO` and `240610708` in February 2014 and it has since spread all over the intertubes. Just [google it](https://www.google.cz/search?q=%22QNKCDZO%22).

- MD5: [Tweet](https://twitter.com/spazef0rze/status/439352552443084800), [code](http://3v4l.org/2vrMi), contributions by [roycewilliams](https://github.com/spaze/hashes/pull/4), [Norbert Tihanyi](https://twitter.com/TihanyiNorbert)
- SHA-1: [Tweet](https://twitter.com/spazef0rze/status/523010190900469760), [code](http://3v4l.org/tT4l8), contributions by [roycewilliams](https://github.com/spaze/hashes/pull/4)
- Plaintext: [Tweet](https://twitter.com/spazef0rze/status/522882677452832768), [code](http://3v4l.org/K3ljr)
- bcrypt: [code](https://3v4l.org/2qc8j)
- PBKDF2 by [Christian "CodesInChaos" Winnerlein](https://twitter.com/CodesInChaos/status/422073818228613121), [as explained by Mathias Bynens](https://mathiasbynens.be/notes/pbkdf2-hmac), [code](https://3v4l.org/RROMo)
- Tiger/192,3 by [Norbert Tihanyi](https://twitter.com/TihanyiNorbert/status/1133436583183568901)
- SHA-224 by [Norbert Tihanyi](https://twitter.com/TihanyiNorbert/status/1138075224010833921), [hops](https://github.com/spaze/hashes/pull/2)
- SHA-256 by [Norbert Tihanyi](https://twitter.com/TihanyiNorbert/status/1148586399207178241), [Chick3nman](https://github.com/spaze/hashes/pull/3), [roycewilliams](https://github.com/spaze/hashes/pull/4)
