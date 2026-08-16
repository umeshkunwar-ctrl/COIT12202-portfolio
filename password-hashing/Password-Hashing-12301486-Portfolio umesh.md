# Password Hashing Portfolio — Student ID 12301486

**Aim:**Learn how to compare password hashing algorithms by creating users using different hash methods, viewing /etc/shadow, and setting up password policies in PAM — and check the difference in cracking speeds with a password cracker.

---

## Setup

For this activity, a single Ubuntu host (Target) was added and started.

---

## Examine Hash Algorithms


The three users were added to the system using the same student-ID password (pass12301486) and with a different algorithms (`mkpasswd -d -a md5 pass12301486 | useradd -p -; mkpasswd -d -a sha512 pass12301486 | useradd -p -; mkpasswd -d -a yescrypt pass12301486 | useradd -p -`), using `MD5-crypt`, `SHA-512`, and `yescrypt`, respectively.

![mkpasswd and useradd generating the three hash types, and the resulting /etc/shadow entries](pw-images/Password-Hashing-12301486-shadow.png)

`/etc/shadow` was then examined with `grep '^user_' /etc/shadow`, showing the three distinct algorithm prefixes and salts:

```
user_md5:$1$Agp1h.mQ$u90mdHmZyzHkh5kKjaPPm0:20680:0:99999:7:::
user_sha512:$6$3LEgnTeeWV8Vvftr$xgLNyZ3YR81j2mF/kiFJx10v6XvoCLZsS954djxZtCv...:20680:0:99999:7:::
user_yescrypt:$y$j9T$7N6Iz6g41jj6L.cNr2.Uv.$lR8WsthLGmOv7v1M531Z3yoV.OL...:20680:0:99999:7:::
```

The three original hashes were not modified throughout the activity as directed, and still could be used for the comparison of the values later in the activity when the values would be cracked.

---

## Configure PAM Policies

### Password quality (`pam_pwquality`)

The file /etc/pam.d/common-password was backed up, and then modified to include: pam_pwquality.so enforce_for_root=1 retry=3 minlen=12 ucredit=-1 dcredit=-1 dictcheck=0 (12 characters required, one must be upper case, one a digit, and the wordlist is not present; this image is built without any cracklib wordlist, hence the setting of dictcheck=0, and the setting of ucredit=-1 and dcredit=-1 means that the policy is not just a warning on the root console).

![Backing up common-password and confirming the pam_pwquality line](pw-images/Password-Hashing-12301486-pam-quality.png)

### Account lockout (`pam_faillock`)

The file /etc/pam.d/common-auth was backed up and then edited to include `pam_faillock` calls around the existing `pam_unix.so` call; one before and one after; with `deny = 5` and `unlock_time = 300` in `/etc/security/faillock.conf`.

![The wired-in common-auth file with pam_faillock preauth/authfail/authsucc in the correct order](pw-images/Password-Hashing-12301486-pam-faillock.png)

---

## Testing the Policies Against `user_test`

A throwaway account, user_test, was created to test policies; this way, the hashes recorded by the three algorithm users did not get modified.

![Creating user_test and confirming its uid/gid after passwd succeeds](pw-images/Password-Hashing-12301486-user-test.png)

**Password quality:** A weak password ("abc") was rejected by the "pam_pwquality" module, because it was too short / did not contain a digit; a compliant password (12+ characters, contains an uppercase letter, contains a digit) was then accepted.

![Testing weak and compliant passwords against user_test, and confirming the pam_pwquality and pam_faillock lines](pw-images/Password-Hashing-12301486-password-policy-test.png)

**Account lockout:** Note that the counter was activated by being put into the `user_test` (free, uncounted) before re-authenticating as user_test (again free, but this time with incorrect password) five times, since the console is logged in as root, and is not required to authenticate..

![Five failed su - user_test re-authentications from the user_test shell](pw-images/Password-Hashing-12301486-lockout-test.png)

`faillock --user user_test` was then run from the root console to check the lockout status, before clearing it with `faillock --user user_test --reset`.

![Checking the faillock status for user_test after the failed attempts](pw-images/Password-Hashing-12301486-faillock-status.png)

---

## Verify — Cracking the Hashes

The word `password` was found in `rockyou.txt`, and three more users were added to the system with that as the password: `crack_md5`, `crack_sha512`, and `crack_yescrypt` each for one of those algorithms. John the Ripper was then executed against `hashes.txt` (which contains hashes from `/etc/shadow`), first using the auto-detection mode, and then with `--format=crypt` for the SHA-512 and yescrypt hash.

![John the Ripper cracking crack_md5, crack_sha512 and crack_yescrypt, with timing for each run](pw-images/Password-Hashing-12301486-crack.png)

`john --show --format=crypt hashes.txt` confirmed all three were cracked:

```
crack_md5:password:20680:0:99999:7:::
crack_sha512:password:20680:0:99999:7:::
crack_yescrypt:password:20680:0:99999:7:::

3 password hashes cracked, 0 left
```

