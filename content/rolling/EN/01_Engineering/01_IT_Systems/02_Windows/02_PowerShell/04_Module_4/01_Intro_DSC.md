@title: Intro to Desired State Configuration (DSC)
@icon: 📜
@description: Declaring desired server state.
@order: 1

# DSC introduction

**DSC** describes **what** state should be (feature installed, file present, setting value) and **LCM** applies it. Modern enterprises may combine DSC concepts with **Azure Automanage** / **Guest Configuration**—verify current Microsoft guidance for new designs.

@section: LCM

The **Local Configuration Manager** applies MOF files and enforces **Monitor**/**AutoCorrect** modes.

@section: Compile

`Configuration` blocks compile to **`.mof`** consumed by **`Start-DscConfiguration`**.

@quiz: What applies DSC configuration on a node?
@option: WMI
@correct: Local Configuration Manager (LCM)
@option: DNS Client

@quiz: What file extension do compiled DSC configurations use?
@option: .json
@correct: .mof
@option: .ps1
