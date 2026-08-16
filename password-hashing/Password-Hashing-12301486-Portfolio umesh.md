# Password Hashing Portfolio — Student ID 12301486

**Aim:** Compare password hashing algorithms by creating users with different hash types, examining `/etc/shadow`, and configuring PAM password policies — then observe cracking speed differences using a password cracker.

---

## Setup

A single Ubuntu host, `Target`, was added and started for this activity.

---

## Examine Hash Algorithms

Three users were created on `Target`, all with the same student-ID password (`pass12301486`), each hashed with a different algorithm using `mkpasswd` and `useradd -p`: `user_md5` (MD5-crypt), `user_sha512` (SHA-512), and `user_yescrypt` (yescrypt).

![mkpasswd and useradd generating the three hash types, and the resulting /etc/shadow entries](pw-images/Password-Hashing-12301486-shadow.png)

`/etc/shadow` was then examined with `grep '^user_' /etc/shadow`, showing the three distinct algorithm prefixes and salts:

```
user_md5:$1$Agp1h.mQ$u90mdHmZyzHkh5kKjaPPm0:20680:0:99999:7:::
user_sha512:$6$3LEgnTeeWV8Vvftr$xgLNyZ3YR81j2mF/kiFJx10v6XvoCLZsS954djxZtCv...:20680:0:99999:7:::
user_yescrypt:$y$j9T$7N6Iz6g41jj6L.cNr2.Uv.$lR8WsthLGmOv7v1M531Z3yoV.OL...:20680:0:99999:7:::
```

The three original hashes were left untouched throughout the rest of the activity, as instructed, so they remained valid for the cracking comparison later.

---

## Configure PAM Policies

### Password quality (`pam_pwquality`)

`/etc/pam.d/common-password` was backed up, then edited so that `pam_pwquality.so` enforces `retry=3 minlen=12 ucredit=-1 dcredit=-1 enforce_for_root dictcheck=0` (12-character minimum, at least one uppercase letter, at least one digit; `dictcheck=0` because this image ships no cracklib word-list, and `enforce_for_root` so the policy is not merely a warning on the root console).

![Backing up common-password and confirming the pam_pwquality line](pw-images/Password-Hashing-12301486-pam-quality.png)

### Account lockout (`pam_faillock`)

`/etc/pam.d/common-auth` was backed up, then edited to wire in `pam_faillock` around the existing `pam_unix.so` line — a `preauth` call before it, and `authfail`/`authsucc` calls after it — with `deny = 5` and `unlock_time = 300` set in `/etc/security/faillock.conf`.

![The wired-in common-auth file with pam_faillock preauth/authfail/authsucc in the correct order](pw-images/Password-Hashing-12301486-pam-faillock.png)

---

## Testing the Policies Against `user_test`

A throwaway account, `user_test`, was created for policy testing so the three algorithm users' recorded hashes stayed untouched.

![Creating user_test and confirming its uid/gid after passwd succeeds](pw-images/Password-Hashing-12301486-user-test.png)

**Password quality:** a weak password (`abc`) was rejected by `pam_pwquality` (too short / missing a digit), and a compliant password (12+ characters, an uppercase letter, a digit) was then accepted.

![Testing weak and compliant passwords against user_test, and confirming the pam_pwquality and pam_faillock lines](pw-images/Password-Hashing-12301486-password-policy-test.png)

**Account lockout:** because the console is logged in as `root`, which is exempt from authentication, the counter was tripped by first becoming `user_test` (free, uncounted) and then re-authenticating as `user_test` *from that shell* with a wrong password, repeated five times.

![Five failed su - user_test re-authentications from the user_test shell](pw-images/Password-Hashing-12301486-lockout-test.png)

`faillock --user user_test` was then run from the root console to check the lockout status, before clearing it with `faillock --user user_test --reset`.

![Checking the faillock status for user_test after the failed attempts](pw-images/Password-Hashing-12301486-faillock-status.png)

---

## Verify — Cracking the Hashes

The word `password` was confirmed present in `rockyou.txt`, and three more users — `crack_md5`, `crack_sha512`, `crack_yescrypt` — were created with that password, one per algorithm. John the Ripper was then run against `hashes.txt` (extracted from `/etc/shadow`), first with auto-detection (which cracked the MD5 hash immediately) and then with `--format=crypt` for the SHA-512 and yescrypt hashes.

![John the Ripper cracking crack_md5, crack_sha512 and crack_yescrypt, with timing for each run](pw-images/Password-Hashing-12301486-crack.png)

`john --show --format=crypt hashes.txt` confirmed all three were cracked:

```
crack_md5:password:20680:0:99999:7:::
crack_sha512:password:20680:0:99999:7:::
crack_yescrypt:password:20680:0:99999:7:::

3 password hashes cracked, 0 left
```

The MD5-crypt hash was cracked almost instantly (`0:00:00:00`, 100%, ~1422 candidates/second), while the SHA-512 and yescrypt hashes together took a real time of 9.827 seconds at a much lower rate (~10.76–21.52 candidates/second) — a clear, direct demonstration of how much more expensive the slower algorithms are to attack, even at this small scale.

---

## Questions

### Q1: What do the prefixes `$1$`, `$6$`, and `$y$` in `/etc/shadow` indicate, and why does the algorithm choice matter for password security?

