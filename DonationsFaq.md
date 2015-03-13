**15 Jun 2011: Effective immediately, the donations program is no longer available for this project.  Donations are now handled via license purchases.  A project wide lifetime license can be purchased or single plugin licenses may be purchases for the plugins available in this project.  Details on the new model are available [here](PluginPricing.md).  Licenses may be purchased from [here](PluginLicenseStore.md).**



# Why all of a sudden are donations required? #

First, donations **aren't** required.  Some of my plugins may have some features disabled without a license and a license is obtained via a donation.

The reason I'm asking for donations is to defray out of pocket expenses incurred during the development of my plugins.  Until a few weeks ago my project work cost me nothing more than my time and I'm more than happy to donate my time to the various projects I work on and to the SageTV community.  However, back in early July while working on some code changes to one of my plugins I introduced a bug that effectively wiped my hard drive (be careful with '<' when you really mean '>') and since I've only ever had one SageTV server - the one everyone in the house uses - I basically rendered my SageTV system useless.  Luckily I have backups, etc. of everything and I was able to restore my server, but it was 3 solid evenings wasted and lots of complaints - "when can we watch tv again!?"  Now I've been developing my plugins like this for over three years and this is the first time I've caused any serious damage to my system, but this incident made me realize that I don't want to be doing that again so I decided I needed to move my development work to a separate SageTV server.  So I first setup a VM on my current server, but not enough RAM, so I moved the VM to my laptop - not enough CPU so then I threw together a Linux server from some spare parts.  Good enough, but I then needed a SageTV for Linux license and a new hard drive.  So for the first time, my development work cost me money - not much, but some.  Considering the only reason for this setup and purchase of Linux license for SageTV is to develop my plugins, I feel it appropriate to now ask for donations.

# What are donations used for? #

They are used to cover expenses related to my SageTV development work.  Currently, that includes the purchase of a SageTV server for Linux license and a new hard drive.  As of this writing (19 Aug 2010), I have received donations to cover about 65% of those expenses.  Excess donations will be used for items such as additional RAM for my production server such that I can host a VM instance of SageTV on it and/or perhaps a CPU upgrade for my current test Linux server or additional tuners for the test server so that I may test some features in a multi-tuner environment, etc.  I'm not looking to make any kind of money from these donations - you're not funding a vacation or any such thing. :)

# Will all your plugins move to a license type system? #

Not sure at this point.  It is fairly certain that my SAD plugin will not ever require a license - it was a weekend of work and I haven't had to touch it since.  SageAlert is the first plugin to implement a license system and SJQv4 will probably be the next.  I haven't made a decision on SRE at this time.

# But SRE is accepting donations, will I have to donate twice if you do require a license? #

Absolutely not.  Donators to SRE prior to a license requirement being applied will receive a license.

# Does the license ever expire? #

At this time, no.  However, the license you have may become invalid as I experiment with various approaches to licensing.  Rest assured, however, that if a different type of license for anything you've donated to becomes necessary, you'll receive a valid replacement license as needed.

This (expiration) policy may change in the future, but I highly doubt it.

# How is the price point set?  Will this change? #

$7.50 is half way between $5 and $10.  At $5 (or less), I felt as though I was doing nothing but making PayPal a little more profitable and at $10 I felt that may be a little high for some so I settled on the number in between.  At $7.50, after PayPal takes their cut, I receive a little under $7 of each donation.  I think that's fair.

The pricing can change in one direction or the other at any time.

# I want the extra features you've disabled, but don't want to/can't afford to/etc. donate.  Suggestions? #

All of my work remains open source, this includes the source code for the licensing schemes I've implemented.  If you feel as though my work isn't worth a donation or you can't afford to donate then you're free to grab the source code, strip the licensing verification out of it, rebuild it, and deploy it within your SageTV installation.  One could even release the license-free version of the code as a new plugin in the community repository.  Not so sure how I'd feel about that, but there's nothing stopping you from doing it.  Again, these donations aren't about making a buck - far from it, which is why I will not hide the source code.

# Will you provide the license-free version of your plugins in the repository for installation? #

No.

# Does a licensed version of a plugin entitle me to anything extra like priority/private support or priority feature request implementation? #

No, sorry.  Donations help cover expenses and are appreciated, but they do not entitle anyone to anything more than I already give.  I have a full time job and other "life" things going on and I set aside only so much time for SageTV.  The amount of time I spend on SageTV or when I spend time on SageTV will not change on account of a donation.  Furthermore, you could donate today and I may quit all SageTV activities tomorrow.  Mind you, that seems quite unlikely, but I want to be clear that it's not impossible.  I've been a part of the SageTV community since 2007 and you are free to look at my level of activity in the user forums to date.  As I say, I think that history speaks for itself, but anything unexpected could happen causing me to walk away from SageTV.  If you're uncomfortable with that possibility then simply do not donate, I won't be upset. ;)

# Will a license for one plugin be valid for all the others requiring a license? #

**(Updated on 22 Aug 2010)** Yes.