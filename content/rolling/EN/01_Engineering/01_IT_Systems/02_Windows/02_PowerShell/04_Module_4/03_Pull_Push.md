@title: Pull vs Push DSC Servers
@icon: 🎯
@description: How nodes retrieve configuration.
@order: 3

# Pull vs push

**Push:** administrator runs **`Start-DscConfiguration`**. **Pull:** nodes fetch MOF/modules from a pull server or cloud service.

**Note:** Microsoft’s guidance for **new** pull-server deployments has evolved—prefer documented solutions for your era (cloud/automation services).

@quiz: Which model starts with `Start-DscConfiguration` from an admin machine?
@option: Pull
@correct: Push
@option: Hybrid Sync

@quiz: What is a key benefit of pull at scale?
@option: No network required
@correct: Nodes can retrieve configuration without simultaneous admin sessions to every server
@option: HTTPS not needed
