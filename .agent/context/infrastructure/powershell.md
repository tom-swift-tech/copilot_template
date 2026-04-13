# PowerShell Conventions

## Module Structure

```
ModuleName/
├── ModuleName.psd1          # Module manifest
├── ModuleName.psm1          # Root module (dot-sources functions)
├── Public/
│   └── <Verb>-<Noun>.ps1   # Exported functions
├── Private/
│   └── helpers.ps1          # Internal functions
└── Tests/
    └── ModuleName.Tests.ps1 # Pester tests
```

## Coding Standards

```powershell
function Get-ServerHealth {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory, ValueFromPipeline)]
        [ValidateNotNullOrEmpty()]
        [string[]]$ComputerName,

        [Parameter()]
        [ValidateSet('Basic', 'Full')]
        [string]$ReportType = 'Basic'
    )

    begin {
        # One-time setup
    }

    process {
        foreach ($computer in $ComputerName) {
            # Per-object processing with proper error handling
            try {
                # ...
            }
            catch {
                Write-Error "Failed to query ${computer}: $_"
            }
        }
    }

    end {
        # Cleanup
    }
}
```

**Mandatory patterns:**

- `[CmdletBinding()]` on every function.
- `Verb-Noun` naming from the approved verb list (`Get-Verb`).
- Pipeline support: `[Parameter(ValueFromPipeline)]` where it makes sense.
- `$ErrorActionPreference = 'Stop'` at script scope for scripts (not modules).
- Structured error handling: `try/catch` with `Write-Error`, never `throw` in exported functions (breaks pipeline).
- Output objects, not strings: return `[PSCustomObject]@{...}`, never `Write-Host` for data.
- `#Requires -Version 7.0` or appropriate minimum at top of scripts.

## Security

- Never store credentials in scripts — use `Get-Credential`, `SecretManagement` module, or Key Vault.
- `SecureString` for any password parameters.
- Sign scripts with a code-signing certificate in production environments.
- Execution policy: `RemoteSigned` minimum, `AllSigned` for production.

## Pester Testing

```powershell
Describe 'Get-ServerHealth' {
    Context 'When server is reachable' {
        It 'Returns a health object with expected properties' {
            $result = Get-ServerHealth -ComputerName 'localhost'
            $result | Should -Not -BeNullOrEmpty
            $result.PSObject.Properties.Name | Should -Contain 'Status'
        }
    }

    Context 'When server is unreachable' {
        It 'Writes an error without terminating' {
            { Get-ServerHealth -ComputerName 'fake-server' } |
                Should -Not -Throw
        }
    }
}
```

- Every exported function has a corresponding Pester test.
- Use `InModuleScope` for testing private functions.
- Mock external dependencies (`Mock Invoke-RestMethod ...`).
