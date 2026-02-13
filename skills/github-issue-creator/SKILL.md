---
name: github-issue-creator
description: Convert raw notes, error logs, voice dictation, screenshots, and informal bug descriptions into structured GitHub-flavored issue reports. Use when the user asks to draft, clean up, or file an issue from messy inputs. Save issue drafts to `/issues/YYYY-MM-DD-short-description.md` in the current repository and, when requested, publish them to the repository remote with `gh issue create`.
---

# GitHub Issue Creator

Transform messy bug input into concise, actionable issue reports.

## Workflow

1. Identify repository context:
   - Run `git rev-parse --show-toplevel` to get repo root.
   - Use `git remote get-url origin` as the default remote repository target.
   - If `origin` is missing and publishing is requested, ask for `<owner>/<repo>`.
2. Extract structured facts from raw input:
   - Pull out summary, environment, reproduction path, expected vs actual behavior, errors, visual evidence, and impact.
   - Infer obvious context from the current conversation (for example product name or environment).
   - Keep unknowns explicit with placeholders like `[PROJECT_NAME]`, `[REGION]`, `[USER_ID]`.
3. Protect sensitive details:
   - Replace secrets and identifying values with placeholders.
   - Keep technical details needed for debugging.
4. Assign impact severity:
   - `Critical`: service down, data loss, security incident.
   - `High`: major feature broken, no workaround.
   - `Medium`: feature impaired, workaround exists.
   - `Low`: minor inconvenience, cosmetic issue.
5. Generate issue markdown using the template below.
6. Save the issue file:
   - Ensure output directory exists: `mkdir -p <repo-root>/issues`.
   - Use filename format: `YYYY-MM-DD-short-description.md`.
   - Build slug from summary in lowercase kebab-case.
   - If filename already exists, append `-2`, `-3`, etc.
7. Publish to GitHub only when requested:
   - Ensure auth: `gh auth status`.
   - If not authenticated, ask user to run `gh auth login`.
   - Create remote issue with `gh issue create --repo <owner/repo> --title "<summary>" --body-file "<issue-file>"`.
   - Return both local file path and remote issue URL.

## Output Template

```markdown
## Summary
[One-line description of the issue]

## Environment
- **Product/Service**:
- **Region/Version**:
- **Browser/OS**: (if relevant)

## Reproduction Steps
1. [Step]
2. [Step]
3. [Step]

## Expected Behavior
[What should happen]

## Actual Behavior
[What actually happens]

## Error Details
```
[Error message/code if applicable]
```

## Visual Evidence
[Reference to attached screenshots/GIFs]

## Impact
[Severity: Critical/High/Medium/Low + brief explanation]

## Additional Context
[Any other relevant details]
```

## Formatting Rules

- Be crisp. Remove filler language.
- Preserve debugging-critical facts and timings.
- Use GitHub-flavored markdown.
- Keep reproduction steps deterministic and ordered.
- Place visual references inline when available: `![Description](attachment-name.png)`.

## Completion Checklist

- Confirm title/summary match.
- Confirm severity matches impact rubric.
- Confirm no raw secrets or sensitive identifiers remain.
- Confirm file saved under `/issues/` with correct date-slug format.
- If published, confirm issue URL is captured in the response.
