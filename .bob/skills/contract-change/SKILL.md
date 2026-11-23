---
name: contract-change
description: (Archit only) Change the shared contracts safely
metadata:
  user-invocable: true
  disable-model-invocation: true
  argument-hint: what to change
---

Requested contract change: $ARGUMENTS

1. Confirm the human is Archit. If not, stop: write the proposed change as text for them to send to Archit, and edit nothing.
2. Edit contracts/models.py. Prefer additive changes (new optional fields with defaults). Bump CONTRACT_VERSION: minor for additive, major for breaking.
3. Run `make types` and confirm contracts/schema/ and web/src/types/ regenerate without errors.
4. Search the repo for every use of the changed models and list the impacted files grouped by owner.
5. Update any fixture that no longer validates (fixtures/validate.py must pass).
6. Commit as `contracts: vX.Y.Z – <summary>` and print a one-paragraph announcement for team chat that says exactly what changed and who must pull.
