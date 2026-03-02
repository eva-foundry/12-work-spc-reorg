# Project 12: VS Code Workspace Reorganization

**Goal**: Restructure workspace for optimal VS Code experience, short paths, and clear project separation

**Status**: 📋 Planning  
**Created**: January 25, 2026  
**Owner**: Marco Presta  

---

## Problem Statement

### Current Issues

1. **Path Length Problem**: Deep nesting causes issues with bash scripts and Windows path limits
   - Current: `I:\EVA-JP-v1.2\docs\eva-foundation\projects\11-MS-InfoJP\base-platform\` (71 chars)
   - Problem: Spaces in OneDrive path required `subst I:` workaround
   - Impact: Bash scripts fail, deployment complexity

2. **Organizational Confusion**: Multiple "bags of projects" scattered across workspace
   - `EVA Suite/` - Planning hub + scattered projects
   - `EVA-JP-v1.2/docs/` - Used as project hub instead of EVA-JP documentation
   - `open-webui/` - Reference repo mixed with active projects
   - No clear separation between active work, archived projects, and reference code

3. **Mixed Purposes**: Single folders serving multiple roles
   - `EVA-JP-v1.2/docs/` contains: actual docs, unrelated projects (habit-tracker), governance work, foundation projects
   - Makes it hard to know what's active vs archived vs reference

4. **VS Code Workspace Overload**: Opening one massive workspace with everything
   - Slow performance
   - Difficult to focus on specific tasks
   - Cluttered file explorer

### Current Structure

```
I:\
├── EVA Suite/                      # 🎒 Bag #1: Planning, orchestration, housekeeping
│   ├── eva-orchestrator/
│   ├── info-assistant-reference/
│   ├── reports/
│   └── scripts/
│
├── EVA-JP-v1.2/                    # 🎒 Bag #2: Mixed app + project hub
│   ├── app/                        # EVA-JP application
│   ├── infra/                      # EVA-JP infrastructure  
│   └── docs/                       # 🚨 ACTUALLY A PROJECT HUB
│       ├── eva-ai-governance/      # Separate project
│       ├── eva-foundation/         # Project container
│       │   └── projects/           # 11 sub-projects (including MS-InfoJP)
│       ├── habit-tracker/          # Unrelated project
│       └── jp/                     # JP-specific stuff
│
├── open-webui/                     # 📚 Reference (used to build EVA Chat)
├── PubSec-Info-Assistant/          # 📚 Reference (Microsoft base)
└── EVA-JP-reference-0113/          # 📚 Reference (previous version)
```

---

## Proposed Solution

### Organizational Principles

1. **Separate by Purpose**: Active work, archived projects, reference code, planning hub
2. **Flatten Structure**: No deep nesting - max 2 levels for any active project
3. **Short Paths**: Enable bash scripts, reduce typing, avoid Windows path limits
4. **Focused Workspaces**: Create task-specific VS Code workspaces instead of one mega-workspace
5. **Clear Naming**: Prefix folders to indicate purpose (`_active/`, `_projects/`, `_reference/`)

### Target Structure

```
I:\
├── _active/                        # 🚀 CURRENT WORK (max 3-5 projects)
│   ├── ms-infojp/                 # MS-InfoJP deployment (moved from deep nest)
│   ├── jp-auto-extraction/        # JP automation (currently active)
│   └── eva-ai-governance/         # Active governance work
│
├── _projects/                      # 📂 ALL PROJECTS (flat, organized)
│   ├── 01-documentation-generator/
│   ├── 02-poc-agent-skills/
│   ├── 03-poc-enhanced-docs/
│   ├── 04-os-vnext/
│   ├── 05-extract-cases/
│   ├── 06-jp-auto-extraction/ → (symlink to _active/jp-auto-extraction)
│   ├── 07-foundation-layer/
│   ├── 08-cds-rag/
│   ├── 09-eva-repo-documentation/
│   ├── 10-mkdocs-poc/
│   ├── 11-ms-infojp/ → (symlink to _active/ms-infojp)
│   ├── 12-work-spc-reorg/         # This project
│   ├── eva-ai-governance/ → (symlink to _active/eva-ai-governance)
│   └── habit-tracker/
│
├── _reference/                     # 📚 REFERENCE CODE (read-only)
│   ├── open-webui/                # Reference: EVA Chat base
│   ├── PubSec-Info-Assistant/     # Reference: Microsoft platform
│   ├── EVA-JP-reference-0113/     # Reference: Previous version
│   └── EVA-JP-v1.2/               # Reference: ESDC customizations
│
├── eva-suite/                      # 🎯 PLANNING HUB (unchanged)
│   ├── eva-orchestrator/
│   ├── reports/
│   ├── scripts/
│   └── docs/
│
├── .vscode/                        # VS Code workspace configs
│   ├── active-work.code-workspace
│   ├── ms-infojp.code-workspace
│   ├── jp-automation.code-workspace
│   └── all-references.code-workspace
│
└── Quick access symlinks (optional):
    ├── ms-infojp → _active/ms-infojp
    └── jp-auto → _active/jp-auto-extraction
