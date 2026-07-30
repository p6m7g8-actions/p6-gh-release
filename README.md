# p6m7g8-actions/p6-gh-release

- [p6m7g8-actions/p6-gh-release](#p6m7g8-actionsp6-gh-release)
  - [Usage](#usage)
  - [Inputs](#inputs)
  - [Version bumping](#version-bumping)

## Usage

```yaml
      - name: Release
        uses: p6m7g8-actions/p6-gh-release@main
        with:
          gh_token: ${{ secrets.P6_A_GH_TOKEN }}
```

## Inputs

| Name | Required | Description |
| --- | --- | --- |
| `gh_token` | yes | Token used to create the GitHub release |

## Version bumping

The next version is derived from the conventional-commit subjects of the commits
since the highest `v*` tag, selected by version order rather than commit date.
Matching is anchored to the commit prefix, so a message that merely contains a
keyword does not move the version.

| Commit | Bump |
| --- | --- |
| `BREAKING CHANGE:` or `BREAKING-CHANGE:` starting a line in the body | major |
| `<type>!:` or `<type>(scope)!:`, for any type | major |
| `major:` | major |
| `feat:` | minor |
| `fix:` | patch |
| `chore:` other than `chore(release):` | patch |
| anything else | none |

When nothing matches, no tag is advanced and no release is created. Two
consequences worth knowing:

- A subject such as `docs: major README rework` produces no bump. It previously
  produced a major one, because the match was an unanchored substring.
- A breaking change is detected from either the `!` marker or a `BREAKING CHANGE:`
  footer. The footer requires the full commit body, which is why the action reads
  both `%s` and `%B`. It must start a line, so `not a BREAKING CHANGE: relax`
  inside a paragraph does not bump major.
- The `!` marker counts on any lowercase type, not only `feat` and `fix`. A commit
  subject of `docs!: drop the v1 examples` therefore produces a major bump.
