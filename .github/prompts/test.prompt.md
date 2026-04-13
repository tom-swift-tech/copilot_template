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

## Step 3: Test and Validate (mandatory — do not skip)
> New tests must prove they actually exercise the code they claim to test.
1. Run the full test suite — all pass, including the new tests.
2. Run each new test twice — flakes fail this gate.
3. **Mutation check**: temporarily break the code under test (flip a `>` to `<`, change a return value). Confirm the new test fails. Revert. A test that doesn't fail on broken code isn't a test.
4. Run the linter — clean.
5. Capture coverage delta if the project tracks it; otherwise list the file:line ranges newly covered.