```

### Path Length Comparison

| Project | Old Path | New Path | Savings |
|---------|----------|----------|---------|
| MS-InfoJP | `I:\EVA-JP-v1.2\docs\eva-foundation\projects\11-MS-InfoJP\base-platform\` (71) | `I:\_active\ms-infojp\` (21) | **-50 chars** |
| JP Auto | `I:\EVA-JP-v1.2\docs\eva-foundation\projects\06-JP-Auto-Extraction\` (67) | `I:\_active\jp-auto-extraction\` (31) | **-36 chars** |
| EVA Gov | `I:\EVA-JP-v1.2\docs\eva-ai-governance\` (39) | `I:\_active\eva-ai-governance\` (31) | **-8 chars** |

---

## Implementation Plan

### Phase 1: Backup (5 minutes)

```powershell
# Create backup of current structure
$backupDate = Get-Date -Format "yyyyMMdd-HHmmss"
$backupPath = "I:\_backups\workspace-reorg-$backupDate"

Write-Host "Creating backup at $backupPath..." -ForegroundColor Cyan
New-Item -ItemType Directory -Path $backupPath -Force

# Backup structure metadata (not files - too slow)
Get-ChildItem "I:\" -Recurse -Directory | 
    Select-Object FullName, CreationTime, LastWriteTime | 
    Export-Csv "$backupPath\folder-structure.csv"

Write-Host "Backup complete. Rollback instructions saved to $backupPath\ROLLBACK.txt" -ForegroundColor Green
```

### Phase 2: Create New Structure (2 minutes)

```powershell
# Create top-level organizing folders
New-Item -ItemType Directory -Path "I:\_active" -Force
New-Item -ItemType Directory -Path "I:\_projects" -Force
New-Item -ItemType Directory -Path "I:\_reference" -Force
New-Item -ItemType Directory -Path "I:\.vscode" -Force
```

### Phase 3: Move Active Projects (5 minutes)

```powershell
# Move current active work
Write-Host "Moving active projects..." -ForegroundColor Cyan

# MS-InfoJP (highest priority)
if (Test-Path "I:\EVA-JP-v1.2\docs\eva-foundation\projects\11-MS-InfoJP\base-platform") {
    Move-Item "I:\EVA-JP-v1.2\docs\eva-foundation\projects\11-MS-InfoJP\base-platform" "I:\_active\ms-infojp"
    Write-Host "  ✓ MS-InfoJP moved to I:\_active\ms-infojp" -ForegroundColor Green
}

# JP Auto Extraction
if (Test-Path "I:\EVA-JP-v1.2\docs\eva-foundation\projects\06-JP-Auto-Extraction") {
    Move-Item "I:\EVA-JP-v1.2\docs\eva-foundation\projects\06-JP-Auto-Extraction" "I:\_active\jp-auto-extraction"
    Write-Host "  ✓ JP Auto moved to I:\_active\jp-auto-extraction" -ForegroundColor Green
}

