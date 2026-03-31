@title: WSUS: Patch Management
@icon: 🩹
@description: Controlling which updates deploy and when.
@order: 3

# WSUS patch management

**WSUS** downloads updates from Microsoft, lets you **approve** them, and points clients via **GPO** to your internal WSUS server.

@section: Design

*   Choose **WID** vs **SQL** for the database.
*   Plan **disk space** for update files.
*   Create **computer groups** (pilot → production).

@section: Client GPO

Set the **intranet update service** location and configure restart policies for business hours.

@section: Maintenance

Run **Server Cleanup Wizard** periodically to remove superseded updates.

@quiz: What is the main benefit of WSUS vs every PC pulling from Windows Update directly?
@option: Faster PCs automatically
@correct: Central approval and scheduling of updates
@option: Removes antivirus need

@quiz: Which policy points clients to an internal WSUS server?
@option: Map network drive
@correct: Specify intranet Microsoft update service location
@option: Restricted groups
