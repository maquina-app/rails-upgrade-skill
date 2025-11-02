---
title: "Rails Upgrade Assistant - Complete Usage Guide"
description: "Comprehensive guide covering installation, usage modes, workflows, best practices, and advanced features for Rails upgrades from 7.0 through 8.1"
type: "usage-guide"
audience: "users"
purpose: "detailed-instructions"
rails_versions: "7.0.x to 8.1.1"
read_time: "60-90 minutes"
workflows_included: 4
learning_paths: 3
tags:
  - usage-guide
  - workflows
  - best-practices
  - installation
  - advanced-features
category: "documentation"
priority: "high"
read_order: 4
last_updated: "2025-11-01"
copyright: Copyright (c) 2025 [Mario Alberto Chávez Cárdenas]
---

# 📖 Rails Upgrade Assistant - Complete Usage Guide

**Version:** 1.0 
**Created:** November 1, 2025  
**Rails Coverage:** 7.0.x through 8.1.1

---

## 📚 Table of Contents

1. [Installation & Setup](#-installation--setup)
2. [Understanding the Package](#-understanding-the-package)
3. [How to Use the Skill](#-how-to-use-the-skill)
4. [Usage Modes](#-usage-modes)
5. [Complete Workflows](#-complete-workflows)
6. [Learning Paths](#-learning-paths)
7. [Best Practices](#-best-practices)
8. [Version-Specific Guidance](#-version-specific-guidance)
9. [Multi-Hop Upgrades](#-multi-hop-upgrades)
10. [Advanced Features](#-advanced-features)
11. [Troubleshooting](#-troubleshooting)
12. [Success Criteria](#-success-criteria)

---

## 🚀 Installation & Setup

### Prerequisites

**Required:**
- Claude Project access (or Claude API)
- Rails application (version 7.0+)
- Git version control
- Rails MCP Server installed and connected

**Optional (for interactive mode):**
- Neovim with MCP server
- Files open in Neovim buffers

### Step 1: Install Rails MCP Server

```bash
# Install 
gem install nvim-mcp-server

# Verify installation
rails-mcp-server version
```

Create `~/.config/rails-mcp/projects.yml`:

```yaml
projects:
  - name: myapp
    path: /path/to/myapp
    description: My Rails Application
```
```bash

# Start in HTTP mode on the default port (6029)
rails-mcp-server --mode http
```
```
```

### Step 2: Configure MCP Server

```bash
# Install the Node.js based MCP proxy
npm install -g mcp-remote

# Run the proxy, pointing to your running Rails MCP Server
npx mcp-remote http://localhost:6029/mcp/sse
```

Add to your Claude Desktop config (`~/Library/Application Support/Claude/claude_desktop_config.json` or equivalent):

```json
{
  "mcpServers": {
    "railsMcpServer": {
      "command": "npx",
      "args": ["mcp-remote", "http://localhost:6029/mcp/sse"]
    }
  }
}
```

For more information visit [Rails MCP Server](https://github.com/maquina-app/rails-mcp-server)

### Step 3: Install Skill in Claude Project

**Option A: Upload entire directory**
1. Open your Claude Project
2. Go to Project Settings → Knowledge
3. Upload the entire `rails-upgrade-assistant/` folder

**Option B: Upload essential files only**
1. Upload `SKILLS.md` (minimum required)
2. Upload `README.md` (recommended)
3. Upload `QUICK-REFERENCE.md` (recommended)
4. The `version-guides/` directory will be referenced

### Step 4: Verify Setup

Test that everything works:

```
Say to Claude:
"What Rails version is my app using?"
```

**Expected Response:**
Claude should use `railsMcpServer:project_info` to detect your Rails version and respond with the version number.

### Step 5: Optional - Neovim Integration

For interactive file updates:

```bash
# Install Neovim MCP server
gem install nvim-mcp-server

# Start in HTTP mode on the default port (6030)
nvim-mcp-server --mode http

# Start Neovim with socket
nvim --listen /tmp/nvim-myapp.sock

# Or configure your nvim init.lua to automatically start the socket with:
local project_name = vim.fn.fnamemodify(vim.fn.getcwd(), ':t')
vim.fn.serverstart('/tmp/nvim-' .. project_name .. '.sock')

# Install the Node.js based MCP proxy
npm install -g mcp-remote

# Run the proxy, pointing to your running Neovim MCP Server
npx mcp-remote http://localhost:6030/mcp/sse
```

Add to Claude config:

```json
{
  "mcpServers": {
    "nvimMcpServer": {
      "command": "npx",
      "args": ["mcp-remote", "http://localhost:6030/mcp/sse"]
    }
  }
}
```

For more information visit [Nvim MCP Server](https://github.com/maquina-app/nvim-mcp-server)

---

## 📦 Understanding the Package

### What You Have

Your Rails Upgrade Assistant package contains:

```
rails-upgrade-assistant/
├── SKILLS.md                    # Main file Claude reads
├── README.md                    # Getting started guide
├── QUICK-REFERENCE.md           # Fast lookup
├── USAGE-GUIDE.md               # This file
└── version-guides/              # Detailed upgrade guides
    ├── upgrade-7.0-to-7.1.md   # 12 breaking changes
    ├── upgrade-7.1-to-7.2.md   # 38 breaking changes
    ├── upgrade-7.2-to-8.0.md   # 13 breaking changes
    └── upgrade-8.0-to-8.1.md   # 8 breaking changes
```

### File Purposes

**SKILLS.md** (25 KB)
- Main entry point for Claude
- Contains all workflow instructions
- MCP tool integration logic
- Sequential upgrade enforcement
- Custom code detection patterns

**README.md** (55 KB)
- Complete user documentation
- Installation instructions
- All upgrade paths explained
- Learning paths by experience
- Success criteria

**QUICK-REFERENCE.md** (48 KB)
- Fast command lookup
- Breaking changes by version
- Quick troubleshooting
- Copy-paste commands

**version-guides/** (4 files, ~320 KB total)
- Detailed breaking changes
- OLD/NEW code examples
- Migration procedures
- Testing checklists
- Component-by-component analysis

---

## 🎯 How to Use the Skill

### Basic Workflow

The skill works in 3 main steps:

1. **You Request** → Say what you want to upgrade
2. **Claude Analyzes** → Uses MCP tools to understand your project
3. **Claude Reports** → Generates comprehensive upgrade guide

### Request Patterns

Claude responds to natural language requests:

**Simple Requests:**
```
"Upgrade my Rails app to 8.1"
"What breaking changes are in Rails 7.2?"
"Help me upgrade from 7.0 to 8.1"
```

**Specific Requests:**
```
"What ActiveRecord changes are in Rails 8.0?"
"Show me configuration changes for Rails 7.2"
"Will my Redis cache work after upgrading to 8.0?"
```

**Interactive Requests:**
```
"Upgrade to 8.1 in interactive mode"
"Update my config files with Neovim"
"Show me the change for config/application.rb"
```

### What Claude Does

When you request an upgrade, Claude:

1. **Calls `railsMcpServer:project_info`**
   - Detects your current Rails version
   - Understands project structure (API-only? Full stack?)
   - Identifies Ruby version

2. **Determines Upgrade Path**
   - Single-hop: 8.0 → 8.1 (loads one guide)
   - Multi-hop: 7.0 → 8.1 (loads all 4 guides)
   - Enforces sequential upgrades (no skipping!)

3. **Analyzes Your Project**
   - Lists configuration files
   - Reads key files for custom code
   - Identifies models, controllers, jobs
   - Checks database schema

4. **Generates Report**
   - Executive summary
   - Breaking changes (HIGH/MEDIUM/LOW)
   - OLD vs NEW code examples
   - ⚠️ Custom code warnings
   - Step-by-step migration guide
   - Testing checklist
   - Rollback plan

---

## 💻 Usage Modes

### Mode 1: Report-Only (Default)

**Best for:** First-time upgrades, team review, complex applications

**How it works:**
1. You request upgrade
2. Claude generates comprehensive report
3. You review report carefully
4. You apply changes manually
5. You test at each step

**Example:**

```
You: "Upgrade my Rails app from 7.2 to 8.0"

Claude: [Analyzes project using MCP tools]

[Generates 50+ page report with:]
- Executive Summary
- 13 Breaking Changes (5 HIGH, 4 MEDIUM, 4 LOW)
- Custom code warnings (3 found)
- Step-by-step guide
- Testing checklist
- Rollback plan

[Report includes links to version-guides/upgrade-7.2-to-8.0.md]
```

**Advantages:**
- ✅ Complete control
- ✅ Review before changes
- ✅ Team collaboration friendly
- ✅ Safe for production apps
- ✅ Works with any editor

**When to use:**
- First time upgrading this app
- Complex application
- Need team review
- Major version jump (7.2 → 8.0)
- Custom code heavy app

---

### Mode 2: Interactive with Neovim

**Best for:** Experienced users, simple upgrades, rapid iteration

**Requirements:**
- Neovim running with MCP server
- Files open in Neovim
- Project name specified
- Socket at `/tmp/nvim-{project}.sock`

**How it works:**
1. You request interactive upgrade
2. Claude generates report
3. Claude checks Neovim buffers
4. For each file:
   - Shows current code (OLD)
   - Shows proposed code (NEW)
   - Asks for approval
   - Updates file if approved
5. Claude summarizes changes

**Example:**

```
You: "Upgrade to 8.1 in interactive mode with project 'myapp'"

Claude: [Generates report]
[Calls nvimMcpServer:get_project_buffers]

Found 3 files open in Neovim:
- config/application.rb
- config/database.yml
- Gemfile

Let me update each one...

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FILE: config/application.rb
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CURRENT CODE (lines 5-7):
```ruby
config.load_defaults 8.0
```

PROPOSED CODE:
```ruby
config.load_defaults 8.1
```

Should I update this file? (yes/no)

You: "yes"

Claude: ✅ Updated config/application.rb
        ✅ File saved
        ✅ Buffer reloaded

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FILE: config/database.yml
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CURRENT CODE (lines 12-14):
```yaml
default: &default
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
```

PROPOSED CODE:
```yaml
default: &default
  max_connections: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
```

Should I update this file? (yes/no)

You: "yes"

Claude: ✅ Updated config/database.yml
        ✅ File saved
        ✅ Buffer reloaded

[Continues for all files...]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SUMMARY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Updated 3 files:
✅ config/application.rb (1 change)
✅ config/database.yml (1 change)
✅ Gemfile (2 changes)

Next steps:
1. Run: bundle install
2. Run: bin/rails test
3. Review changes in git diff
```

**Advantages:**
- ✅ Fast iteration
- ✅ See changes immediately in editor
- ✅ No context switching
- ✅ Easy to review with git diff
- ✅ Can undo with Neovim

**When to use:**
- Simple upgrades (8.0 → 8.1)
- Experienced with Neovim
- Quick iteration needed
- Files already open
- Trust the skill

**Safety features:**
- Shows changes before applying
- Requires approval for each file
- Only updates files in Neovim buffers
- Never modifies closed files
- Preserves custom code (with warnings)

---

### Mode 3: Query-Specific

**Best for:** Specific questions, learning, troubleshooting

**How it works:**
1. You ask a specific question
2. Claude loads relevant guide sections
3. Claude provides focused answer
4. Links to full documentation

**Examples:**

**Component-Specific:**
```
You: "What ActiveRecord changes are in Rails 7.2?"

Claude: Rails 7.2 includes these ActiveRecord changes:

HIGH IMPACT:
• ActiveRecord::Base.connection deprecated
  OLD: ActiveRecord::Base.connection.execute("...")
  NEW: ActiveRecord::Base.with_connection { |c| c.execute("...") }

MEDIUM IMPACT:
• Model serialize syntax changed
  OLD: serialize :data, Hash
  NEW: serialize :data, type: Hash

• query_constraints deprecated
  Use composite_primary_key instead

[Links to version-guides/upgrade-7.1-to-7.2.md for details]
```

**Feature-Specific:**
```
You: "How do transaction-aware jobs work in Rails 7.2?"

Claude: [Loads section from version guide]

Transaction-Aware Job Enqueuing (Rails 7.2):

WHAT CHANGED:
Jobs enqueued inside transactions now wait for commit.

EXAMPLE:
```ruby
Topic.transaction do
  topic = Topic.create(name: "Rails")
  NotificationJob.perform_later(topic)  # Waits for commit!
end
```

WHY: Prevents jobs from running with rolled-back data

IMPACT:
- ✅ Safer: Jobs won't see inconsistent data
- ⚠️ Timing: Job execution delayed until commit
- ⚠️ Testing: May affect test timing expectations

RESTORE OLD BEHAVIOR:
```ruby
class MyJob < ApplicationJob
  self.enqueue_after_transaction_commit = :never
end
```

[Links to full section in version guide]
```

**Configuration-Specific:**
```
You: "Show me all SSL configuration changes"

Claude: [Searches across all version guides]

SSL Configuration Changes Across Versions:

RAILS 7.1 (from 7.0):
• force_ssl now ON by default in production
• New: config.assume_ssl = true (if behind load balancer)

RAILS 8.0 (from 7.2):
• assume_ssl setting formalized
• SSL redirect exclusions for health checks

RAILS 8.1 (from 8.0):
• SSL settings now COMMENTED by default
• Designed for Kamal deployment
• Must uncomment if NOT using Kamal

[Links to relevant sections]
```

**Compatibility:**
```
You: "Will my Redis cache work after upgrading to 8.0?"

Claude: [Analyzes cache configuration]

Redis Cache Compatibility with Rails 8.0:

YES, Redis will continue to work! Here's what you should know:

RAILS 8.0 INTRODUCES:
• Solid Cache (optional SQLite-based cache)
• Default for new apps, but Redis still supported

YOUR OPTIONS:
1. Keep Redis (no changes needed)
2. Migrate to Solid Cache (optional)
3. Use both (different cache stores)

TO KEEP REDIS:
```ruby
# config/environments/production.rb
config.cache_store = :redis_cache_store, { url: ENV['REDIS_URL'] }
```

TO TRY SOLID CACHE:
```bash
rails solid_cache:install
```

RECOMMENDATION:
Stay with Redis if it's working well. Solid Cache is optional.

[Links to full caching documentation]
```

---

## 🔄 Complete Workflows

### Workflow 1: Simple Upgrade (8.0 → 8.1)

**Scenario:** Mature Rails 8.0 app, experienced team, straightforward upgrade

**Time:** 3-5 hours  
**Difficulty:** ⭐ Easy  
**Team Size:** 1-2 developers

**Phase 1: Preparation (30 minutes)**

```bash
# 1. Create backup
git checkout -b rails-8.1-upgrade
git add -A && git commit -m "Pre-upgrade checkpoint"
pg_dump myapp_production > backup.sql

# 2. Verify current state
bin/rails -v  # Should show 8.0.4
bin/rails test  # All tests should pass
```

**Phase 2: Analysis (10 minutes)**

```
Say to Claude:
"Upgrade my Rails app from 8.0 to 8.1"
```

**Claude will:**
- Detect Rails 8.0.4
- Load `version-guides/upgrade-8.0-to-8.1.md`
- Analyze your config files
- Generate report with 8 breaking changes

**Review report:**
- Read executive summary (2 min)
- Review HIGH priority changes (5 min)
- Check custom code warnings (3 min)

**Phase 3: Implementation (1-2 hours)**

Follow report step-by-step:

```ruby
# 1. Update Gemfile
gem "rails", "~> 8.1.1"
gem "image_processing", "~> 1.2"  # Uncomment

group :development, :test do
  gem "bundler-audit", require: false  # Add
end
```

```bash
# 2. Install
bundle update rails
bundle install
```

```ruby
# 3. Update config/application.rb
config.load_defaults 8.1  # was 8.0
```

```yaml
# 4. Update config/database.yml
max_connections:  # was pool:
```

```ruby
# 5. Update config/environments/production.rb
# Uncomment SSL if NOT using Kamal
config.assume_ssl = true
config.force_ssl = true
```

```ruby
# 6. Update app/controllers/application_controller.rb
stale_when_importmap_changes  # Add this line
```

```bash
# 7. Create new files
cat > bin/bundler-audit << 'EOF'
#!/usr/bin/env ruby
require_relative "../config/boot"
require "bundler/audit/cli"
ARGV.concat %w[ --config config/bundler-audit.yml ] if ARGV.empty? || ARGV.include?("check")
Bundler::Audit::CLI.start
EOF

chmod +x bin/bundler-audit
```

```yaml
# 8. Create config/bundler-audit.yml
ignore:
  - CVE-THAT-DOES-NOT-APPLY
```

```
# 9. Update .gitignore
/config/*.key  # was /config/master.key
```

**Phase 4: Testing (1-2 hours)**

```bash
# 1. Run migrations (if any)
bin/rails db:migrate

# 2. Run test suite
bin/rails test
bin/rails test:system

# 3. Check for vulnerabilities
bin/bundler-audit

# 4. Check for deprecations
grep -r "DEPRECATION" log/test.log

# 5. Manual testing
bin/rails server
# Test key workflows in browser
```

**Phase 5: Deployment (1 hour)**

```bash
# 1. Commit changes
git add -A
git commit -m "Upgrade to Rails 8.1.1"

# 2. Deploy to staging
git push staging rails-8.1-upgrade

# 3. Test in staging
# Run smoke tests
# Check error tracking
# Monitor logs

# 4. Deploy to production
git checkout main
git merge rails-8.1-upgrade
git push production main

# 5. Monitor production
# Watch error rates
# Check performance metrics
# Monitor for 24-48 hours
```

**Success Criteria:**
- ✅ All tests pass
- ✅ No deprecation warnings
- ✅ bundler-audit clean
- ✅ Staging tests successful
- ✅ Production deployment smooth
- ✅ No new errors in tracking

---

### Workflow 2: Medium Upgrade (7.1 → 7.2)

**Scenario:** Rails 7.1 app with background jobs, needs careful testing

**Time:** 6-8 hours  
**Difficulty:** ⭐⭐⭐ Hard  
**Team Size:** 2-3 developers

**Critical Note:** This upgrade has the MOST breaking changes (38) and includes a major behavior change with transaction-aware jobs!

**Phase 1: Preparation (1 hour)**

```bash
# 1. Full backup
git checkout -b rails-7.2-upgrade
git add -A && git commit -m "Pre-upgrade checkpoint"
pg_dump myapp_production > backup_$(date +%Y%m%d).sql

# 2. Document current state
bin/rails -v > docs/pre-upgrade-version.txt
git log --oneline -10 >> docs/pre-upgrade-state.txt

# 3. Verify tests pass
bin/rails test  # Should be 100% passing
```

**Phase 2: Analysis (30 minutes)**

```
Say to Claude:
"Upgrade my Rails app from 7.1 to 7.2"
```

**Claude will:**
- Detect Rails 7.1.6
- Load `version-guides/upgrade-7.1-to-7.2.md`
- Analyze your project extensively
- Generate detailed report

**CRITICAL: Review transaction-aware jobs**

```
Say to Claude:
"Explain transaction-aware job enqueuing in detail"
```

**Find all jobs in transactions:**

```bash
# Search for this pattern
grep -r "\.transaction do" app/models/ -A 10 | \
  grep -E "perform_later|perform_now"
```

**Review results carefully!** This affects job timing.

**Phase 3: Implementation (2-3 hours)**

**Step 1: Update Gemfile**

```ruby
gem "rails", "~> 7.2.3"
```

```bash
bundle update rails
```

**Step 2: Update Configuration**

```ruby
# config/application.rb
config.load_defaults 7.2  # was 7.1
config.autoload_lib(ignore: %w[assets tasks])  # Update syntax
```

```ruby
# config/environments/development.rb
config.action_mailer.default_url_options = { host: "localhost", port: 3000 }
config.action_view.annotate_rendered_view_with_filenames = true
```

```ruby
# config/environments/test.rb
config.action_mailer.default_url_options = { host: "www.example.com" }
```

```ruby
# config/environments/production.rb
config.action_dispatch.show_exceptions = :all  # was true
config.ssl_options = { redirect: { exclude: ->(request) { request.path == "/up" } } }
config.active_record.attributes_for_inspect = [:id]
```

**Step 3: Fix Breaking Code**

```ruby
# Fix 1: Params comparison
# OLD
if params == { name: "John" }

# NEW
if params.to_h == { name: "John" }
```

```ruby
# Fix 2: ActiveRecord connection
# OLD
ActiveRecord::Base.connection.execute("TRUNCATE table")

# NEW
ActiveRecord::Base.with_connection do |conn|
  conn.execute("TRUNCATE table")
end
```

```ruby
# Fix 3: Model serialize
# OLD
serialize :metadata, Hash
serialize :settings, JSON

# NEW
serialize :metadata, type: Hash
serialize :settings, coder: JSON
```

```ruby
# Fix 4: Secrets
# OLD
Rails.application.secrets.api_key

# NEW
Rails.application.credentials.api_key
```

**Step 4: Handle Jobs (CRITICAL)**

For each job enqueued in a transaction, decide:

**Option A: Keep new behavior (recommended)**
```ruby
# No changes needed
# Job will wait for transaction to commit
# Test timing carefully!
```

**Option B: Restore old behavior (if timing critical)**
```ruby
class TimeShift < ApplicationJob
  self.enqueue_after_transaction_commit = :never
  
  def perform
    # ...
  end
end
```

**Phase 4: Testing (2-3 hours)**

**Critical: Test jobs extensively!**

```ruby
# Test job timing
test "jobs wait for transaction commit" do
  assert_no_enqueued_jobs do
    Topic.transaction do
      topic = Topic.create(name: "Test")
      NotificationJob.perform_later(topic)
    end
  end
  
  # Job enqueued AFTER transaction
  assert_enqueued_jobs 1
end

# Test rollback behavior
test "jobs not enqueued on rollback" do
  assert_no_enqueued_jobs do
    Topic.transaction do
      topic = Topic.create(name: "Test")
      NotificationJob.perform_later(topic)
      raise ActiveRecord::Rollback
    end
  end
end
```

```bash
# Run full test suite
bin/rails test
bin/rails test:system

# Run specific job tests
bin/rails test test/jobs/
bin/rails test test/models/  # Models that enqueue jobs

# Check deprecations
grep -r "DEPRECATION" log/test.log

# Manual testing
bin/rails server
# Test all job-related features
# Verify timing meets expectations
```

**Phase 5: Staging Deployment (1 hour)**

```bash
# Deploy to staging
git add -A
git commit -m "Upgrade to Rails 7.2.3"
git push staging rails-7.2-upgrade

# Test in staging for 24-48 hours
# Monitor:
# - Job processing times
# - Job success rates
# - Any timing-related issues
# - Error tracking
```

**Phase 6: Production Deployment (1 hour + monitoring)**

```bash
# After staging success
git checkout main
git merge rails-7.2-upgrade
git push production main

# Monitor closely for 48 hours:
# - Job queue depths
# - Job processing times
# - Error rates
# - Performance metrics
```

**Success Criteria:**
- ✅ All tests pass
- ✅ Job timing verified in staging
- ✅ No transaction-related job issues
- ✅ All breaking changes fixed
- ✅ No deprecation warnings
- ✅ Production stable for 48 hours

---

### Workflow 3: Complex Upgrade (7.2 → 8.0)

**Scenario:** Major version jump with architectural changes

**Time:** 8-12 hours  
**Difficulty:** ⭐⭐⭐⭐ Very Hard  
**Team Size:** 3-5 developers

**Critical Changes:**
- Asset pipeline: Sprockets → Propshaft
- Database: Multi-database setup
- Solid gems: New defaults

**Phase 1: Preparation (2 hours)**

```bash
# 1. Full backup and documentation
git checkout -b rails-8.0-upgrade
git add -A && git commit -m "Pre-upgrade checkpoint"

# Database backup
pg_dump myapp_production > backup_$(date +%Y%m%d).sql

# Document current setup
gem list > docs/pre-upgrade-gems.txt
bin/rails routes > docs/pre-upgrade-routes.txt
ls -R app/assets > docs/pre-upgrade-assets.txt

# 2. Review current asset pipeline
ls -la app/assets/
cat app/assets/config/manifest.js
# Document any custom Sprockets processors

# 3. Review database configuration
cat config/database.yml
# Document any custom connection pools

# 4. Team meeting
# Discuss timeline (1-2 weeks recommended)
# Assign responsibilities
# Plan testing strategy
```

**Phase 2: Analysis (1 hour)**

```
Say to Claude:
"Upgrade my Rails app from 7.2 to 8.0"
```

**Review report thoroughly:**
- Executive summary (10 min)
- Asset pipeline changes (20 min)
- Database configuration (15 min)
- Solid gems overview (15 min)

```
Say to Claude:
"Explain the Sprockets to Propshaft migration in detail"
```

```
Say to Claude:
"Do I need to use Solid gems or can I keep Redis?"
```

**Phase 3: Asset Pipeline Migration (2-3 hours)**

**Step 1: Update Gemfile**

```ruby
# Remove
# gem "sprockets-rails"

# Add
gem "propshaft"
gem "rails", "~> 8.0.4"
```

```bash
bundle install
```

**Step 2: Remove Sprockets Manifest**

```bash
rm app/assets/config/manifest.js
```

**Step 3: Update Asset References**

```erb
<!-- app/views/layouts/application.html.erb -->

<!-- OLD -->
<%= stylesheet_link_tag "application", "data-turbo-track": "reload" %>
<%= javascript_importmap_tags %>

<!-- NEW -->
<%= stylesheet_link_tag :app, "data-turbo-track": "reload" %>
<%= javascript_importmap_tags %>
```

**Step 4: Remove Sprockets Directives**

```css
/* app/assets/stylesheets/application.css */

/* OLD - Remove these */
/*
 *= require_tree .
 *= require_self
 */

/* NEW - Import normally */
/* Propshaft serves files without preprocessing */
```

**Step 5: Handle Custom Processors (if any)**

```
Say to Claude:
"I have custom Sprockets processors. How do I migrate?"
```

**Options:**
- Migrate to build-time preprocessing
- Keep Sprockets (supported but not default)
- Use external build tool (esbuild, etc.)

**Step 6: Test Assets**

```bash
# Clear cache
rm -rf tmp/cache
rm -rf public/assets

# Start server
bin/rails server

# Test in browser:
# - All CSS loads correctly
# - All JS loads correctly
# - Images display correctly
# - No 404s in browser console
```

**Phase 4: Database Configuration (1-2 hours)**

**Step 1: Update database.yml**

```yaml
# config/database.yml

# OLD
production:
  <<: *default
  database: storage/production.sqlite3

# NEW
production:
  primary:
    <<: *default
    database: storage/production.sqlite3
  cache:
    <<: *default
    database: storage/production_cache.sqlite3
    migrations_paths: db/cache_migrate
  queue:
    <<: *default
    database: storage/production_queue.sqlite3
    migrations_paths: db/queue_migrate
  cable:
    <<: *default
    database: storage/production_cable.sqlite3
    migrations_paths: db/cable_migrate
```

**Step 2: Create Migration Directories**

```bash
mkdir -p db/cache_migrate
mkdir -p db/queue_migrate
mkdir -p db/cable_migrate
```

**Step 3: Optional - Install Solid Gems**

**Option A: Keep Redis/Sidekiq (no changes)**

```ruby
# No changes to Gemfile or config
# Continue using Redis, Sidekiq, ActionCable with Redis
```

**Option B: Migrate to Solid Gems**

```ruby
# Gemfile
gem "solid_cache"
gem "solid_queue"
gem "solid_cable"
```

```bash
# Install
rails solid_cache:install
rails solid_queue:install
rails solid_cable:install

# Run migrations
rails db:migrate
```

**Phase 5: Configuration Updates (1 hour)**

```ruby
# config/application.rb
config.load_defaults 8.0  # was 7.2
```

```ruby
# config/environments/production.rb
config.assume_ssl = true
config.force_ssl = true
config.silence_healthcheck_path = "/up"
config.active_support.report_deprecations = false
```

```ruby
# config/environments/development.rb
config.active_record.query_log_tags_enabled = true
```

**Phase 6: Testing (3-4 hours)**

```bash
# 1. Database migrations
bin/rails db:migrate
bin/rails db:test:prepare

# 2. Test suite
bin/rails test
bin/rails test:system

# 3. Asset testing
# - Visit every page
# - Check browser console
# - Verify images load
# - Test responsive design

# 4. Database connections
# - Test query performance
# - Verify connection pooling
# - Check for connection leaks

# 5. Background jobs (if using Solid Queue)
# - Test job enqueueing
# - Test job processing
# - Verify job retries

# 6. Caching (if using Solid Cache)
# - Test cache reads
# - Test cache writes
# - Verify cache invalidation

# 7. WebSockets (if using Solid Cable)
# - Test ActionCable connections
# - Verify real-time features
```

**Phase 7: Staging Deployment (1-2 days)**

```bash
# Deploy to staging
git add -A
git commit -m "Upgrade to Rails 8.0.4"
git push staging rails-8.0-upgrade

# Extended staging testing (24-48 hours):
# - Full smoke tests
# - Performance testing
# - Load testing
# - Asset delivery verification
# - Database query performance
# - Background job processing
# - Real-user testing
```

**Phase 8: Production Deployment (1 day + monitoring)**

```bash
# After staging success
git checkout main
git merge rails-8.0-upgrade
git push production main

# Monitor closely for 48-72 hours:
# - Error tracking dashboard
# - Performance metrics
# - Asset load times
# - Database connection pool usage
# - Job queue depths
# - Cache hit rates
# - User feedback
```

**Success Criteria:**
- ✅ Assets load correctly
- ✅ Database connections stable
- ✅ Background jobs processing
- ✅ Cache working as expected
- ✅ All tests pass
- ✅ No performance regression
- ✅ No error rate increase
- ✅ User experience unchanged

---

### Workflow 4: Multi-Hop Upgrade (7.0 → 8.1)

**Scenario:** Legacy app needs to reach latest Rails

**Time:** 2-3 weeks  
**Difficulty:** ⭐⭐⭐⭐⭐ Extremely Hard  
**Team Size:** 3-5 developers

**Overview:**

You CANNOT skip versions. You must complete:
1. **Hop 1:** 7.0 → 7.1 (12 breaking changes)
2. **Hop 2:** 7.1 → 7.2 (38 breaking changes) 
3. **Hop 3:** 7.2 → 8.0 (13 breaking changes)
4. **Hop 4:** 8.0 → 8.1 (8 breaking changes)

**Total:** 71 breaking changes to address!

**Phase 1: Planning (1 week before)**

```
Say to Claude:
"Help me plan an upgrade from Rails 7.0 to 8.1"
```

**Claude will:**
- Explain sequential requirement
- Break down into 4 hops
- Provide timeline estimates
- Identify cumulative risk areas

**Create plan:**

```
Week 1: Hop 1 (7.0 → 7.1)
  - Days 1-2: Implement + test
  - Day 3: Deploy to staging
  - Days 4-5: Deploy to production + monitor

Week 2: Stabilization + Hop 2 prep
  - Days 1-2: Monitor 7.1 in production
  - Days 3-5: Plan 7.1 → 7.2 (review jobs!)

Week 3: Hop 2 (7.1 → 7.2)
  - Days 1-3: Implement + test (extra time for jobs!)
  - Day 4: Deploy to staging
  - Day 5: Deploy to production

Week 4: Stabilization + Hop 3 prep
  - Days 1-2: Monitor 7.2 in production
  - Days 3-5: Plan 7.2 → 8.0 (complex!)

Week 5: Hop 3 (7.2 → 8.0)
  - Days 1-3: Asset pipeline migration
  - Day 4: Testing
  - Day 5: Deploy to staging

Week 6: Hop 3 completion + Hop 4 prep
  - Days 1-2: Monitor 8.0 in staging
  - Day 3: Deploy to production
  - Days 4-5: Plan 8.0 → 8.1

Week 7: Hop 4 (8.0 → 8.1)
  - Days 1-2: Implement + test
  - Day 3: Deploy to staging
  - Days 4-5: Deploy to production

Week 8: Final stabilization
  - Monitor all systems
  - Document lessons learned
  - Update team documentation
```

**Phase 2: Execution**

For each hop:

1. **Create separate branch**
   ```bash
   git checkout -b rails-7.1-upgrade  # For hop 1
   ```

2. **Complete upgrade following workflow**
   - Use Workflow 1 for simple hops (7.0→7.1, 8.0→8.1)
   - Use Workflow 2 for medium hops (7.1→7.2)
   - Use Workflow 3 for complex hops (7.2→8.0)

3. **Deploy to production**
   ```bash
   git checkout main
   git merge rails-7.1-upgrade
   git push production main
   ```

4. **Monitor for 48 hours before next hop**

5. **Document issues and solutions**

6. **Move to next hop**

**Phase 3: Between-Hop Checklist**

After each hop deployment:

- [ ] All tests passing in production
- [ ] No new errors in error tracking
- [ ] Performance metrics stable
- [ ] No user complaints
- [ ] Database healthy
- [ ] Background jobs processing normally
- [ ] 48-hour monitoring period complete
- [ ] Team debriefed on issues
- [ ] Documentation updated

**Only proceed to next hop when ALL items checked!**

**Success Criteria (Final):**
- ✅ All 4 hops completed
- ✅ Rails 8.1.1 in production
- ✅ Zero-downtime upgrades
- ✅ No data loss
- ✅ No major incidents
- ✅ Team confidence high
- ✅ Documentation complete

---

## 🎓 Learning Paths

### Path 1: Complete Beginner

**Goal:** Go from zero knowledge to successful upgrade

**Week 1: Learning**
- Read README.md (30 min)
- Read QUICK-REFERENCE.md (30 min)
- Read relevant version guide (1-2 hours)
- Watch for Claude examples (ongoing)

**Week 2: Planning**
- Analyze your app (1 hour)
- Request upgrade report from Claude (30 min)
- Review all breaking changes (2 hours)
- Create detailed plan (2 hours)

**Week 3: Implementation**
- Follow workflow step-by-step (4-8 hours)
- Test thoroughly (3-4 hours)
- Deploy to staging (1 hour)

**Week 4: Production**
- Deploy to production (1 hour)
- Monitor closely (ongoing)
- Document lessons (2 hours)

**Total Time:** 20-30 hours over 4 weeks

---

### Path 2: Experienced Developer

**Goal:** Efficient upgrade with minimal learning curve

**Day 1: Review**
- Skim QUICK-REFERENCE.md (15 min)
- Request upgrade report from Claude (5 min)
- Review breaking changes (30 min)

**Day 2: Execute**
- Implement changes (3-4 hours)
- Run tests (1-2 hours)

**Day 3: Deploy**
- Staging deployment (1 hour)
- Testing (2 hours)
- Production deployment (1 hour)

**Total Time:** 8-12 hours over 3 days

---

### Path 3: Team Lead

**Goal:** Plan and execute team upgrade

**Week 1: Assessment**
- Review package with team (1 hour meeting)
- Request upgrade analysis from Claude (15 min)
- Assess complexity and risks (2 hours)
- Create project plan (3 hours)

**Week 2: Preparation**
- Assign tasks to team members
- Set up tracking (Jira, etc.)
- Schedule code reviews
- Plan testing strategy

**Week 3-4: Execution**
- Team implements changes
- Daily standups (15 min each)
- Code reviews (ongoing)
- Integration testing

**Week 5: Deployment**
- Staging deployment and testing
- Production deployment
- Monitoring and support

**Total Time:** Varies by team size and complexity

---

## 💎 Best Practices

### Before Starting

**1. Read First, Code Later**

Don't start making changes until you've read the complete upgrade report. Understanding the full scope prevents mistakes.

```
❌ Bad: Start changing Gemfile immediately
✅ Good: Read full report → Plan → Execute
```

**2. Use Version Control**

Create a dedicated upgrade branch:

```bash
git checkout -b rails-8.1-upgrade
git add -A
git commit -m "Pre-upgrade checkpoint"
```

**3. Backup Everything**

```bash
# Database
pg_dump myapp_production > backup.sql

# Or your backup method
heroku pg:backups:capture --app myapp

# Files (if using local storage)
tar -czf storage_backup.tar.gz storage/
```

**4. Verify Tests Pass**

Before upgrading:

```bash
bin/rails test
# Should show: 100% passing

bin/rails test:system
# Should show: All green
```

If tests fail before upgrading, fix them first!

**5. Have a Rollback Plan**

Know how to rollback in < 5 minutes:

```bash
# Practice rollback in staging
git reset --hard HEAD^
bundle install
bin/rails db:rollback STEP=X
bin/rails server
# Verify app works
```

---

### During Upgrade

**1. One Change at a Time**

Make one change → test → commit → repeat

```bash
# Good workflow
git checkout -b rails-upgrade

# Change 1
vim Gemfile
bundle update rails
bin/rails test
git commit -am "Update Rails gem"

# Change 2
vim config/application.rb
bin/rails test
git commit -am "Update load_defaults"

# And so on...
```

**2. Address ALL ⚠️ Warnings**

Never ignore custom code warnings:

```
⚠️ Custom SSL middleware detected
```

This means you need to review and possibly update custom code.

**3. Test After Each Change**

```bash
# Quick test
bin/rails test path/to/affected_test.rb

# Full test
bin/rails test

# System test
bin/rails test:system
```

**4. Check for Deprecation Warnings**

```bash
bin/rails test 2>&1 | grep -i "deprecation"

# Or
grep -r "DEPRECATION" log/test.log
```

Fix deprecations even if tests pass!

**5. Use Staging Extensively**

Especially for:
- Major version jumps (7.2 → 8.0)
- First time upgrading this app
- Complex custom code
- Production-critical apps

**6. Keep Notes**

Document:
- Issues encountered
- Solutions that worked
- Time spent on each phase
- Custom code changes made

This helps future upgrades and helps your team.

---

### After Upgrade

**1. Monitor Closely**

For 48-72 hours after production deploy:

```
Check every 4 hours:
- Error tracking dashboard
- Performance metrics
- User feedback channels
- Database performance
- Job queue depths
```

**2. Enable New Features Gradually**

Don't enable everything at once:

```ruby
# Week 1: Just upgrade, no new features
config.load_defaults 8.1

# Week 2: Enable new cache format (if ready)
config.active_support.cache_format_version = 8.1

# Week 3: Try new features one by one
```

**3. Update Documentation**

```
Update:
- Deployment docs
- Setup instructions
- Team wiki
- README.md
- CHANGELOG.md
```

**4. Share Learnings**

```
Document and share:
- What went well
- What was challenging
- What you'd do differently
- Time estimates (actual vs planned)
```

**5. Plan Next Steps**

```
After upgrade is stable:
- Review new Rails features
- Plan adoption of new features
- Update coding standards
- Train team on changes
```

---

### Multi-Hop Best Practices

**1. Complete Each Hop Fully**

Don't start the next hop until:
- Previous hop deployed to production
- Monitoring period complete (48+ hours)
- All issues resolved
- Team signoff obtained

**2. Deploy Between Hops**

```
❌ Bad: 7.0 → 7.1 → 7.2 all at once
✅ Good: 7.0 → 7.1 [deploy + monitor] → 7.2 [deploy + monitor]
```

**3. Allocate More Time**

Multi-hop upgrades take longer than sum of parts:
- Context switching overhead
- Cumulative complexity
- More testing needed
- Higher risk management

Plan 20-30% extra time.

**4. Team Coordination**

```
Weekly schedule:
- Monday: Plan next hop
- Tuesday-Thursday: Execute
- Friday: Code review + testing
- Weekend: Staging deployment
- Next week: Production + monitor
```

**5. Documentation Critical**

Keep detailed logs:
- What was changed in each hop
- Issues encountered
- Solutions applied
- Time spent

This helps later hops and future upgrades.

---

## 📊 Version-Specific Guidance

### Rails 7.1 (from 7.0)

**Key Changes:**
- cache_classes → enable_reloading (INVERTED!)
- Force SSL now ON by default
- SQLite moved to storage/
- lib/ autoloaded by default

**Focus Areas:**
- Review ALL environment files (inverted boolean!)
- Check production SSL setup
- Move SQLite databases if applicable
- Check lib/ for naming conflicts

**Time:** 2-4 hours  
**Difficulty:** ⭐⭐ Medium

**Most Common Issues:**
- Forgetting to invert enable_reloading
- SSL redirect loops in production
- Naming conflicts in lib/

**See:** `version-guides/upgrade-7.0-to-7.1.md`

---

### Rails 7.2 (from 7.1)

**Key Changes:**
- Transaction-aware jobs (BIGGEST change!)
- ActiveRecord.connection deprecated
- show_exceptions symbol-only
- params comparison removed
- secrets completely removed

**Focus Areas:**
- **CRITICAL:** Find all jobs in transactions
- Test job timing extensively
- Update all .connection usage
- Fix params comparisons
- Migrate from secrets to credentials

**Time:** 4-8 hours  
**Difficulty:** ⭐⭐⭐ Hard (Most breaking changes!)

**Most Common Issues:**
- Job timing unexpected
- Connection pool exhaustion
- params == hash causing errors

**See:** `version-guides/upgrade-7.1-to-7.2.md`

---

### Rails 8.0 (from 7.2)

**Key Changes:**
- Sprockets → Propshaft
- Multi-database setup
- Solid gems as defaults
- SSL configuration changes

**Focus Areas:**
- Asset pipeline complete migration
- Database.yml restructure
- Decide on Solid gems vs existing
- Review custom Sprockets processors

**Time:** 6-12 hours  
**Difficulty:** ⭐⭐⭐⭐ Very Hard

**Most Common Issues:**
- Assets not loading after Propshaft
- Database connection errors
- Confusion about Solid gems

**See:** `version-guides/upgrade-7.2-to-8.0.md`

---

### Rails 8.1 (from 8.0)

**Key Changes:**
- SSL settings commented (Kamal!)
- pool → max_connections
- bundler-audit required
- Semicolon separator removed

**Focus Areas:**
- Uncomment SSL if NOT using Kamal
- Update all database.yml files
- Add bundler-audit
- Fix any semicolon URLs

**Time:** 2-4 hours  
**Difficulty:** ⭐ Easy (Fewest breaking changes)

**Most Common Issues:**
- Forgetting to uncomment SSL
- Missing bundler-audit
- database.yml not fully updated

**See:** `version-guides/upgrade-8.0-to-8.1.md`

---

## 🚧 Multi-Hop Upgrades

### Understanding Multi-Hop

**What is it?**

Upgrading across multiple major versions requires doing each upgrade sequentially:

```
7.0 → 7.1 → 7.2 → 8.0 → 8.1
(4 hops required)
```

**Why can't you skip?**

Rails deprecates features gradually:
- Version X: Feature deprecated (warnings)
- Version X+1: Feature removed (errors)

Skipping versions means:
- Missing deprecation warnings
- Sudden breaking changes
- Harder debugging
- Official guides assume sequential

**Time Estimates:**

| Hops | Time | Difficulty |
|------|------|-----------|
| 1 hop | 2-4 hours | ⭐ Easy |
| 2 hops | 1 week | ⭐⭐⭐ Hard |
| 3 hops | 2 weeks | ⭐⭐⭐⭐ Very Hard |
| 4 hops | 3-4 weeks | ⭐⭐⭐⭐⭐ Extreme |

### Multi-Hop Strategy

**Phase 1: Assessment**

```
Say to Claude:
"I need to upgrade from Rails 7.0 to 8.1. Help me plan this."
```

Claude will:
- Explain sequential requirement
- List all 4 hops
- Estimate time per hop
- Highlight cumulative risks

**Phase 2: Planning**

Create detailed project plan:

```
Milestone 1: Hop 1 (7.0 → 7.1)
- Dates: Week 1
- Developer: Alice
- Reviewer: Bob
- Testing: 2 days
- Deploy: Friday
- Monitor: Weekend

Milestone 2: Stabilization
- Dates: Week 2
- Activities: Monitor production, fix issues
- Prepare: Review 7.1 → 7.2 breaking changes

Milestone 3: Hop 2 (7.1 → 7.2)
- Dates: Week 3
- Developer: Bob
- Reviewer: Alice
- Testing: 3 days (extra for jobs!)
- Deploy: Friday
- Monitor: Weekend

[Continue for all hops...]
```

**Phase 3: Execution**

For each hop:

1. **Create branch**
   ```bash
   git checkout main
   git pull
   git checkout -b rails-7.X-upgrade
   ```

2. **Request analysis**
   ```
   "Upgrade my Rails app from 7.0 to 7.1"
   ```

3. **Implement changes**
   Follow generated report step-by-step

4. **Test thoroughly**
   ```bash
   bin/rails test
   bin/rails test:system
   ```

5. **Deploy to staging**
   ```bash
   git push staging rails-7.X-upgrade
   ```

6. **Test in staging for 24-48 hours**

7. **Deploy to production**
   ```bash
   git checkout main
   git merge rails-7.X-upgrade
   git push production main
   ```

8. **Monitor for 48 hours**

9. **Only then, start next hop!**

**Phase 4: Between-Hop Checklist**

Before starting next hop, verify:

- [ ] Production stable for 48+ hours
- [ ] No new errors in error tracking
- [ ] Performance metrics normal
- [ ] User feedback positive
- [ ] Database healthy
- [ ] Background jobs processing normally
- [ ] Team debriefed
- [ ] Documentation updated
- [ ] Lessons learned captured

**Phase 5: Final Verification**

After all hops complete:

- [ ] Rails 8.1.1 running in production
- [ ] All features working
- [ ] No performance regression
- [ ] No error rate increase
- [ ] Team trained on changes
- [ ] Documentation complete
- [ ] Post-mortem conducted

### Multi-Hop Timeline Example

**Realistic 7.0 → 8.1 Upgrade:**

```
Week 1: Hop 1 (7.0 → 7.1)
  Mon-Tue: Implementation (6 hours)
  Wed: Testing (4 hours)
  Thu: Staging deployment (2 hours)
  Fri: Production deployment (2 hours)
  Weekend: Monitoring

Week 2: Stabilization
  Mon-Tue: Monitor, fix any issues
  Wed-Fri: Plan Hop 2, review job changes

Week 3: Hop 2 (7.1 → 7.2)
  Mon-Wed: Implementation (12 hours)
  Thu: Testing, especially jobs (6 hours)
  Fri: Staging deployment (2 hours)
  Weekend: Extended staging testing

Week 4: Hop 2 Production
  Mon: Production deployment (2 hours)
  Tue-Fri: Monitoring, job testing
  
Week 5: Hop 3 Prep & Start (7.2 → 8.0)
  Mon-Tue: Plan complex asset migration
  Wed-Fri: Begin implementation (12 hours)

Week 6: Hop 3 Complete
  Mon-Tue: Finish implementation (8 hours)
  Wed: Testing (6 hours)
  Thu: Staging deployment (2 hours)
  Fri: Extended staging testing

Week 7: Hop 3 Production & Hop 4 Start
  Mon: Production deployment (2 hours)
  Tue: Monitor
  Wed-Thu: Hop 4 implementation (8 hours)
  Fri: Testing (4 hours)

Week 8: Hop 4 Complete & Final
  Mon: Staging deployment (2 hours)
  Tue: Staging testing
  Wed: Production deployment (2 hours)
  Thu-Fri: Final monitoring & verification

Total: 8 weeks for complete 7.0 → 8.1 upgrade
```

### Multi-Hop Risk Management

**High-Risk Periods:**

1. **Hop 2 (7.1 → 7.2):** Transaction-aware jobs
   - Mitigation: Extra testing time, staging period
   
2. **Hop 3 (7.2 → 8.0):** Asset pipeline change
   - Mitigation: Thorough asset testing, fallback plan

3. **First production deployment of each hop**
   - Mitigation: Deploy during low-traffic window, have rollback ready

**Rollback Strategy:**

Each hop should be independently rollback-able:

```bash
# If Hop 2 (7.1.6) causes issues
git revert HEAD  # Rollback merge
bundle install   # Reinstall 7.1.6 gems
bin/rails db:rollback STEP=X  # If migrations ran
# Deploy

# Investigate issues in staging before retry
```

**Communication Plan:**

- Team daily standups during active hop
- Stakeholder weekly updates
- User communication for downtime windows
- Incident response plan ready

---

## 🔧 Advanced Features

### Custom Code Detection

The skill automatically identifies customizations that may need attention:

**How it works:**

1. Claude calls `railsMcpServer:get_file` on key files
2. Analyzes content for patterns
3. Compares against breaking changes
4. Flags potential issues with ⚠️

**Example detection:**

```
⚠️ CUSTOM MIDDLEWARE DETECTED

File: config/application.rb
Line: 23
Code: middleware.use CustomSSLMiddleware

WARNING: Rails 8.0 changes SSL handling. This custom middleware
may conflict with config.force_ssl and config.assume_ssl.

ACTION REQUIRED:
1. Review CustomSSLMiddleware implementation
2. Test with new SSL config
3. Consider removing if redundant
4. Update if needed for compatibility
```

**What gets detected:**

- Custom middleware
- Manual Redis configuration
- Custom asset processors
- Database sharding setup
- Custom autoload paths
- Overridden Rails methods
- Custom job adapters
- Non-standard file structures

**Using detection results:**

```
Say to Claude:
"Explain the custom SSL middleware warning in detail"

Claude will:
- Explain why it's flagged
- Show relevant breaking changes
- Provide migration guidance
- Suggest testing approach
```

### Interactive Mode Deep Dive

**Prerequisites:**

```bash
# 1. Start Neovim with socket
nvim --listen /tmp/nvim-myapp.sock

# 2. Open files you want to update
:edit config/application.rb
:edit config/database.yml
:edit Gemfile
```

**Activation:**

```
"Upgrade to 8.1 in interactive mode with project 'myapp'"
```

**What happens:**

1. **Claude generates full report first**
   - Same as report-only mode
   - You can review before any changes

2. **Claude checks Neovim buffers**
   ```
   Calls: nvimMcpServer:get_project_buffers
   Finds: 3 files open
   - config/application.rb
   - config/database.yml
   - Gemfile
   ```

3. **For each file, Claude:**
   - Shows current code
   - Shows proposed code
   - Explains what's changing
   - Asks for approval
   - Updates if approved
   - Skips if declined

**Example interaction:**

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FILE 1 of 3: config/application.rb
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CURRENT CODE (lines 11-13):
```ruby
module MyApp
  class Application < Rails::Application
    config.load_defaults 8.0
```

PROPOSED CHANGE:
```ruby
module MyApp
  class Application < Rails::Application
    config.load_defaults 8.1  # Updated for Rails 8.1
```

REASON: Update load_defaults to 8.1 to enable new behaviors

Should I apply this change? (yes/no/skip)
> yes

✅ Applied change
✅ File saved
✅ Buffer reloaded in Neovim

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FILE 2 of 3: config/database.yml
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CURRENT CODE (lines 10-12):
```yaml
default: &default
  adapter: postgresql
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
```

PROPOSED CHANGE:
```yaml
default: &default
  adapter: postgresql
  max_connections: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>  # Renamed in 8.1
```

REASON: Rails 8.1 renames 'pool:' to 'max_connections:'

⚠️ CUSTOM WARNING: You have this configuration in 3 environments
(development, test, production). I can update all 3.

Should I apply this change to ALL occurrences? (yes/no/skip)
> yes

✅ Applied change (3 occurrences)
✅ File saved
✅ Buffer reloaded in Neovim

[Continues...]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SUMMARY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Changes applied:
✅ config/application.rb (1 change)
✅ config/database.yml (3 changes)
✅ Gemfile (2 changes)

Files updated: 3
Total changes: 6

Next steps:
1. Review changes: git diff
2. Run: bundle install
3. Test: bin/rails test
4. Commit: git commit -am "Upgrade to Rails 8.1"
```

**Safety features:**

- Shows EVERY change before applying
- Requires explicit approval
- Never modifies closed files
- Can skip individual changes
- All changes visible in git diff
- Can undo with Neovim undo
- Can rollback with git

**Best practices:**

1. Open only files you want to update
2. Review changes in git diff after
3. Test before committing
4. Commit with good message
5. Can run again if you skipped files

### Querying Specific Information

The skill can answer very specific questions:

**Component-specific:**

```
"What changes in ActiveJob for Rails 7.2?"
"Show me all ActionMailer deprecations"
"What's new in ActiveRecord 8.0?"
```

**Feature-specific:**

```
"How do I use params.expect()?"
"Explain transaction-aware job enqueuing"
"What is Solid Cache?"
```

**File-specific:**

```
"What changes in config/application.rb?"
"Show me Gemfile updates for Rails 8.1"
"What needs to change in production.rb?"
```

**Code-specific:**

```
"How do I fix params == hash comparisons?"
"Show me OLD and NEW serialize syntax"
"What replaces Rails.application.secrets?"
```

**Testing-specific:**

```
"How do I test transaction-aware jobs?"
"What assertion syntax changed in 7.2?"
"How do I test Solid Queue?"
```

### Working with Large Applications

For complex apps with many custom features:

**Strategy 1: Incremental Analysis**

```
# Start broad
"Analyze my Rails app for upgrade to 8.1"

# Drill down into concerns
"Show me how my background jobs are affected"
"What about my custom authentication system?"
"Will my API routes need changes?"
```

**Strategy 2: File-by-File Review**

```
# For each critical file
"Analyze config/application.rb for Rails 8.1 compatibility"
"Check app/controllers/application_controller.rb for issues"
"Review config/initializers/redis.rb for deprecations"
```

**Strategy 3: Feature-by-Feature**

```
# For each major feature
"How does the upgrade affect file uploads?"
"What about WebSocket connections?"
"Will image processing still work?"
```

### Handling Custom Gems

If your app uses custom or forked gems:

```
Say to Claude:
"I use a forked version of devise. Will it work with Rails 8.1?"

Claude will:
- Check for known compatibility issues
- Suggest checking gem's Rails version requirements
- Recommend testing approach
- Provide alternatives if needed
```

**Common gem compatibility questions:**

```
"Is devise compatible with Rails 8.1?"
"What about pundit?"
"Does kaminari work with Rails 8.0?"
"Will my custom engine need updates?"
```

---

## 🆘 Troubleshooting

### Common Issues

**Issue: "Rails MCP server not responding"**

Solution:
```bash
# Check if running
rails-mcp-server --version

# Reinstall if needed
npm uninstall -g rails-mcp-server
npm install -g rails-mcp-server

# Verify Claude config
cat ~/.config/Claude/config.json
# Should have "rails" in mcpServers
```

**Issue: "Can't detect Rails version"**

Solution:
```bash
# Verify Gemfile exists
ls Gemfile

# Check for Rails gem
grep "gem 'rails'" Gemfile

# Manually tell Claude if needed
"My Rails version is 8.0.4"
```

**Issue: "Interactive mode not working"**

Solution:
```bash
# Check Neovim socket
ls /tmp/nvim-*.sock

# If missing, start with socket
nvim --listen /tmp/nvim-myapp.sock

# Verify Neovim MCP server
# Check Claude config for "neovim" entry
```

**Issue: "Report is too generic"**

Solution:
```
# Ask Claude to analyze first
"Analyze my Rails project configuration"

# Then request upgrade
"Now upgrade to 8.1 based on my actual setup"

# Or be more specific
"Check my config/application.rb and tell me what needs updating"
```

**Issue: "Tests failing after upgrade"**

Solution:
```
# Get help from Claude
"My tests are failing with this error: [paste error]"

Claude will:
- Identify the breaking change causing it
- Explain what changed
- Provide fix
- Show test examples
```

For more troubleshooting, see `TROUBLESHOOTING.md` (if you have it) or ask Claude!

---

## ✅ Success Criteria

### Minimum Success (Functional)

- ✅ Application boots without errors
- ✅ All tests pass
- ✅ Core features work in browser
- ✅ Database queries execute
- ✅ Background jobs process

### Good Success (Production-Ready)

All minimum criteria plus:
- ✅ No deprecation warnings in logs
- ✅ Staging tests successful
- ✅ Performance metrics stable
- ✅ No error rate increase
- ✅ Security scan clean (bundler-audit)

### Excellent Success (Optimized)

All good criteria plus:
- ✅ New features adopted
- ✅ Caching optimized
- ✅ Documentation updated
- ✅ Team trained
- ✅ Zero-downtime deployment
- ✅ Monitoring dashboards healthy
- ✅ User feedback positive

### Measuring Success

**Immediately After Upgrade:**

```bash
# Version check
bin/rails -v
# Should show target version

# Tests
bin/rails test
# Should show 100% passing

# Deprecations
grep -r "DEPRECATION" log/
# Should show none (or only minor)

# Security
bin/bundler-audit
# Should show no vulnerabilities
```

**24 Hours After Production:**

- Error tracking: No spike in errors
- Performance: Response times normal or better
- Users: No complaints or issues
- Jobs: Processing normally
- Database: Healthy metrics

**1 Week After Production:**

- Stability: No new issues discovered
- Performance: Sustained improvement
- Team: Comfortable with changes
- Documentation: Complete and accurate

---

## 📝 Final Notes

### This Guide Provides

✅ Complete installation instructions
✅ All usage modes explained
✅ 4 detailed workflows
✅ Learning paths for all levels
✅ Comprehensive best practices
✅ Version-specific guidance
✅ Multi-hop upgrade strategy
✅ Advanced features documentation
✅ Troubleshooting guidance
✅ Success criteria

### What You Should Do Next

**If this is your first time:**
1. Read this guide completely (2-3 hours)
2. Review your target version guide
3. Request analysis from Claude
4. Create detailed plan
5. Start upgrade

**If you're experienced:**
1. Skim relevant sections (30 min)
2. Request upgrade from Claude
3. Follow generated report
4. Reference this guide as needed

**If you have questions:**
1. Check QUICK-REFERENCE.md first
2. Search this guide
3. Ask Claude directly
4. Consult community resources

### Remember

- 📖 Read first, code later
- 🧪 Test thoroughly
- 💾 Backup everything
- 📊 Monitor closely
- 📝 Document learnings
- 👥 Communicate with team
- 🎯 One step at a time

**Good luck with your Rails upgrade! 🚀**

---

**Usage Guide Version:** 1.0  
**Last Updated:** November 1, 2025  
**Rails Coverage:** 7.0.x through 8.1.1  
**Based on:** Official Rails CHANGELOGs

For the latest version of this guide and other resources, check the main README.md file.