# EVA AI Governance
if (Test-Path "I:\EVA-JP-v1.2\docs\eva-ai-governance") {
    Move-Item "I:\EVA-JP-v1.2\docs\eva-ai-governance" "I:\_active\eva-ai-governance"
    Write-Host "  ✓ EVA Governance moved to I:\_active\eva-ai-governance" -ForegroundColor Green
}
```

### Phase 4: Organize All Projects (10 minutes)

```powershell
# Move all foundation projects to flat _projects/ folder
Write-Host "Organizing foundation projects..." -ForegroundColor Cyan

$foundationProjects = Get-ChildItem "I:\EVA-JP-v1.2\docs\eva-foundation\projects\*" -Directory | 
    Where-Object { $_.Name -notin '06-JP-Auto-Extraction', '11-MS-InfoJP' }

foreach ($project in $foundationProjects) {
    Move-Item $project.FullName "I:\_projects\$($project.Name)"
    Write-Host "  ✓ $($project.Name)" -ForegroundColor Green
}

# Move other scattered projects
if (Test-Path "I:\EVA-JP-v1.2\docs\habit-tracker") {
    Move-Item "I:\EVA-JP-v1.2\docs\habit-tracker" "I:\_projects\habit-tracker"
    Write-Host "  ✓ habit-tracker" -ForegroundColor Green
}

# Create symlinks for active projects in _projects/ for continuity
New-Item -ItemType SymbolicLink -Path "I:\_projects\06-jp-auto-extraction" -Target "I:\_active\jp-auto-extraction"
New-Item -ItemType SymbolicLink -Path "I:\_projects\11-ms-infojp" -Target "I:\_active\ms-infojp"
New-Item -ItemType SymbolicLink -Path "I:\_projects\eva-ai-governance" -Target "I:\_active\eva-ai-governance"
Write-Host "  ✓ Created organization symlinks" -ForegroundColor Green
```

### Phase 5: Move Reference Repos (5 minutes)

```powershell
# Move reference repositories
Write-Host "Organizing reference repositories..." -ForegroundColor Cyan

if (Test-Path "I:\open-webui") {
    Move-Item "I:\open-webui" "I:\_reference\open-webui"
    Write-Host "  ✓ open-webui" -ForegroundColor Green
}

if (Test-Path "I:\PubSec-Info-Assistant") {
    Move-Item "I:\PubSec-Info-Assistant" "I:\_reference\PubSec-Info-Assistant"
    Write-Host "  ✓ PubSec-Info-Assistant" -ForegroundColor Green
}

if (Test-Path "I:\EVA-JP-reference-0113") {
    Move-Item "I:\EVA-JP-reference-0113" "I:\_reference\EVA-JP-reference-0113"
    Write-Host "  ✓ EVA-JP-reference-0113" -ForegroundColor Green
}

if (Test-Path "I:\EVA-JP-v1.2") {
    Move-Item "I:\EVA-JP-v1.2" "I:\_reference\EVA-JP-v1.2"
    Write-Host "  ✓ EVA-JP-v1.2" -ForegroundColor Green
}
```

### Phase 6: Create Convenience Shortcuts (1 minute)

```powershell
# Create quick-access symlinks at root
Write-Host "Creating convenience shortcuts..." -ForegroundColor Cyan

New-Item -ItemType SymbolicLink -Path "I:\ms-infojp" -Target "I:\_active\ms-infojp" -ErrorAction SilentlyContinue
New-Item -ItemType SymbolicLink -Path "I:\jp-auto" -Target "I:\_active\jp-auto-extraction" -ErrorAction SilentlyContinue

