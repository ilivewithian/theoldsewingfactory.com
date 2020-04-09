---
layout: post
title: Hashing Passwords
date: 2018-05-05
---

Last October I made a utility website [passwordhashing.com](https://passwordhashing.com/), it takes 
plain text and converts it into one of  [BCrypt](https://passwordhashing.com/BCrypt), 
[PBKDF2](https://passwordhashing.com/PBKDF2), [SHA1](https://passwordhashing.com/SHA1), 
[SHA256](https://passwordhashing.com/SHA256), [SHA384](https://passwordhashing.com/SHA384) or 
[SHA512](https://passwordhashing.com/SHA512).

It's not a high traffic site, but what's interesting is that 94% of the requests are for bcrypt hashes. What's not clear is why. I'd like to think that bcrypt is
now the more popular hashing algorithm, it's a happy thought, but I can't claim
this site is big enough to evidence that. I suspect the reality is that someone
has linked to the tool from a forum where someone needed a bcrypt hash to reset
an admin password in magento, or some other cms tool. ¯\\_(ツ)_/¯. Here's the breakdown of requests:

| Url 	        | % of Views 	|
| ---	        | ----------	|
| /	            | 1.26% |
| /BCrypt	    | 94.11% | 
| /PBKDF2	    | 1.69% |
| /SHA1	        | 1.14% |
| /SHA256	    | 0.72% |
| /SHA384	    | 0.26% |
| /SHA512	    | 0.79% |
| /translate_c	| 0.03% |

TIL the /translate_c url is because someone is mirroring my site in another language.
