@title: Group Policy Objects (GPO): Fundamentals
@icon: 🏛️
@description: The primary way to configure Windows clients at scale.
@order: 1

# Group Policy fundamentals

**GPOs** apply computer/user settings to sites, domains, and **OUs**. Master **inheritance**, **enforcement**, **security filtering**, and **link order**.

@section: GPMC

Use **`gpmc.msc`** to create, link, backup, and model GPOs.

@section: Processing order

Local → Site → Domain → OUs (parent to child). **Later links** usually win unless blocked/enforced.

@section: Preferences vs Policies

**Policies** tattoo some registry keys; **Preferences** can be softer and item-targeted.

@section: Reporting

`gpresult /h report.html` on clients; **Group Policy Modeling** before rollout.

@quiz: Which console manages Group Policy objects?
@option: dnsmgmt.msc
@correct: gpmc.msc
@option: lusrmgr.msc

@quiz: What determines precedence when multiple GPOs apply to the same OU?
@option: Alphabetical name only
@correct: Link order, enforcement, and block inheritance settings
@option: Windows install date