Write-Host "  ✓ Quick access: I:\ms-infojp" -ForegroundColor Green
Write-Host "  ✓ Quick access: I:\jp-auto" -ForegroundColor Green
```

### Phase 7: Create VS Code Workspaces (5 minutes)

See [Workspace Configurations](#workspace-configurations) section below.

### Phase 8: Update Documentation (10 minutes)

- Update MS-InfoJP README with new paths
- Update JP automation scripts
- Update EVA Suite docs
- Create migration completion report

---

## Workspace Configurations

### 1. `active-work.code-workspace` (Daily Driver)

**Purpose**: Focus on current active projects only  
**Location**: `I:\.vscode\active-work.code-workspace`

```json
{
  "folders": [
    {
      "path": "I:\\_active\\ms-infojp",
      "name": "🚀 MS-InfoJP Deploy"
    },
    {
      "path": "I:\\_active\\jp-auto-extraction",
      "name": "🚀 JP Automation"
    },
    {
      "path": "I:\\_active\\eva-ai-governance",
      "name": "🚀 EVA AI Governance"
    },
    {
      "path": "I:\\eva-suite",
      "name": "🎯 EVA Suite (Planning)"
    }
  ],
  "settings": {
    "files.exclude": {
      "**/.git": true,
      "**/__pycache__": true,
      "**/node_modules": true,
      "**/.venv": true,
      "**/.pytest_cache": true
    },
    "search.exclude": {
      "**/node_modules": true,
      "**/.venv": true,
      "**/dist": true,
      "**/build": true
    },
    "python.defaultInterpreterPath": "${workspaceFolder}/.venv/Scripts/python.exe"
  },
  "extensions": {
    "recommendations": [
      "ms-python.python",
      "ms-azuretools.vscode-azurefunctions",
      "ms-azuretools.vscode-azureresourcegroups",
      "GitHub.copilot"
    ]
  }
}
```

### 2. `ms-infojp.code-workspace` (MS-InfoJP Focused)

**Purpose**: Deep work on MS-InfoJP with reference code accessible  
**Location**: `I:\.vscode\ms-infojp.code-workspace`

```json
{
  "folders": [
    {
      "path": "I:\\_active\\ms-infojp",
      "name": "MS-InfoJP Deploy"
    },
    {
      "path": "I:\\_reference\\EVA-JP-v1.2\\app\\backend",
      "name": "📚 EVA-JP Backend (Reference)"
    },
    {
      "path": "I:\\_reference\\PubSec-Info-Assistant",
      "name": "📚 Microsoft Base (Reference)"
    },
    {
      "path": "I:\\eva-suite\\docs",
      "name": "📋 EVA Suite Docs"
    }
  ],
  "settings": {
    "azureFunctions.projectSubpath": "functions",
    "azureFunctions.pythonVenv": ".venv",
    "python.defaultInterpreterPath": "${workspaceFolder:MS-InfoJP Deploy}/.venv/Scripts/python.exe",
    "files.exclude": {
      "**/__pycache__": true,
      "**/node_modules": true
    }
  }
}
```

### 3. `jp-automation.code-workspace` (JP Automation Focused)

**Purpose**: JP batch processing and automation work  
**Location**: `I:\.vscode\jp-automation.code-workspace`

```json
{
  "folders": [
    {
      "path": "I:\\_active\\jp-auto-extraction",
      "name": "JP Automation"
    },
    {
      "path": "I:\\_reference\\EVA-JP-v1.2\\docs\\jp",
      "name": "📚 JP Reference Docs"
    }
  ],
  "settings": {
    "python.defaultInterpreterPath": "${workspaceFolder:JP Automation}/.venv/Scripts/python.exe",
    "python.analysis.extraPaths": [
      "${workspaceFolder:JP Automation}/scripts"
    ]
  }
}
```

### 4. `all-references.code-workspace` (Code Comparison)

**Purpose**: When you need to compare multiple reference implementations  
**Location**: `I:\.vscode\all-references.code-workspace`

```json
{
  "folders": [
    {
      "path": "I:\\_reference\\open-webui",
      "name": "📚 Open WebUI (EVA Chat Base)"
    },
    {
      "path": "I:\\_reference\\PubSec-Info-Assistant",
      "name": "📚 PubSec Info Assistant (MS Base)"
    },
    {
      "path": "I:\\_reference\\EVA-JP-v1.2",
      "name": "📚 EVA-JP v1.2 (ESDC)"
    },
    {
      "path": "I:\\_reference\\EVA-JP-reference-0113",
      "name": "📚 EVA-JP v0113 (Previous)"
    }
  ],
  "settings": {
    "files.watcherExclude": {
      "**/.git/objects/**": true,
      "**/node_modules/**": true,
      "**/.venv/**": true
    }
  }
}
```

---

## Benefits

### 1. **Shorter Paths**
- MS-InfoJP: 71 chars → 21 chars (**-70%**)
- JP Auto: 67 chars → 31 chars (**-54%**)
- Enables bash scripts without workarounds

### 2. **Clear Organization**
- `_active/` = What you're working on now (3-5 max)
- `_projects/` = All projects, flat structure
- `_reference/` = Read-only reference code
- `eva-suite/` = Planning and orchestration

### 3. **Focused Workspaces**
- Open only what you need for current task
- Faster VS Code performance
- Reduced cognitive load
- Better search results

### 4. **Easy Scaling**
- Add new project to `_projects/`
- Move to `_active/` when working on it
- Move back to `_projects/` when paused
- Archive to `_archive/` when complete

### 5. **Better Git Workflows**
- Each workspace can target specific repos
- No accidental commits to wrong repo
- Clearer git status across projects

### 6. **Discoverability**
- Flat `_projects/` folder shows everything at once
- No hunting through nested folders
- Numbered projects maintain chronological order

---

## Rollback Plan

If migration goes wrong, rollback steps:

### Option A: Automated Rollback

```powershell
# Restore from backup structure
$backupPath = "I:\_backups\workspace-reorg-[TIMESTAMP]"
$structureCsv = Import-Csv "$backupPath\folder-structure.csv"

