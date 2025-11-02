---
title: "Rails Upgrade Assistant Skill - Main Instructions"
description: "Core instructions for Claude to analyze Rails projects, detect versions, enforce sequential upgrades, and generate comprehensive upgrade reports using Rails MCP tools"
type: "skill-instructions"
version: "1.0"
for: "claude-projects"
capabilities:
  - rails-version-detection
  - sequential-upgrade-enforcement
  - custom-code-detection
  - mcp-integration
  - neovim-integration
  - report-generation
rails_versions: "7.0.x to 8.1.1"
mcp_servers:
  - rails-mcp-server
  - neovim-mcp-server
tags:
  - rails-upgrade
  - skill
  - mcp
  - automation
  - version-detection
category: "skill-core"
priority: "critical"
read_order: 1
last_updated: "2025-11-01"
copyright: Copyright (c) 2025 [Mario Alberto Chávez Cárdenas]
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
- `README.md` - Human-readable overview
- `QUICK-REFERENCE.md` - Command cheat sheet
- `USAGE-GUIDE.md` - Comprehensive how-to
- `TROUBLESHOOTING.md` - Common issues & solutions

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

### Examples
- `examples/simple-upgrade.md` - Single version hop
- `examples/complex-multi-hop.md` - Multi-version upgrade
- `examples/custom-code-handling.md` - Preserving customizations

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

### Step 1: Detect Current Version and Project Info

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
