# 10xTubeStorage

Public, git-backed storage for [10xTube](https://github.com/chico/10xTube) reports and optional learning modules for [10xLearn](https://github.com/chico/10xLearn).

**No database.** Content is public but unlisted by path obscurity (hashed user folders). Do not put PII or raw user UUIDs in this repo.

## Privacy

| Item | Rule |
| --- | --- |
| User identity | App generates a UUID (`localStorage`). **Never** commit the UUID here. |
| Path key | `userHash = sha256(userId).slice(0, 16)` (hex, lowercase) |
| Profile | `profile.json` may contain `{ "createdAt" }` only — no email/name |

Anyone with a `userHash` can read that folder. Treat hashes like unlisted links.

## Layout

```text
users/
  <userHash>/
    profile.json                 # { "createdAt": "<iso>" }
    index.json                   # list of summarised videos for this user
    videos/
      <youtubeVideoId>/
        report.md                # full insights report (markdown)
        meta.json                # title, url, transcriptSource, scores, …
        learning-module/         # optional Tube → Learn export
          module.json
          snippets/
            <snippetId>.md
          prompt-attachment.md
```

`users/` starts empty; folders are created on first write from 10xTube.

## `index.json`

```json
{
  "videos": [
    {
      "videoId": "AJnDyBLKRZs",
      "title": "…",
      "createdAt": "2026-08-12T00:00:00.000Z",
      "updatedAt": "2026-08-12T00:00:00.000Z"
    }
  ]
}
```

## `meta.json`

```json
{
  "videoId": "AJnDyBLKRZs",
  "title": "…",
  "url": "https://www.youtube.com/watch?v=AJnDyBLKRZs",
  "transcriptSource": "gemini",
  "worthWatchingScore": 90,
  "timeToValue": "12:00",
  "bestTimestamp": { "seconds": 279, "label": "4:39", "rationale": "…" },
  "createdAt": "…",
  "updatedAt": "…"
}
```

`transcriptSource` is `gemini` | `fallback`.

## Writes

10xTube commits via the GitHub API (`GITHUB_TOKEN`) — typically one commit per summarise / module export.

## Reads

- Raw: `https://raw.githubusercontent.com/chico/10xTubeStorage/main/users/<userHash>/videos/<videoId>/report.md`
- Or GitHub Contents API

## Learning modules

See 10xTube `contracts/learning-module.schema.json` and harness playbook §8 (`type: "10x-learn-module"`, version 1).
