---
name: rails-upgrade-assistant
description: Analyzes Rails applications and generates comprehensive upgrade reports with breaking changes, deprecations, and step-by-step migration guides for Rails 7.0 through 8.1.1. Use when upgrading Rails applications, planning multi-hop upgrades, or querying version-specific changes.
---

# Rails Upgrade Assistant Skill v1.0

## Skill Identity
- **Name:** Rails Upgrade Assistant
- **Version:** 1.0
- **Purpose:** Intelligent Rails application upgrades from 7.0 through 8.1.1
- **Based on:** Official Rails CHANGELOGs from GitHub
- **Upgrade Strategy:** Sequential only (no version skipping)

---

## Core Functionality

This skill helps users upgrade Rails applications using:
1. **Rails MCP Server** - for project analysis and file inspection
2. **Neovim MCP Server** - for interactive file updates (optional)
3. **Sequential Upgrade Path** - enforces proper version progression
4. **Custom Configuration Preservation** - maintains user customizations

---

## Trigger Patterns

Claude should activate this skill when user says:
- "Upgrade my Rails app to [version]"
- "Help me upgrade from Rails [x] to [y]"
- "What breaking changes are in Rails [version]?"
- "Plan my upgrade from [x] to [y]"
- "What Rails version am I using?"
- "Analyze my Rails app for upgrade"

---

## CRITICAL: Sequential Upgrade Strategy

### ⚠️ Version Skipping is NOT Allowed

Rails upgrades MUST follow this exact sequence:
```
7.0.x → 7.1.x → 7.2.x → 8.0.x → 8.1.x
```

**You CANNOT skip versions.** Examples:
- ❌ 7.0 → 7.2 (skips 7.1)
- ❌ 7.0 → 8.0 (skips 7.1 and 7.2)
- ✅ 7.0 → 7.1 (correct)
- ✅ 7.1 → 7.2 (correct)

If user requests a multi-hop upgrade (e.g., 7.0 → 8.1), you must:
1. Explain the sequential requirement
2. Break it into individual hops
3. Generate separate reports for each hop
4. Recommend completing each hop fully before moving to next

---

## Available Resources

### Core Documentation
- `docs/README.md` - Human-readable overview
- `docs/QUICK-REFERENCE.md` - Command cheat sheet
- `docs/USAGE-GUIDE.md` - Comprehensive how-to

### Version-Specific Guides (Load as needed)
- `version-guides/upgrade-7.0-to-7.1.md` - Rails 7.0 → 7.1
- `version-guides/upgrade-7.1-to-7.2.md` - Rails 7.1 → 7.2
- `version-guides/upgrade-7.2-to-8.0.md` - Rails 7.2 → 8.0
- `version-guides/upgrade-8.0-to-8.1.md` - Rails 8.0 → 8.1

### Reference Materials
- `reference/breaking-changes-by-version.md` - Quick lookup
- `reference/multi-hop-strategy.md` - Multi-version planning
- `reference/deprecations-timeline.md` - Deprecation tracking
- `reference/testing-checklist.md` - Comprehensive testing

---

---

## Report Generation Templates

### Upgrade Report Template

**Location:** `templates/upgrade-report-template.md`

**Purpose:** Structure for generating comprehensive upgrade reports

**When to use:** Every time you generate an upgrade report

**Template Variables:**

The template uses placeholder variables that you should replace with actual values:

- `{FROM_VERSION}` - Current Rails version (e.g., "8.0.4")
- `{TO_VERSION}` - Target Rails version (e.g., "8.1.1")
- `{GENERATION_DATE}` - Current date
- `{PROJECT_NAME}` - User's project name
- `{UPGRADE_TYPE}` - "Single-hop" or "Multi-hop (hop X of Y)"
- `{COMPLEXITY_STARS}` - ⭐ to ⭐⭐⭐⭐⭐
- `{TIME_ESTIMATE}` - "2-4 hours", "1-2 weeks", etc.
- `{RISK_LEVEL}` - "Low", "Medium", "High", "Very High"
- `{BREAKING_CHANGES_COUNT}` - Number of breaking changes
- `{CUSTOM_WARNINGS_COUNT}` - Number of custom code warnings
- `{FILES_COUNT}` - Number of files to change
- `{TESTS_COUNT}` - Number of tests in suite
- `{HIGH_COUNT}` - Number of HIGH priority changes
- `{MEDIUM_COUNT}` - Number of MEDIUM priority changes
- `{LOW_COUNT}` - Number of LOW priority changes
- `{PROJECT_ROOT}` - Full path to project
- `{CURRENT_RAILS_VERSION}` - Detected Rails version
- `{RUBY_VERSION}` - Detected Ruby version
- `{APP_TYPE}` - "Full Stack" or "API-only"
- `{DATABASE_TYPE}` - "PostgreSQL", "MySQL", "SQLite", etc.

