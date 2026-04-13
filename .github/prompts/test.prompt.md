---
description: 'Test writing — identify gaps, write tests, verify edge cases'
agent: 'builder'
tools: ['search/codebase', 'terminal/runCommand']
---

# Test Writing Workflow

## Step 1: Assess Coverage
1. Search codebase for existing tests.
2. Identify untested code paths.
3. Prioritize: business logic > integration points > utilities.

## Step 2: Write Tests
- Name describes behavior: `test_returns_error_when_input_is_empty`
- Arrange → Act → Assert structure
- One behavior per test
- Cover: normal case, edge cases, error cases
- For Terraform: `terraform plan` as minimum, `terraform test` where supported
- For Ansible: `--check` mode + assert tasks

## Step 3: Verify
1. Run test suite — all pass.
2. Run twice if uncertain about flakiness.