The MD5-crypt hash was cracked nearly instantaneously (~100%), and the SHA-512 and yescrypt hashes together required a realtime of 9.827 seconds, at a much lower speed (~10.76 – 21.52 candidates/second), which is a direct proof of the increased cost of the slower algorithms to attack.

---
### Q1. What do the prefixes `$1$`, `$6$` and`$y$` in `/etc/shadow` represent and how does the choice of algorithm affect password safety?

The prefix at the beginning of the hash section shows which hashing mechanism was employed for the hash: `$1$` signifies MD5-crypt, `$6$` symbolizes SHA-512-crypt whereas `$y$` refers to yescrypt. This is important because different algorithms have different running requirements and principles of implementation — MD5-crypt is an old technique designed to work as fast as possible and being easy to use; SHA-512-crypt is a technique which is called upon many times to delay the operation. Yescrypt is even more advanced — it is a memory-hard algorithm that consumes resources excessively thus diminishing the advantages that special-purpose hardware devices have over normal server. The experiment results illustrate it well — MD5 hash is cracked within seconds, while SHA-512 and yescrypt hashes take much more time even with the identical passwords.
### Q2: When is the slower hashing algorithm (for example: yescrypt or bcrypt) a better option for password storing knowing that it will result in a heavier server load?

 
When a legitimate user attempts to log in, only a single hash is calculated for that. Therefore, even if a hashing algorithm takes a fraction of a second to execute, it will go unnoticed for a real user. On the other hand, an attacker who uses a dictionary or a brute force attack needs to recalculate the hash multiple times and run through millions or billions of possibilities using the keys. Thus, a slow hashing algorithm makes it possible for legitimate users to pay very little in case of a single computation, whereas attackers will have to pay a lot due to multiple computations. This is the reason why slow and memory-intensive hashing algorithms are more preferable than fast algorithms.

What is a salt and what type of attack does it thwart when hashing passwords? Find out the name of the salt in one of the entries of the /etc/shadow file.

The salt is a random value that is generated for each user and is stored attached to the hash, and is used with the password before hashing, so that a user's password is mapped to a different stored hash each time they log in. This will prevent precomputed lookup table and rainbow table attacks: if there is no salt, then an attacker can hash a whole word list once and then be able to generate the password for any matching hash in a compromised database and any matching password across the two different users will be immediately evident. If the attacker has a per-user salt, he must repeat the computation for each hash he guesses. In the `user_sha512` entry, `$6$3LEgnTeeWV8Vvftr$xgLNyZ3YR81j2mF/kiFJx10v6XvoCLZsS954djxZtCv...`, the salt is the segment between the second and third `$` — `3LEgnTeeWV8Vvftr`.

### Q4: The module `pam_faillock` is designed to stop the account from being accessed by unwanted people after several unsuccessful login attempts. However, what is the disadvantage of this method, and how can attackers benefit from it?

The disadvantage is that the lock mechanism is useful in preventing successful password guessing, while compromising the availability of the account. The reason for this is that the lock mechanism gets activated every time a wrong password is used, which means that the person attempting to perform a guessing attack does not need to know any actual passwords to be successful. Thus `pam_faillock` turns from the security system into a denial-of-service attack tool. The main thing is that attackers do not need to guess anything correctly.  A number of failed attempts is enough for a successful lock of a legitimate user account for a specific period of time.

---

## Reflection

This exercise provided a clear understanding and helped put hashing algorithms into practice while comparing how fast the three hashing algorithms could break various passwords. This allowed us to understand the difference between the hashing algorithms and the reason that hashing is deliberately slow. 

The second phase was easy, as it showed us how equally tricky to configure PAM modules it can be. The requirements like `dictcheck=0`, `enforce_for_root`, and the unusual placement of the lines involving `pam_faillock` around `pam_unix.so` could be easily missed, which would lead to one of the two extreme results, such as the Invalid password being accepted or weak password being accepted by the root account without any alerts. 

Testing the lockout policy turned out to be the most surprising part of the exercise; I would never think of starting from `user_test` because authentication is off for root, so I would probably end up with a broken lockout policy if I didn’t know how to act.

## Conclusion

The activity managed to compare three password hashing algorithms — MD5-crypt, SHA-512-crypt, and yescrypt — by generating users, analyzing their `/etc/shadow` entries, and cracking a common password with the help of John the Ripper for each algorithm. The results revealed a clear slowdown, which starts from MD5 and goes to yescrypt, effectively showing the reason why contemporary systems prefer slow memory-hard algorithm for password hashing. In addition, `pam_pwquality` was set up with a minimum-length, mixed-letter password requirement, and `pam_faillock` was adjusted to block accounts after five attempts; verification of both settings was done through a perishable `user_test` account and not the users used for the algorithm comparison. Overall, the tasks have accomplished the goal of the activity: practical demonstration of how choice of hashing algorithm, salt, and PAM password policies contribute to systems’ resistance to password attacks.
