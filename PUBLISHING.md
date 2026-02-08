# Publishing to ClawdHub

This document details the process of publishing the Alpaca Trading skill to ClawdHub.

## Prerequisites

1. **ClawdHub CLI installed:**
   ```bash
   npm install -g clawhub
   ```

2. **Authenticated with ClawdHub:**
   ```bash
   clawhub login
   # Opens browser for authentication

   # Verify authentication
   clawhub whoami
   ```

3. **GitHub account at least 1 week old** (ClawdHub requirement)

## Publishing Process

### Initial Publication (v1.0.0)

```bash
# Navigate to skill directory
cd ~/.openclaw/skills/alpaca-trading

# Publish the skill
clawhub publish . --slug alpaca-trading --name "Alpaca Trading" --version 1.0.0
```

**Result:** ✅ Published `alpaca-trading@1.0.0`

### Subsequent Updates

When you make changes to the skill, publish a new version:

```bash
# Make your changes to SKILL.md or README.md
# ...

# Publish new version (increment version number)
clawhub publish . --slug alpaca-trading --name "Alpaca Trading" --version 1.1.0
```

**Result:** ✅ Published `alpaca-trading@1.1.0`

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-02-08 | Initial release |
| 1.0.1 | 2026-02-08 | Documentation updates |
| 1.1.0 | 2026-02-08 | Enhanced README with marketing psychology, added GitHub repo |

## Versioning Guidelines

Follow [Semantic Versioning](https://semver.org/):

- **MAJOR** (x.0.0): Breaking changes to skill functionality
- **MINOR** (1.x.0): New features, significant documentation improvements
- **PATCH** (1.0.x): Bug fixes, minor documentation tweaks

**Examples:**
- Adding new command examples: PATCH (1.0.1 → 1.0.2)
- Adding new workflow sections: MINOR (1.0.0 → 1.1.0)
- Changing skill triggers or requirements: MAJOR (1.0.0 → 2.0.0)

## Common Publishing Commands

### Check skill info
```bash
clawhub inspect alpaca-trading
```

**Output:**
```
alpaca-trading  Alpaca Trading
Summary: Execute trades, analyze markets, and manage portfolios...
Owner: lacymorrow
Latest: 1.1.0
```

### Search for your skill
```bash
clawhub search alpaca trading
```

### Install your published skill
```bash
clawhub install alpaca-trading

# Or install specific version
clawhub install alpaca-trading@1.0.0
```

### Update an installed skill
```bash
clawhub update alpaca-trading
```

### Star your own skill (optional)
```bash
clawhub star alpaca-trading
```

## Sync Method (Alternative)

Instead of manually specifying versions, you can use the `sync` command:

```bash
cd ~/.openclaw/skills

# Scans all skills in the directory and auto-publishes new/updated ones
clawhub sync
```

**Pros:** Automatic version detection and bulk publishing
**Cons:** Less control over version numbers

## Troubleshooting

### Error: "SKILL.md required"
**Cause:** `clawhub publish` can't find the SKILL.md file
**Solution:** Use absolute path or ensure you're in the correct directory
```bash
clawhub publish /Users/lacy/.openclaw/skills/alpaca-trading --slug alpaca-trading --name "Alpaca Trading" --version 1.1.0
```

### Error: "Version already exists"
**Cause:** You've already published that version number
**Solution:** Increment the version number
```bash
# If 1.0.0 exists, use 1.0.1 or 1.1.0
clawhub publish . --slug alpaca-trading --name "Alpaca Trading" --version 1.1.0
```

### Error: "GitHub account too new"
**Cause:** ClawdHub requires GitHub accounts to be at least 1 week old
**Solution:** Wait until your account is old enough, or use an older GitHub account

### Check what's published
```bash
# View published skill details
clawhub inspect alpaca-trading

# See all your published skills
clawhub explore --author lacymorrow
```

## Post-Publication

After publishing, users can install your skill:

```bash
# Anyone can now install it
clawhub install alpaca-trading

# View it on ClawdHub website
# Visit: https://clawhub.ai/skills/alpaca-trading
```

## Updating the Skill

1. **Make changes** to SKILL.md or README.md
2. **Test locally** by restarting Claude Code
3. **Commit to GitHub:**
   ```bash
   git add .
   git commit -m "Update: describe your changes"
   git push
   ```
4. **Publish new version to ClawdHub:**
   ```bash
   clawhub publish . --slug alpaca-trading --name "Alpaca Trading" --version 1.2.0
   ```
5. **Update this document** with the new version in Version History

## Publishing Checklist

Before publishing a new version:

- [ ] SKILL.md is accurate and complete
- [ ] README.md is up to date
- [ ] Version number is incremented appropriately
- [ ] Changes are committed to GitHub
- [ ] Tested the skill locally
- [ ] Version history is updated in this file
- [ ] No sensitive information (API keys) in files

## Links

- **GitHub Repository:** https://github.com/lacymorrow/alpaca-trading-skill
- **ClawdHub Profile:** https://clawhub.ai/@lacymorrow
- **ClawdHub Documentation:** https://docs.clawhub.ai/

## Notes

- ClawdHub uses semantic versioning
- The `latest` tag always points to the highest version number
- Published skills are public and visible to everyone
- You can hide or delete skills if needed (see `clawhub hide` and `clawhub delete`)

---

**Last Updated:** 2026-02-08
**Latest Version:** 1.1.0
**Total Downloads:** Check with `clawhub inspect alpaca-trading`
