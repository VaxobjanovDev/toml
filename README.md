# TOML Documentation Generator Skill - Complete Guide

## What Was Improved

Your original skill concept was excellent! I've transformed it into a production-ready skill following Anthropic's best practices:

### 1. **Proper Structure & Format**
- ✅ Correct YAML frontmatter with comprehensive description (triggers skill usage)
- ✅ Kebab-case naming (`toml-docs`)
- ✅ Clear, imperative instructions
- ✅ Organized into logical sections

### 2. **Enhanced Description (The Trigger)**
The description field is critical—it's how Claude knows when to use your skill. I made it comprehensive:

```yaml
description: Generates structured TOML documentation that mirrors a codebase's 
architecture for AI agent consumption. Use when the user asks to "document my 
project for AI", "generate TOML files", "create metadata for my components", 
"index my codebase", "map out my architecture", "prepare documentation for 
an AI agent"...
```

This ensures the skill triggers on all relevant user requests.

### 3. **Added Bundled Resources**

#### **scripts/generate_toml.py**
A Python utility that:
- Scans source directories for TypeScript/JavaScript files
- Extracts component metadata (props, types, dependencies)
- Generates initial TOML files with TODOs for manual refinement
- Handles components, hooks, pages, utilities, and type definitions
- Preserves folder structure automatically

**Usage:**
```bash
python generate_toml.py src/ toml_docs/
```

#### **references/examples.md**
Comprehensive examples showing:
- Simple presentational components
- Data-fetching containers
- Custom hooks (data fetching & form state)
- Page/route components
- Utility functions
- Type definitions

Each example includes both source code and the expected TOML output with deep contextualization.

#### **assets/template.toml**
A ready-to-use template with:
- All required TOML tables ([metadata], [properties], [context])
- Inline comments explaining each field
- Examples for different file types
- Optional recommended fields

### 4. **Improved Content Organization**

#### **SKILL.md Structure:**
1. **Core Workflow** - 3-step process (Analyze → Process → Generate)
2. **TOML Structure Requirements** - Mandatory tables with examples
3. **Quality Standards** - What makes great vs. poor documentation
4. **File Processing Rules** - What to include/exclude
5. **Example Output** - Gold standard for ProfileCard component
6. **Anti-Patterns** - Common mistakes to avoid
7. **Execution Strategy** - How to actually use the skill
8. **Success Criteria** - What "done" looks like

### 5. **Key Improvements to Your Original Concept**

**Your Original:**
```
description = "A sophisticated profile display card featuring async image loading..."
```

**Enhanced Version:**
```
description = "User profile display card with async data loading, follow/unfollow 
interactions, and responsive layout variants. Serves as primary user representation 
in discovery and social features."
```

The enhancement:
- Specifies **what** it does (async loading, interactions, layouts)
- Explains **where** it's used (discovery, social features)
- Clarifies **architectural role** (primary user representation)

## How to Use This Skill

### Installation
1. Download the `toml-docs.skill` file
2. Upload it to Claude at https://claude.ai/skills
3. Enable it in your project

### Basic Usage

**User says:**
> "Document my React project for AI agents"

**Claude will:**
1. Ask which directory to scan (default: `src/`)
2. Scan the project structure
3. Process each file and generate TOML documentation
4. Create `toml_docs/` directory mirroring your source structure
5. Generate `toml_docs/INDEX.md` with summary

### Manual TOML Creation

If you prefer manual control:

1. Copy `assets/template.toml` for each file
2. Fill in the three required tables:
   - `[metadata]` - Name, type, path, description
   - `[properties]` - Interface contract (props/params/returns)
   - `[context]` - Architectural positioning

3. Refer to `references/examples.md` for inspiration

### Using the Python Script

For semi-automated generation:

```bash
# Generate initial TOML files with TODOs
python scripts/generate_toml.py src/ toml_docs/

# Review and enhance the generated files
# The script creates a scaffold—you add the semantic depth
```

The script handles:
- ✅ Extracting props from interfaces
- ✅ Detecting file types (component/hook/page/utility)
- ✅ Finding dependencies from imports
- ✅ Creating proper folder structure

You manually add:
- ✅ High-quality descriptions (the "why")
- ✅ Architectural context
- ✅ Usage guidelines
- ✅ State management patterns

## Why This Matters for AI Agents

### Traditional Approach:
```
AI Agent: "Where should I add this feature?"
→ Reads 50 source files
→ Tries to infer architecture
→ Makes educated guess
```

### With TOML Docs:
```
AI Agent: "Where should I add this feature?"
→ Reads toml_docs/INDEX.md
→ Finds relevant components via [context].architectural_role
→ Understands dependencies and state flow
→ Makes informed decision instantly
```

### Real Example:

**User Request:** "Add a follow button to user profiles"

**Without TOML Docs:**
- Agent reads UserProfile.tsx (200 lines)
- Reads 5 imported components to understand structure
- Guesses where button should go
- May miss that follow state is managed by useFollowStatus hook

**With TOML Docs:**
- Agent reads toml_docs/components/user/UserProfile.toml (30 lines)
- Sees: `dependencies = ["useFollowStatus", "Button", ...]`
- Understands: `usage_guideline = "Wrap follow actions in useFollowStatus..."`
- Implements feature correctly first try

## Quality Checklist

When creating TOML docs, ensure each file has:

- [ ] Functional description (not just "This is a...")
- [ ] All props/parameters documented with impact explanation
- [ ] Dependencies listed (both internal components and libraries)
- [ ] Usage guidelines explaining where/when to use
- [ ] Architectural role clearly stated
- [ ] State management approach described
- [ ] NO source code included
- [ ] NO generic descriptions
- [ ] NO flattened hierarchy (preserve folder structure)

## Advanced Features

### For Large Projects
The skill handles batching:
- Processes 20 files at a time for 50+ file projects
- Asks for confirmation before each batch
- Allows targeting specific subdirectories

### Custom Contexts
Add these optional fields for deeper context:
- `performance_notes` - Optimization strategies
- `error_handling` - How errors surface
- `accessibility_notes` - A11y implementation
- `caching_behavior` - Cache keys and invalidation
- `api_contract` - Backend dependencies

### Type Definitions
For TypeScript type files:
```toml
[metadata]
type = "types"

[properties.exports.InterfaceName]
field = { type = "string", description = "..." }
```

## Next Steps

1. **Try the skill** - Upload a small project and generate docs
2. **Refine manually** - Enhance the auto-generated TODOs
3. **Iterate** - Notice what helps AI agents most
4. **Expand** - Add custom context fields for your domain

## Questions & Feedback

The skill is designed to evolve. Common enhancements:
- Add support for Vue/Angular/Svelte
- Include API endpoint documentation
- Generate visual architecture diagrams from TOML
- Create searchable index of all components

Your original concept was spot-on—this implementation just follows Anthropic's proven patterns to make it maximally effective!
