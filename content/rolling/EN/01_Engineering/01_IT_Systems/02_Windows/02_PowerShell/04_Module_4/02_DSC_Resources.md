@title: DSC Resources and Configurations
@icon: 🧩
@description: Building blocks for desired state.
@order: 2

# DSC resources

Resources like **WindowsFeature**, **File**, **Registry**, **Service** compose configurations. **DependsOn** orders dependencies.

@section: Test

Use **`Test-DscConfiguration`** to verify drift without blindly reapplying.

@quiz: Which property commonly marks whether a resource should exist?
@option: State
@correct: Ensure (Present/Absent)
@option: Mode

@quiz: Which cmdlet tests compliance without applying?
@option: Get-DscConfiguration
@correct: Test-DscConfiguration
@option: Confirm-Dsc
