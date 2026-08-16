# Password Hashing — Activity Preparation Portfolio

**Aim (from the Password Hashing lab instructions):** Compare password hashing algorithms by creating users with different hash types, examining `/etc/shadow`, and configuring PAM password policies — then observe cracking speed differences using a password cracker (John the Ripper or hashcat).

---

## Interactive Activities

Two interactive activities were completed as preparation before starting the GNS3 lab.

### Password Strength & Entropy

8 rounds covering strength rating, true/false, entropy ordering, and mistake identification.

![Password Strength & Entropy — 4/8 (50%)](pw2-images/Screenshot_2026-08-16_114011.png)

**Score: 4/8 (50%).** The result flagged this as an area to revisit — the feedback pointed back to the Password Strength explainer to reinforce the concepts (likely entropy ordering and spotting common password mistakes) before trying again.

### Authentication Sequence

![Authentication Sequence — 8/8 (100%)](pw2-images/Screenshot_2026-08-16_114556.png)

**Score: 8/8 (100%).** A strong result, with feedback confirming a solid grasp of authentication fundamentals and readiness to move on to MFA and authentication protocols.

---

## Password Hashing Lab — Task Overview

The two activities above cover foundational concepts (password strength/entropy and authentication flow) that sit alongside the Password Hashing lab, which is structured as follows per the instructions:

1. Add a single Ubuntu host, `Target`.
2. Create three users with the same student-ID password, each hashed with a different algorithm: MD5 (`md5crypt`), SHA-512, and yescrypt.
3. Examine `/etc/shadow`, identify the algorithm prefix, salt, and hash for each user, and record the full hash strings.
4. Configure `pam_pwquality` (12-character minimum, an uppercase letter, a digit) and `pam_faillock` (lock after 5 failed attempts for 300 seconds).
5. Test both policies against a disposable `user_test` account — a weak password rejected then a compliant one accepted, and a lockout triggered by five failed re-authentications.
6. Create a second set of users (`crack_md5`, `crack_sha512`, `crack_yescrypt`) with a password confirmed to be in `rockyou.txt`, then crack each with John the Ripper or hashcat and compare timing across algorithms.

This lab has not yet been run — no terminal evidence (network screenshot, `/etc/shadow` entries, PAM configuration, or cracking output) has been produced yet. Once completed, that evidence should be added alongside these activities as the main body of the portfolio.

---

## Reflection

The Password Strength & Entropy activity was the more useful of the two precisely because it went badly first — a 50% score is a clear signal that entropy ordering and picking out common password mistakes aren't as intuitive as they seem, which is worth knowing before the lab asks for password-policy configuration and cracking comparisons that depend on that same reasoning. The Authentication Sequence activity, by contrast, confirmed that the general flow of how authentication happens is already solid, which should make the PAM configuration side of the lab (`pam_pwquality`, `pam_faillock`) easier to follow conceptually, even though the two topics are only loosely connected.

## Conclusion

This portfolio currently documents two preparatory activities for the Password Hashing lab: a 4/8 attempt at Password Strength & Entropy, flagged for review, and a clean 8/8 on Authentication Sequence. The weaker result points to entropy and password-strength concepts as worth revisiting before relying on them in the lab's cracking-comparison task. The lab itself — creating the three hash-type users, examining `/etc/shadow`, configuring PAM policies, and cracking a matched set of hashes to compare algorithm speed — is outlined above from the instructions but has not yet been carried out; the next step is to complete that lab and add its evidence (the `/etc/shadow` screenshot, PAM configuration, and cracking tool output) to this portfolio.
