# Password Hashing — Activity Preparation Portfolio

**Aim (from the Password Hashing lab instructions):** Demonstrate the differences in password hashing algorithms: create users with different hashing algorithms and look at the differences in the file /etc/shadow; configure PAM password policies; and watch differences in the speed of cracking passwords with a password cracker (John the Ripper or hashcat).
---

## Interactive Activities

Two interactive activities were completed as preparation before starting the GNS3 lab.

### Password Strength & Entropy

Stronger, True/False, Entropy ordering and identification of mistakes, in 8 rounds!

![Password Strength & Entropy — 4/8 (50%)](pw2-images/Screenshot_2026-08-16_114011.png)

**Score: 4/8 (50%).** This was scored as 4/8 (50%) and the feedback took the user back to the Password Strength explainer to reinforce the concepts (probably entropy ordering and common password pitfalls) and then attempt again.

### Authentication Sequence

![Authentication Sequence — 8/8 (100%)](pw2-images/Screenshot_2026-08-16_114556.png)

**Score: 8/8 (100%).** A very good score – very good understanding of authentication basics, and preparation to proceed with MFA and authentication protocols.

---

## Password Hashing Lab — Task Overview

There are two activities above that address fundamental concepts (password strength/entropy and authentication flow) that are meant to be paired with the Password Hashing lab which is organized as follows, according to the instructions:


Add one Ubuntu host, `Target`.
2. Generate three users using the same student-ID password, with different hashing methods: MD5 (`md5crypt`), SHA-512 and yescrypt.
3. Look at /etc/shadow, find the algorithm prefix, salt and hash used for each user and document the entire hash string.
4. Set up the following: `pam_pwquality` (with a minimum length of 12 characters, an upper case letter, a digit), and `pam_faillock` (lock after 5 failed attempts for 300 seconds).
5. Test both the policies with a disposable `user_test` account, a weak password is denied, and a compliant one is accepted, and a lockout is applied with 5 failed re-authentications.
Make another set of users (crack_md5, crack_sha512, crack_yescrypt), where the password is known to be in rockyou.txt, and then crack each of them using john or hashcat and compare the timings of the algorithms.

This lab has not been executed, no terminal evidence (screenshots of the network, entries in /etc/shadow, PAM configuration, etc.) has been created yet, nor has any cracking output been created. That evidence should then be included with these activities as the portfolio's main body once they have been completed.

---

## Reflection

The Password Strength & Entropy activity was the more useful of the two, just because it failed first — a 50% score is a clear reminder that entropy ordering and picking out common password mistakes do not make them as easy to use as they sound; something that's useful to know before the lab asks you for password-policy configuration and cracking comparisons based on that assumption. The Authentication Sequence, on the other hand, confirmed that the overall process in which authentication is performed is sound and will make it easier to follow conceptually on the PAM configuration side of the lab (`pam_pwquality`, `pam_faillock`).

## Conclusion

Currently, there are two prep activities in this portfolio for Password Hashing lab; one 4/8 on Password Strength & Entropy, and one 8/8 on Authentication Sequence, both of which were flagged for review. The weaker suggests that concepts such as entropy and password-strength may be reexamined in light of these ideas prior to the use in the lab for the cracking-comparison task. Above are the instructions for performing the lab, but it has not been finished yet; the next steps are to conduct this lab and submit the work to this portfolio (the `/etc/shadow` screenshot, PAM configuration, and output from the cracking tool).
