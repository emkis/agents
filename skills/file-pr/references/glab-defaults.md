# Personal GitLab defaults

Not universal — personal defaults for GitLab remotes that don't already have their own PR/MR lifecycle skill owning this. Add them to `glab mr create` alongside the title and description file:

- `--assignee "nicolas.jardim"`
- `--label "team::app-engagement"`

Full command:

```
glab mr create --draft --assignee "nicolas.jardim" --label "team::app-engagement" --title "<title>" --description-file <path>
```

No equivalent defaults exist for `gh` yet.
