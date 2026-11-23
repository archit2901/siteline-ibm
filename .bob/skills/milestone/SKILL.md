---
name: milestone
description: Check your folders against a milestone's done criteria (P0 to P4, or Setup)
metadata:
  user-invocable: true
  disable-model-invocation: true
  argument-hint: Setup|P0|P1|P2|P3|P4
---

Check milestone $ARGUMENTS for the person who owns this session.

1. Confirm who you are working for (ask if you don't know) and their folders from the ownership table in the root CLAUDE.md.
2. Read the milestone section in that person's folder CLAUDE.md and in prompts/<person>.md for $ARGUMENTS.
3. Run the folder's tests, then smoke-check the endpoints or screens: fixture mode first, then live mode if it exists.
4. Report a checklist: each done criterion with PASS or FAIL and the evidence (command plus a short output snippet).
5. List anything blocked on another teammate, with their name and what you need.

Do not change code while running this command unless I ask you to.