**Sections to populate:**

1. `{HIGH_IMPACT_SUMMARY}` - Bulleted list of high-priority changes
2. `{MEDIUM_IMPACT_SUMMARY}` - Bulleted list of medium-priority changes
3. `{LOW_IMPACT_SUMMARY}` - Bulleted list of low-priority changes
4. `{CUSTOM_CODE_LIST}` - Numbered list of detected customizations
5. `{RECOMMENDATION_TEXT}` - Proceed/wait recommendation with reasoning
6. `{PROJECT_STRUCTURE}` - ASCII tree of project directories
7. `{GEMFILE_CONTENT}` - Relevant gems from Gemfile
8. `{PRODUCTION_CONFIG}` - Key production.rb settings
9. `{DATABASE_CONFIG}` - Database.yml configuration
10. `{CUSTOM_CODE_DETAILS}` - Detailed custom code findings
11. `{BREAKING_CHANGES_SECTION}` - Full breaking changes with OLD/NEW examples
12. `{CUSTOM_WARNINGS_SECTION}` - Detailed warnings for each customization
13. `{MIGRATION_GUIDE_SECTION}` - 8-phase step-by-step guide
14. `{TESTING_CHECKLIST_SECTION}` - Complete testing procedures
15. `{ROLLBACK_PLAN_SECTION}` - When and how to rollback
16. `{RESOURCES_SECTION}` - Links to official documentation
17. `{FILES_SUMMARY_SECTION}` - List of files to modify/create

**Usage Instructions:**

When generating an upgrade report:

1. Read the template: `templates/upgrade-report-template.md`
2. Use MCP tools to gather project information
3. Load appropriate version guide(s)
4. Replace all placeholder variables
5. Populate all sections with specific content
6. Generate complete report as output

**Important:** 
- Never show placeholder variables in the final report
- All `{VARIABLES}` must be replaced with actual values
- Each section must contain real, actionable content
- Include OLD vs NEW code examples for every breaking change
- Flag all custom code with ⚠️ warnings

---

## MCP Tools Integration

### Required MCP Server: Rails MCP Server

**Available Tools:**
- `railsMcpServer:project_info` - Get Rails version, structure, API mode
- `railsMcpServer:get_file` - Read file contents
- `railsMcpServer:list_files` - Browse directories
- `railsMcpServer:get_routes` - Analyze routes
- `railsMcpServer:analyze_models` - Inspect models
- `railsMcpServer:get_schema` - Database schema
- `railsMcpServer:analyze_controller_views` - Controllers & views
- `railsMcpServer:analyze_environment_config` - Config files

### Optional MCP Server: Neovim MCP Server

**Available Tools:**
- `nvimMcpServer:get_project_buffers` - List open files
- `nvimMcpServer:update_buffer` - Update file content

---

## Workflow Instructions for Claude

### Detect Current Version and Project Info

**ALWAYS start with project analysis:**

```
1. Call: railsMcpServer:project_info
   - Extracts Rails version from Gemfile
   - Gets project structure (API-only? Full stack?)
   - Identifies Rails root directory

2. Store:
   - current_version (e.g., "7.0.8")
   - target_version (from user request)
   - project_type (api/full_stack)
   - project_root
```

### Report Generation Workflow

**Step 1: Load Template**

- Read: `templates/upgrade-report-template.md`
- Understand report structure
- Note all placeholder variables

**Step 2: Gather Project Data (Using MCP)**

1. Call: railsMcpServer:project_info
  - Extract: Rails version, structure, type

2. Call: railsMcpServer:get_file
  - Read: Gemfile, config files

3. Call: railsMcpServer:list_files
  - Find: Custom code patterns

4. Store all data for template population

**Step 3: Load Version Guide**

1. Determine upgrade path (from → to)
2. Load: version-guides/upgrade-X-to-Y.md
3. Extract: Breaking changes, priorities

**Step 4: Detect Custom Code**

1. Search for custom middleware
2. Find manual configurations
3. Identify API-specific patterns
4. Flag each with ⚠️ warning

**Step 5: Populate Template**

1. Replace all {PLACEHOLDER} variables
2. Fill in each section with actual content
3. Include OLD vs NEW examples
4. Add custom warnings
5. Generate complete report

**Step 6: Deliver Report**

1. Present complete markdown report
2. Offer to save as file
3. Offer interactive mode (if Neovim available)
