# Week 1 Tutorial Portfolio Unit Introduction
The previous week (this week) is a tutorial to prepare for the unit. This consists of five sections: Interactive Activities, GitHub Repository setup, GNS3 Re-familiarisation, Group Formation and Moodle Review and Assessments.

---

## Interactive Activities 

Two interactive activities were completed this week.

### Network Security Architecture Identify Zones, Devices & Boundaries

In this activity users will be tasked with identifying devices/services in the appropriate security zone (trusted, DMZ, untrusted), matching devices to their zone, answering true/false questions, and answering questions about where to place a firewall, across 8 rounds of questions covering zone identification, device matching, true/false and placement of a firewall.

![Activity 1: Identify Zones, Devices & Boundaries — score 8/8 (100%)](week1-images/Week1-Network-Security-Architecture_png.png)

The activity was completed with a score of 8/8 (100%).

### Phishing Triage

This activity provides a scenario in which a support is needed and requests the appropriate action for incident response. The scenario that unfolded was one in which a user, named Umair, was having emails appearing as read when they were not, which is an indication of mailbox access without his presence. The sign-in log was clean, that indicated that a third party app (not published by Adobe) was given too many permissions including `Mail.Read` + `Send` + `MailboxSettings.ReadWrite`, and OAuth tokens weren't found in sign-in logs. The correct fix was identified as not resetting Umair's password, but rather revoking the rogue OAuth grant instead, since a password reset is not going to do anything if the attacker is using a valid OAuth grant and not stolen credentials.

![Phishing Triage — correct answer: revoke the rogue OAuth grant (3/3 stars)](week1-images/Week1-Phishing-Triage.png)

The activity was completed correctly on the first attempt (3/3 stars).

---

## GitHub Repository 

As per instructions a personal GitHub account is needed and a private repository called coit12202-portfolio added to the repository with the tutor and Unit Coordinator as collaborators and a link to the repository shared in the Teams General channel.

---

## GNS3 Re Familiarisation 

As directed, you should install the GNS3 Appliance in VirtualBox and start the GNS3 VM, finish the GNS3 Intro activity, and then transfer a file from the GNS3 VM to the host machine (such as using WinSCP or FileZilla).
---

## Group Formation 


For Assessment 2 a group is to be formed, with one member creating a coit12202-project GitHub repository, adding the other members of the group, and adding the tutor and Unit Coordinator as Collaborators, setting up a Project Kanban board and adding issue "Read the assignment specification" to that board, then posting the team names/IDs and links to the repository and Kanban board in the Teams General channel.

---

## Review Moodle and Assessments 

As directed, the rest of Moodle is reviewed, particularly the assessments and, optionally, a preview of the GNS3 activities for the next week's tutorial.
---

## Reflection

The two interactive activities were effective as an introduction to the unit as they were low stakes and useful to refresh the students on the placement of devices and controls in a layered network (Trusted zone, DMZ, Untrusted zone) and the concept of defence in depth, which means not relying on a single control. Kneeling on the ground with a foundation picture already strong coming in, is what has been suggested by Getting.

The Phishing Triage scenario was interesting, as the first step that you might take seems obvious — but it's not. The scenario was based on the premise that there is no correlation between a clean sign-in log and an account being safe: OAuth consent grants are not part of the standard sign-in experience and they aren't even recorded in sign-in logs.The scenario was based on the premise that there is no correlation between a clean sign-in log and an account being safe: OAuth consent grants are not part of the standard sign-in experience, they aren't even recorded in sign-in logs. That's nice to carry over to real incident triage because it's a common mistake—when you see no suspicious sign-ins, that's no compromise.
## Conclusion
This week's tutorial was an orientation and preparation week in that it reinforced and was used to prepare for the core concepts of network security architecture and incident-triage which were covered in the unit, without the actual unit commencing. The two were successfully completed (8/8 and 3/3 respectively). These are the hands-on activities that will form the foundation of the rest of the unit; they should be done prior to Week 2 as instructed: the personal GitHub repository for the assessment portfolio, re-familiarisation with GNS3, group formation for Assessment 2, and review of the Moodle and assessment briefs.
