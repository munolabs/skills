# Muno Labs Skills

A collection of AI agent skills for Claude Code, Codex, and other AI coding assistants.

Built by [Muno Labs](https://munolabs.com) — we use these skills daily and share them with the community.

## Available Skills

### looker-studio

Complete guide for Google Looker Studio - calculated fields, formulas, PostgreSQL/Supabase connection, and visualization best practices.

**Use when:**
- Creating calculated fields or formulas in Looker Studio
- Connecting Looker Studio to PostgreSQL/Supabase
- Working with date/time conversions and timezones
- Building dashboards and visualizations
- Need syntax reference for CASE, IF, DATETIME functions

**Location:** `./looker-studio/SKILL.md`

**References included:**
- Date functions (DATETIME_ADD, DATETIME_SUB, FORMAT_DATETIME)
- Text functions (CONCAT, SUBSTR, REGEXP_EXTRACT)
- Aggregation functions (SUM, AVG, COUNT, PERCENTILE)
- Logic functions (CASE, IF, IFNULL, COALESCE)
- PostgreSQL/Supabase connection guide

## Installation

### Claude Code
```bash
/plugin marketplace add munolabs/skills
/plugin install looker-studio
```

### Manual
Copy the skill folder to your `.claude/skills/` directory.

## Contributing

We welcome contributions! If you have improvements or new skills to add, please open a PR.

## License

MIT License - see [LICENSE](LICENSE) for details.

---

**Contact:** [hi@munolabs.com](mailto:hi@munolabs.com)