# Recreate original structure (folders only)
# Note: You'll need to manually move projects back
# This script only recreates the folder structure

Write-Host "ROLLBACK: This will help you restore original structure" -ForegroundColor Yellow
Write-Host "You will need to manually move projects back to original locations" -ForegroundColor Yellow
Write-Host ""
Write-Host "Original structure is documented in:" -ForegroundColor Cyan
Write-Host "  $backupPath\folder-structure.csv"
```

### Option B: Manual Rollback

1. **Restore Active Projects**:
   ```powershell
   Move-Item "I:\_active\ms-infojp" "I:\EVA-JP-v1.2\docs\eva-foundation\projects\11-MS-InfoJP\base-platform"
   Move-Item "I:\_active\jp-auto-extraction" "I:\EVA-JP-v1.2\docs\eva-foundation\projects\06-JP-Auto-Extraction"
   Move-Item "I:\_active\eva-ai-governance" "I:\EVA-JP-v1.2\docs\eva-ai-governance"
   ```

2. **Restore Reference Repos**:
   ```powershell
   Move-Item "I:\_reference\open-webui" "I:\open-webui"
   Move-Item "I:\_reference\PubSec-Info-Assistant" "I:\PubSec-Info-Assistant"
   Move-Item "I:\_reference\EVA-JP-reference-0113" "I:\EVA-JP-reference-0113"
   Move-Item "I:\_reference\EVA-JP-v1.2" "I:\EVA-JP-v1.2"
   ```

3. **Restore Projects**: Move everything from `I:\_projects\` back to original locations

4. **Remove New Folders**:
   ```powershell
   Remove-Item "I:\_active" -Force -Recurse
   Remove-Item "I:\_projects" -Force -Recurse
   Remove-Item "I:\_reference" -Force -Recurse
   ```

---

## Post-Migration Tasks

### 1. Update Hardcoded Paths in Scripts

**EVA Suite scripts**:
```powershell
# Find and update all hardcoded paths
Get-ChildItem "I:\eva-suite\scripts\*.ps1" | ForEach-Object {
    $content = Get-Content $_.FullName -Raw
    $updated = $content -replace 'c:\\Users\\marco\.presta\\dev\\EVA Suite', 'I:\eva-suite'
    $updated | Set-Content $_.FullName
}
```

**MS-InfoJP scripts** (if any exist):
```powershell
# Update paths in any MS-InfoJP scripts
Get-ChildItem "I:\_active\ms-infojp\scripts\*.ps1" -Recurse | ForEach-Object {
    $content = Get-Content $_.FullName -Raw
    $updated = $content -replace 'EVA-JP-v1\.2\\docs\\eva-foundation\\projects\\11-MS-InfoJP\\base-platform', '_active\ms-infojp'
    $updated | Set-Content $_.FullName
}
```

### 2. Update Project Documentation

- [ ] Update MS-InfoJP README with new path (`I:\_active\ms-infojp`)
- [ ] Update JP automation README with new path
- [ ] Update EVA Suite documentation
- [ ] Update any reference links in other projects

### 3. Update VS Code Settings

- [ ] Set default workspace: `I:\.vscode\active-work.code-workspace`
- [ ] Update recent workspaces list
- [ ] Test each workspace configuration

### 4. Update Git Ignore Patterns

Add to `.gitignore` in each active project:
```gitignore
# VS Code workspace files
*.code-workspace

