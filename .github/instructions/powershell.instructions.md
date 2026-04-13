---
name: 'PowerShell Conventions'
description: 'PowerShell scripting standards'
applyTo: '**/*.{ps1,psm1,psd1}'
---

# PowerShell Conventions

- `#Requires -Version 7` minimum for cross-platform
- `[CmdletBinding()]` and proper parameter validation on all functions
- Verb-Noun naming: `Get-ServiceStatus`, `Set-ConfigValue`
- Output objects, not strings — let the consumer format
- `$ErrorActionPreference = 'Stop'` at script level
- Never use `-ErrorAction SilentlyContinue` without justification
- Use `try/catch` with `-ErrorAction Stop` for error handling

Refer to [.agent/context/infrastructure/powershell.md](../../.agent/context/infrastructure/powershell.md) for full enterprise PowerShell conventions.
