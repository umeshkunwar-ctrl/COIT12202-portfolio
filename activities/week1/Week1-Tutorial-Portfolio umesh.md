# Week 1 Tutorial Portfolio — Unit Introduction

This week 1 tutorial prepares for the unit. It covers five parts: Interactive Activities, GitHub Repository setup, GNS3 Re-familiarisation, Group Formation, and a review of Moodle and Assessments.

---

## Interactive Activities [30 minutes]

Two interactive activities were completed this week.

### Network Security Architecture — Identify Zones, Devices & Boundaries

This activity involves placing devices and services in the correct security zone (trusted, DMZ, untrusted), identifying boundary security devices, and classifying controls by defence-in-depth layer, across 8 rounds covering zone identification, device matching, true/false, and firewall placement questions.

![Activity 1: Identify Zones, Devices & Boundaries — score 8/8 (100%)](week1-images/Week1-Network-Security-Architecture_png.png)

The activity was completed with a score of 8/8 (100%).

### Phishing Triage

This activity presents a support scenario and asks for the correct incident-response action. The scenario involved a user, Umair, whose emails were showing as read without him opening them — a sign of mailbox access without his knowledge. The sign-in log was clean, which pointed away from a compromised password and towards **OAuth consent abuse**: a third-party app ("Adobe License Manager," not published by Adobe) had been granted excessive permissions (`Mail.Read` + `Send` + `MailboxSettings.ReadWrite`), and OAuth tokens do not appear in sign-in logs. The correct response was identified as revoking the rogue OAuth grant rather than resetting Umair's password, since a password reset does nothing if the attacker is operating through a valid OAuth token rather than stolen credentials.

![Phishing Triage — correct answer: revoke the rogue OAuth grant (3/3 stars)](week1-images/Week1-Phishing-Triage.png)

The activity was completed correctly on the first attempt (3/3 stars).

---

## GitHub Repository [20 min]

Per the instructions, a personal GitHub account is required, along with a **private** repository named `coit12202-portfolio`, with the tutor and Unit Coordinator added as collaborators, and a link to the repository posted in the Teams General channel.

---

## GNS3 Re-Familiarisation [30 min]

Per the instructions, this involves installing the GNS3 Appliance inside VirtualBox, starting the GNS3 VM, completing the GNS3 Intro activity, and transferring a file from the GNS3 VM to the host computer (e.g. via WinSCP or FileZilla).

---

## Group Formation [20 min]

Per the instructions, for Assessment 2 a group needs to be formed, after which one member creates a `coit12202-project` GitHub repository, adds team members plus the tutor and Unit Coordinator as collaborators, creates an initial issue (e.g. "Read the assignment specification"), sets up a Project Kanban board with that issue added, and posts the team names/IDs and links to the repository and Kanban board in the Teams General channel.

---

## Review Moodle and Assessments [Homework, 30 min]

Per the instructions, remaining time is used to review the rest of Moodle, especially the assessments, and optionally to look ahead at the GNS3 activities for the following week's tutorial.

---

## Reflection

The two interactive activities were a good low-stakes way to start the unit — the Network Security Architecture exercise was a useful refresher on where devices and controls belong in a layered network (trusted zone, DMZ, untrusted zone) and why defence-in-depth means no single control is relied on alone. Getting 8/8 suggested that foundational picture was already solid coming in.

The Phishing Triage scenario was more interesting because the obvious first instinct — reset the user's password — turned out to be the wrong move. The scenario was built around the fact that a clean sign-in log does not mean an account is safe: OAuth consent grants operate independently of the normal sign-in flow and don't appear in those logs at all, so a compromised account can look completely clean from that angle. That's a useful distinction to carry forward, since it's an easy blind spot in real incident triage — assuming "no suspicious sign-ins" means "no compromise."

## Conclusion

This week's tutorial served its purpose as an orientation and preparation week: the two completed interactive activities reinforced core network security architecture and incident-triage concepts ahead of the unit properly starting, both completed successfully (8/8 and 3/3 respectively). The remaining setup tasks — the personal GitHub portfolio repository, GNS3 re-familiarisation, group formation for Assessment 2, and a review of Moodle and the assessment briefs — are the practical groundwork the rest of the unit depends on, and are to be completed before the start of Week 2 as instructed.
