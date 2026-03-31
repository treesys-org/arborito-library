@title: Jobs and Background Processing
@icon: ⏳
@description: Long-running tasks without blocking the shell.
@order: 4

# Jobs

**`Start-Job`** for local background work; **`Invoke-Command -AsJob`** for remote parallel tasks. PowerShell 7 adds **`ForEach-Object -Parallel`**.

@section: Receive-Job

Always **`Receive-Job`** and **`Remove-Job`** to avoid leaking job objects.

@quiz: Which cmdlet retrieves output from a job?
@option: Get-Output
@correct: Receive-Job
@option: Wait-Job

@quiz: Which `ForEach-Object` parameter enables parallelism in PS 7+?
@option: -Batch
@correct: -Parallel
@option: -Async
