---
name: handoff
description: Write a short status note for team chat
metadata:
  user-invocable: true
  disable-model-invocation: true
---

Run `git log --oneline -15` and `git diff origin/main --stat` for this branch, then write a status note for team chat, under 120 words, plain text, no headings:
- what now works and how a teammate can try it (endpoint, URL or screen)
- any change to env vars or shapes (there should be none unless Archit changed contracts)
- what is next
- blockers, each with the teammate's name
