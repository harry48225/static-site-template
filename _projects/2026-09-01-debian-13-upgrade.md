---
layout: project
author: harry
title: debian 13 upgrade | octopode
---

I took the bank holiday weekend as an opportunity to do a task I'd been putting off for a while - upgrading my homelab vms from debian 12 to debian 13 :aaaaaa: , this is because debian 12 is now out of regular security support (and has been for over a month (oops)).

I was pretty worried this could snowball into a task that would take all three days to resolve if I ended up breaking something. The official upgrade guide is really long, but I had a better idea.

In theory, as I've got everything set up as IaaC with ansible and open tofu (terraform fork), along with automatic backup and restore of my container data to/from my nas, all I needed to do was change the vm os image resource in my tf config to point at debian 13 (rather than 12), then run `tofu apply`, and run my playbooks with ansible-playbook , the vms should be recreated and then ansible should deploy all my services, pulling backups from the nas as appropriate.

So I did it and waited nervously for 20 mins as everything was re-provisioned, and ..., it worked*****!

Well almost worked, everything but nextcloud and jellyfin came back exactly as they were. Nextcloud didn't work because I wasn't actually correctly backing up its data (something I learned after fighting with it, for 2 hours), and jellyfin didn't work because the intel decoding driver system package is no longer included in debian 13 (but I can install it manually from intel's github). I think this was also a pretty good test of my disaster recovery process since this was essentially the same - but I still need to test my offsite backups.

Some lessons learned:
- Upgrading vms in a rolling manner starting from the lowest risk to the highest risk worked well and helped me build confidence in the process.
- I didn't delete the old vms until I had verified the new ones were working (saved me when I nearly lost the nextcloud data)
- Adding notes with all the snags I found (surprisingly few) and updating my playbooks to prevent them in future (mostly just the docker group id changing between OS installs), and the notes that I had in my repo from previous tasks like this were invaluable.
- Avoid scope creep!! This was meant to be just an OS upgrade but also turned into a refactor of my .tf file, but I managed to stop it there. If I'd let this become the refactor of my playbooks into roles (that I'm definitely for sure going to do one day soon...) that I was also tempted to do it would have taken the entire weekend, and been a lot riskier.

obligatory fastfetch
![screenshot of liunx desktop, there are 4 terminal windows open each showing the output of fastfetch showing each computer is running debian 13](/assets/images/debian-13/fastfetch.png)