The prefix at the start of the hash field identifies which hashing algorithm was used to produce it: `$1$` is MD5-crypt, `$6$` is SHA-512-crypt, and `$y$` is yescrypt. This matters because these algorithms differ hugely in how expensive they are to compute — MD5-crypt is a fast, simple hash originally designed for speed rather than resisting attackers, while SHA-512-crypt is deliberately iterated many times to slow it down. yescrypt goes further again: it is a memory-hard algorithm, meaning it is expensive in both CPU time and memory, which specifically blunts the advantage that GPUs and custom cracking hardware normally have over a regular server. The cracking results above show this directly — the MD5 hash fell almost instantly, while the SHA-512 and yescrypt hashes took noticeably longer for the same password.

### Q2: Why is a slow hashing algorithm (like yescrypt or bcrypt) preferred for password storage, even though it uses more CPU time on the server during legitimate logins?

A legitimate login only ever computes one hash per attempt, so even an algorithm that takes a fraction of a second is unnoticeable to a real user. An attacker running a dictionary or brute-force attack, however, needs to compute the hash function millions or billions of times to work through a wordlist or keyspace. Making each individual hash computation slower and more memory-intensive multiplies that total attack cost enormously, while barely affecting the one-off cost paid by a genuine login. That asymmetry — negligible cost for the defender, massive cost for the attacker — is exactly why slow, memory-hard algorithms like yescrypt and bcrypt are preferred for password storage over fast general-purpose hashes.

### Q3: What is a salt in the context of password hashing, and what attack does it prevent? Identify the salt in one of your `/etc/shadow` entries.

A salt is a random value generated per-user and stored alongside the hash, which is combined with the password before hashing so that the same password produces a different stored hash for every user. This prevents precomputed lookup-table and rainbow-table attacks: without a salt, an attacker could hash an entire wordlist once and instantly reverse any matching hash in a breached database, and identical passwords across different users would be immediately visible from matching hashes. With a per-user salt, the attacker has to redo the work for every single hash individually. In the `user_sha512` entry, `$6$3LEgnTeeWV8Vvftr$xgLNyZ3YR81j2mF/kiFJx10v6XvoCLZsS954djxZtCv...`, the salt is the segment between the second and third `$` — `3LEgnTeeWV8Vvftr`.

### Q4: The `pam_faillock` module locks accounts after repeated failures. What is a potential downside of this approach, and how might an attacker exploit it?

The main downside is that lockout policies protect against password guessing at the cost of availability: because the lock triggers on failed attempts regardless of who is making them, an attacker who does not know — and has no intention of guessing — a legitimate user's password can still deliberately submit a handful of wrong passwords against that account purely to lock it out. This turns `pam_faillock` into a denial-of-service tool rather than a defence: the attacker never needs to succeed, only to fail five times, to lock a real user out of their own account for the configured `unlock_time` (300 seconds here, but potentially repeated indefinitely to keep the account locked). This is a genuine trade-off in the design, and is one reason lockout policies are sometimes paired with other measures, such as per-source rate limiting, rather than being relied on in isolation.

---

## Reflection

This activity made the practical difference between hashing algorithms far more concrete than reading about them in the abstract. Generating the same password under three different algorithms and then watching them get cracked at very different speeds — the MD5 hash falling almost instantly while the SHA-512 and yescrypt hashes together still took under ten seconds even for a password straight out of `rockyou.txt` — was a genuinely useful demonstration of exactly why algorithm choice matters, and it made the deliberate design trade-off of slow, memory-hard hashing feel obvious rather than theoretical.

Configuring `pam_pwquality` and `pam_faillock` also reinforced how order-sensitive and easy to misconfigure PAM stacks are: the `dictcheck=0` requirement, the `enforce_for_root` flag, and the exact placement of the `pam_faillock` lines around `pam_unix.so` in `common-auth` were all details that would silently break the intended behaviour if missed — a *valid* password erroring out instead of being accepted, or a weak password on the root console succeeding with only a warning, or failed attempts being counted but never actually enforced. Backing up `common-auth` before editing it turned out to be a sensible precaution given how easily a single misplaced line can lock authentication itself.

Testing the lockout policy was the most counter-intuitive part: needing to become `user_test` first (since root is exempt from authentication) and then re-authenticate as `user_test` *from within that shell* to actually trip the counter was not something I would have guessed without the hint, but it made the distinction between "authenticated as root" and "authenticated as the target user" very clear.

## Conclusion

This activity successfully compared three password hashing algorithms — MD5-crypt, SHA-512-crypt, and yescrypt — by creating matching users, examining their `/etc/shadow` entries, and cracking a shared, wordlist-guessable password under each algorithm with John the Ripper. The results showed a clear, measurable slowdown from MD5 through to yescrypt, directly illustrating why modern systems default to slow, memory-hard hashing for password storage. Alongside this, `pam_pwquality` was configured to enforce a minimum-length, mixed-character password policy, and `pam_faillock` was configured to lock accounts after five failed attempts, with both policies verified against a disposable `user_test` account rather than the algorithm-comparison users. Together, the tasks met the aim of the activity: a working demonstration of how hash algorithm choice, salting, and PAM-enforced password policy each contribute to — and each have limits within — a system's overall resistance to password attacks.
