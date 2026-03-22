# Contributing to GRITS Agent Scanner

Issues and PRs welcome. A few guidelines to keep the project clean.

## What belongs here

- Bug reports and fixes for the scanner or fixer
- New checks for OpenClaw or NemoClaw (follow the existing check function pattern)
- Improvements to hardening guides and checklists
- Host hardening script updates aligned to CIS/DISA

## What belongs in the framework repo

The GRITS Framework (github.com/X-Scale-AI/GRITS) owns the governance spec,
control catalog, scoring methodology, and compliance mappings. If you are
proposing a new control layer or scoring change, open an issue there first.

## Pull request checklist

- [ ] New check functions follow the `Finding` class pattern
- [ ] One-liner implications added to `OC_LINES` or `NC_LINES` in `grits-agent-scanner`
- [ ] Auto-fixable checks have corresponding logic in `grits-agent-secure`
- [ ] `CHECK_LINES` `auto_fixable` flag matches whether a fixer exists
- [ ] Scanner output attribution line (`Scored with GRITS v... by X Scale AI`) preserved

## Security vulnerabilities

See [SECURITY.md](SECURITY.md).

## License

Contributions are accepted under Apache 2.0. See [LICENSE](LICENSE).

X Scale AI -- https://xscaleai.com
