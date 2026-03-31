@title: BitLocker and Physical Security
@icon: 🛡️
@description: Encrypting disks to protect data at rest.
@order: 4

# BitLocker fundamentals

**BitLocker** encrypts volumes so stolen disks cannot be read without keys. Often required for compliance (healthcare, finance, privacy regulations).

@section: Requirements

**TPM** (preferred), proper **system partition**, updated firmware.

@section: Recovery

Protect **recovery passwords** (AD backup, MBAM/Azure AD depending on design). Losing keys can mean **data loss**.

@section: Policy

Use **GPO** to require BitLocker on mobile devices and standardize encryption algorithms.

@quiz: Which hardware chip commonly stores BitLocker keys?
@option: GPU
@correct: TPM
@option: 4G modem

@quiz: What happens if you lose recovery keys and the volume is damaged?
@option: Windows reinstalls automatically
@correct: You may permanently lose access to data
@option: Microsoft emails the key
