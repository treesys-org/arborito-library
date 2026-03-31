@title: GPO: Preferences vs Policies and WMI Filtering
@icon: ⚙️
@description: Advanced targeting techniques.
@order: 2

# Advanced GPO: WMI filters and loopback

**WMI filters** apply a GPO only when a **WQL** query matches the machine—use sparingly for performance.

@section: Loopback policy

**User policy** can depend on **computer OU** location—common for labs/kiosks.

*   **Merge** vs **Replace** modes.

@section: Item-level targeting

GPP items can test IP ranges, security groups, registry values—flexible without exploding OU count.

@quiz: What is loopback primarily used for?
@option: Faster Internet
@correct: Apply user configuration based on where the computer account lives
@option: Disable firewall globally

@quiz: What query language do WMI filters use?
@option: ANSI SQL
@correct: WQL
@option: PowerShell only