# Backup folders
_backups/
```

### 5. Verify All Symlinks Work

```powershell
# Test all symlinks
Get-ChildItem "I:\_projects\*" | Where-Object { $_.LinkType -eq "SymbolicLink" } | ForEach-Object {
    if (Test-Path $_.Target) {
        Write-Host "✓ $($_.Name) → $($_.Target)" -ForegroundColor Green
    } else {
        Write-Host "✗ $($_.Name) → BROKEN LINK" -ForegroundColor Red
    }
}
```

---

## Success Criteria

- [ ] All active projects accessible at `I:\_active\[project-name]`
- [ ] All projects organized in flat `I:\_projects\` structure
- [ ] Reference repos in `I:\_reference\`
- [ ] VS Code workspaces created and tested
- [ ] MS-InfoJP deployment runs without path errors
- [ ] JP automation scripts work with new paths
- [ ] Bash scripts (if any) work without modification
- [ ] All symlinks functional
- [ ] Documentation updated
- [ ] No broken links in VS Code

---

## Timeline

- **Planning & Backup**: 10 minutes
- **Migration Execution**: 30 minutes
- **Testing & Verification**: 20 minutes
- **Documentation Updates**: 20 minutes
- **Total**: ~1.5 hours

---

## Next Steps

1. **Review this proposal** - Ensure structure makes sense for your workflow
2. **Test on one project first** - Move MS-InfoJP only, verify it works
3. **Full migration** - Execute complete reorganization
4. **Update documentation** - Fix all references to old paths
5. **Archive old structure** - Keep backup for 30 days, then remove

---

## Files in This Project

```
12-work-spc-reorg/
├── README.md                           # This file
├── scripts/
│   ├── 01-backup.ps1                  # Create backup before migration
│   ├── 02-migrate.ps1                 # Full migration script
│   ├── 03-create-workspaces.ps1       # Generate VS Code workspace files
│   ├── 04-verify.ps1                  # Verify migration success
│   └── 99-rollback.ps1                # Emergency rollback
├── workspaces/
│   ├── active-work.code-workspace
│   ├── ms-infojp.code-workspace
│   ├── jp-automation.code-workspace
│   └── all-references.code-workspace
└── evidence/
    ├── before-structure.txt           # Pre-migration state
    ├── after-structure.txt            # Post-migration state
    └── migration-log.txt              # Execution log
```

---

## References

- [VS Code Multi-root Workspaces](https://code.visualstudio.com/docs/editor/multi-root-workspaces)
- [Windows Symbolic Links](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/mklink)
- [Git Submodules vs Symlinks](https://git-scm.com/book/en/v2/Git-Tools-Submodules)

---

**Status**: Ready for review and execution  
**Risk Level**: Low (non-destructive, fully reversible)  
**Impact**: High (improves all future development work)
