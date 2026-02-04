# TOML Documentation Generator Skill

**Skill Name:** toml-docs  
**Version:** 1.0.0  
**Author:** Custom Skill  
**Category:** Documentation, Code Analysis, AI Tools

---

## Description

Generates structured TOML documentation that mirrors a codebase's architecture for AI agent consumption. Use when the user asks to "document my project for AI", "generate TOML files", "create metadata for my components", "index my codebase", "map out my architecture", "prepare documentation for an AI agent", or wants to create a lightweight structural reference of their repository. Supports React/TypeScript/JavaScript projects with components, pages, hooks, and utilities.

---

## Overview

Transform source code into structured, AI-readable TOML documentation. This skill creates a `toml_docs/` directory that mirrors your project structure with semantic metadata files designed for AI agent comprehension.

---

## Core Workflow

**Step 1: Analyze Project Structure**
- Scan the target directory (typically `src/`)
- Identify file types: components, pages, hooks, utilities, types
- Map folder hierarchy for structural fidelity

**Step 2: Process Each File**
For each code file, extract:
- **Intent**: Core responsibility (e.g., "Authentication flow orchestrator")
- **Interface**: Props, return values, event handlers, context dependencies
- **Dependencies**: Internal imports and external libraries
- **Usage Context**: Where it fits in the architecture

**Step 3: Generate TOML Files**
Create mirrored `toml_docs/` structure with one `.toml` file per source file.

Example mapping:
```
src/components/auth/LoginForm.tsx
  → toml_docs/components/auth/LoginForm.toml
```

---

## TOML Structure Requirements

Every TOML file MUST include these three tables:

### [metadata]
```toml
[metadata]
name = "ComponentName"
type = "component|page|hook|utility|type"
path = "src/path/to/file.tsx"
description = "Functional intent in 1-2 sentences. Focus on WHAT problem it solves, not just what it is."
```

### [properties]
Define the interface contract:
```toml
[properties]
propName = { type = "string|number|boolean|object|function|enum", required = true, default = "value", description = "Impact on component behavior" }
```

For hooks, document return values:
```toml
[properties.return]
data = { type = "User[]", description = "Fetched user list" }
loading = { type = "boolean", description = "Request in-flight status" }
```

### [context]
Architectural positioning:
```toml
[context]
state_management = "Describe state handling approach"
dependencies = ["InternalComponent", "externalLibrary"]
usage_guideline = "Where and how this should be used"
architectural_role = "Layer in the system (e.g., presentation, business logic, data access)"
```

---

## Quality Standards

**Avoid Generic Descriptions**
- ❌ "This is a button component"
- ✅ "Primary CTA trigger supporting async actions, loading states, and theme variants"

**Deep Contextualization**
Include architectural reasoning:
- Why this component exists (not just what it does)
- How state flows through it
- What assumptions it makes about parent components

**Technical Precision**
- Enum values must list all options: `type = "enum['sm', 'md', 'lg']"`
- Function signatures should indicate parameters: `onClick = { type = "function(event: MouseEvent)", description = "..." }`
- Context dependencies must specify source: `"Requires AuthContext from providers/AuthProvider"`

---

## File Processing Rules

**Include:**
- `.tsx`, `.jsx`, `.ts`, `.js` files with logic
- Component files (functional or class)
- Hooks (use*, custom hooks)
- Pages/routes
- Utility functions with exports
- Type definition files with interfaces/types

**Exclude:**
- `.css`, `.scss`, `.module.css`
- Test files (`.test.tsx`, `.spec.ts`)
- Config files (`vite.config.ts`, `tsconfig.json`)
- `.svg`, `.png`, and other assets
- `index.ts` barrel exports (unless they contain logic)

**Special Cases:**
- For files with multiple exports, create a single TOML with `[properties.exports]` table listing each export
- For TypeScript type files, use `type = "types"` and document interfaces/types in `[properties]`

---

## Example Output

### Source File: `src/components/user/ProfileCard.tsx`

```typescript
interface ProfileCardProps {
  userId: string;
  variant?: 'compact' | 'full';
  onFollow?: (userId: string) => void;
}

export const ProfileCard: React.FC<ProfileCardProps> = ({ userId, variant = 'full', onFollow }) => {
  const { user, loading } = useUser(userId);
  const { isFollowing, toggleFollow } = useFollowStatus(userId);
  
  return (/* ... */);
}
```

### Generated TOML: `toml_docs/components/user/ProfileCard.toml`

```toml
[metadata]
name = "ProfileCard"
type = "component"
path = "src/components/user/ProfileCard.tsx"
description = "User profile display card with async data loading, follow/unfollow interactions, and responsive layout variants. Serves as primary user representation in discovery and social features."

[properties]
userId = { type = "string", required = true, description = "UUID for fetching user data via useUser hook" }
variant = { type = "enum['compact', 'full']", required = false, default = "full", description = "Determines detail level: 'compact' shows avatar and name only, 'full' includes bio and stats" }
onFollow = { type = "function(userId: string) => void", required = false, description = "Callback executed when follow button is toggled. Parent must handle API request and state update." }

[context]
state_management = "Uses useUser hook for data fetching (SWR caching). useFollowStatus manages follow state optimistically."
dependencies = ["Avatar", "Badge", "Button", "useUser", "useFollowStatus"]
usage_guideline = "Best suited for sidebar profiles, discovery feeds, and search results. Requires ErrorBoundary wrapper for graceful failure handling. Parent must provide userId from routing or context."
architectural_role = "Presentation layer component. No direct API calls - delegates data fetching to custom hooks."
performance_notes = "Memoized with React.memo to prevent re-renders when parent updates. Variant changes trigger re-layout."
```

---

## Anti-Patterns to Avoid

1. **Never include source code** in TOML files
2. **Never flatten hierarchy** - preserve exact folder structure
3. **Never generate trivial descriptions** - every description must add semantic value
4. **Never document implementation details** - focus on interface and intent
5. **Never skip the `[context]` table** - architectural positioning is critical for AI agents

---

## Execution Strategy

When user triggers this skill:

1. Ask: "Which directory should I document?" (default: `src/`)
2. Scan and build file tree
3. Process files in logical order (utilities → hooks → components → pages)
4. Create mirrored `toml_docs/` structure as you go
5. Report progress every 10 files
6. Generate summary: `toml_docs/INDEX.md` listing all documented files with their types

**For large projects (50+ files):**
- Process in batches of 20 files
- Ask user to confirm before processing next batch
- Provide option to process specific subdirectories only

---

## Success Criteria

A well-documented project enables an AI agent to:
- Understand component responsibilities without reading source code
- Identify where to make changes for feature requests
- Determine component dependencies and impact radius
- Generate new components that match existing patterns
- Refactor code with full awareness of architectural relationships

The `toml_docs/` directory becomes the "DNA map" of the project - a complete semantic index optimized for AI comprehension.

---

## Python Utility Script

Below is a Python script to help automate TOML generation:

```python
#!/usr/bin/env python3
"""
TOML Documentation Generator Utility
Analyzes TypeScript/JavaScript files and generates structured TOML metadata.
"""

import os
import re
from pathlib import Path
from typing import Dict, List, Optional, Tuple


def extract_component_info(file_path: str, content: str) -> Dict:
    """
    Extract component metadata from TypeScript/JavaScript file.
    
    Args:
        file_path: Path to the source file
        content: File content as string
        
    Returns:
        Dictionary with extracted metadata
    """
    info = {
        'name': Path(file_path).stem,
        'type': determine_file_type(file_path, content),
        'path': file_path,
        'description': '',
        'properties': {},
        'dependencies': extract_dependencies(content),
        'exports': extract_exports(content)
    }
    
    # Extract props interface if component
    if info['type'] == 'component':
        info['properties'] = extract_props(content)
    
    # Extract hook return values
    elif info['type'] == 'hook':
        info['properties'] = extract_hook_returns(content)
    
    return info


def determine_file_type(file_path: str, content: str) -> str:
    """Determine the type of file (component, hook, page, utility, type)."""
    filename = Path(file_path).name
    
    # Hooks
    if filename.startswith('use') and filename[3].isupper():
        return 'hook'
    
    # Type definitions
    if '.types.' in filename or '.d.ts' in filename:
        return 'types'
    
    # Check content for React component patterns
    if re.search(r'(export\s+(default\s+)?function|const\s+\w+\s*[:=]\s*\([^)]*\)\s*=>|React\.FC)', content):
        # Check if in pages directory
        if '/pages/' in file_path or '/app/' in file_path:
            return 'page'
        return 'component'
    
    # Default to utility
    return 'utility'


def extract_props(content: str) -> Dict:
    """Extract TypeScript interface props."""
    props = {}
    
    # Match interface/type definitions
    interface_pattern = r'interface\s+(\w+Props)\s*\{([^}]+)\}'
    type_pattern = r'type\s+(\w+Props)\s*=\s*\{([^}]+)\}'
    
    for pattern in [interface_pattern, type_pattern]:
        matches = re.finditer(pattern, content, re.DOTALL)
        for match in matches:
            props_body = match.group(2)
            props.update(parse_props_body(props_body))
    
    return props


def parse_props_body(props_body: str) -> Dict:
    """Parse the body of a props interface."""
    props = {}
    
    # Match prop definitions: name?: type;
    prop_pattern = r'(\w+)(\??):\s*([^;]+);'
    for match in re.finditer(prop_pattern, props_body):
        prop_name = match.group(1)
        optional = match.group(2) == '?'
        prop_type = match.group(3).strip()
        
        props[prop_name] = {
            'type': prop_type,
            'required': not optional,
            'description': ''  # To be filled manually
        }
    
    return props


def extract_hook_returns(content: str) -> Dict:
    """Extract return values from custom hooks."""
    returns = {}
    
    # Look for return statement with object destructuring
    return_pattern = r'return\s+\{([^}]+)\}'
    match = re.search(return_pattern, content)
    
    if match:
        return_body = match.group(1)
        # Extract variable names
        vars_pattern = r'(\w+)'
        for var_match in re.finditer(vars_pattern, return_body):
            var_name = var_match.group(1)
            if var_name not in ['as', 'const']:  # Skip keywords
                returns[var_name] = {
                    'type': 'unknown',  # To be inferred
                    'description': ''
                }
    
    return returns


def extract_dependencies(content: str) -> List[str]:
    """Extract import statements."""
    dependencies = []
    
    # Match import statements
    import_patterns = [
        r"import\s+.*?from\s+['\"]([^'\"]+)['\"]",
        r"import\(['\"]([^'\"]+)['\"]\)",
    ]
    
    for pattern in import_patterns:
        for match in re.finditer(pattern, content):
            dep = match.group(1)
            # Filter out relative paths to focus on libraries
            if not dep.startswith('.'):
                dependencies.append(dep)
            else:
                # Extract component name from relative import
                component_name = Path(dep).stem
                dependencies.append(component_name)
    
    return list(set(dependencies))  # Remove duplicates


def extract_exports(content: str) -> List[str]:
    """Extract exported names."""
    exports = []
    
    # Match export statements
    export_patterns = [
        r'export\s+(?:default\s+)?(?:function|const|class)\s+(\w+)',
        r'export\s+\{\s*([^}]+)\s*\}',
    ]
    
    for pattern in export_patterns:
        for match in re.finditer(pattern, content):
            export_name = match.group(1).strip()
            if ',' in export_name:
                # Multiple exports in single statement
                exports.extend([e.strip() for e in export_name.split(',')])
            else:
                exports.append(export_name)
    
    return exports


def generate_toml(info: Dict) -> str:
    """Generate TOML content from extracted info."""
    toml_lines = []
    
    # Metadata section
    toml_lines.append('[metadata]')
    toml_lines.append(f'name = "{info["name"]}"')
    toml_lines.append(f'type = "{info["type"]}"')
    toml_lines.append(f'path = "{info["path"]}"')
    toml_lines.append(f'description = "TODO: Add functional description"')
    toml_lines.append('')
    
    # Properties section
    toml_lines.append('[properties]')
    if info['properties']:
        for prop_name, prop_info in info['properties'].items():
            required = str(prop_info['required']).lower()
            toml_lines.append(
                f'{prop_name} = {{ type = "{prop_info["type"]}", '
                f'required = {required}, '
                f'description = "TODO: Add description" }}'
            )
    else:
        toml_lines.append('# No properties detected - add manually if needed')
    toml_lines.append('')
    
    # Context section
    toml_lines.append('[context]')
    toml_lines.append('state_management = "TODO: Describe state management approach"')
    
    if info['dependencies']:
        deps_str = '", "'.join(info['dependencies'])
        toml_lines.append(f'dependencies = ["{deps_str}"]')
    else:
        toml_lines.append('dependencies = []')
    
    toml_lines.append('usage_guideline = "TODO: Describe where and how to use this"')
    toml_lines.append('architectural_role = "TODO: Describe architectural layer"')
    
    return '\n'.join(toml_lines)


def process_directory(src_dir: str, output_dir: str, exclude_patterns: List[str] = None):
    """
    Process all files in a directory and generate TOML documentation.
    
    Args:
        src_dir: Source directory to scan
        output_dir: Output directory for TOML files
        exclude_patterns: List of patterns to exclude
    """
    if exclude_patterns is None:
        exclude_patterns = [
            '.test.', '.spec.', '.css', '.scss',
            'node_modules', '__tests__', '.config.'
        ]
    
    src_path = Path(src_dir)
    output_path = Path(output_dir)
    
    # Track processed files
    processed = []
    
    for file_path in src_path.rglob('*.{ts,tsx,js,jsx}'):
        # Skip excluded patterns
        if any(pattern in str(file_path) for pattern in exclude_patterns):
            continue
        
        # Read file content
        try:
            with open(file_path, 'r', encoding='utf-8') as f:
                content = f.read()
        except Exception as e:
            print(f"Error reading {file_path}: {e}")
            continue
        
        # Generate relative path structure
        rel_path = file_path.relative_to(src_path)
        output_file = output_path / rel_path.with_suffix('.toml')
        
        # Create output directory
        output_file.parent.mkdir(parents=True, exist_ok=True)
        
        # Extract info and generate TOML
        info = extract_component_info(str(file_path), content)
        toml_content = generate_toml(info)
        
        # Write TOML file
        with open(output_file, 'w', encoding='utf-8') as f:
            f.write(toml_content)
        
        processed.append({
            'source': str(file_path),
            'output': str(output_file),
            'type': info['type']
        })
        
        print(f"✓ Generated: {output_file}")
    
    return processed


if __name__ == '__main__':
    import sys
    
    if len(sys.argv) < 3:
        print("Usage: python generate_toml.py <source_dir> <output_dir>")
        sys.exit(1)
    
    source_dir = sys.argv[1]
    output_dir = sys.argv[2]
    
    print(f"Processing files in: {source_dir}")
    print(f"Output directory: {output_dir}")
    print("-" * 50)
    
    results = process_directory(source_dir, output_dir)
    
    print("-" * 50)
    print(f"Processed {len(results)} files")
    print("\nNote: All TOML files contain TODO placeholders.")
    print("Review and complete the descriptions manually for best results.")
```

**Usage:**
```bash
python generate_toml.py src/ toml_docs/
```

---

## TOML Template

Use this template when manually creating TOML files:

```toml
# TOML Documentation Template
# Copy this template when manually creating TOML files

[metadata]
name = "ComponentName"
type = "component|page|hook|utility|types"
path = "src/path/to/file.tsx"
description = "Clear, functional description focusing on WHAT problem this solves and WHY it exists. Avoid generic descriptions like 'This is a component that...' Instead: 'Orchestrates user authentication flow with OAuth providers and session management.'"

[properties]
# For Components - document props:
# propName = { type = "string|number|boolean|enum['a','b']|function(...) => void", required = true|false, default = "value", description = "Impact on behavior" }

# For Hooks - document return values:
# [properties.return]
# returnValue = { type = "Type", description = "What this value represents" }

# For Utilities - document parameters and return:
# param1 = { type = "Type", required = true, description = "Purpose" }
# [properties.return]
# result = { type = "Type", description = "Return value" }

# For Types - document exported interfaces:
# [properties.exports.InterfaceName]
# field = { type = "Type", description = "Field purpose" }

[context]
state_management = "Describe how state is managed: local useState, global context, external hook, etc. Include state flow and update triggers."
dependencies = ["InternalComponent", "externalLibrary", "customHook"]
usage_guideline = "Where and how this should be used. Required context (auth, providers). Constraints. Best practices."
architectural_role = "Layer in the system: presentation, container, business logic, data access, utility, type definition."

# Optional but recommended fields:
# performance_notes = "Memoization, lazy loading, render optimization strategies"
# error_handling = "How errors are caught and surfaced to users"
# accessibility_notes = "ARIA labels, keyboard navigation, screen reader support"
# testing_notes = "Key test scenarios and mocking requirements"
# caching_behavior = "Cache keys, invalidation rules, revalidation triggers"
# validation_strategy = "When and how validation occurs"
# localization_notes = "i18n considerations, hardcoded locales"
# seo_metadata = "Page titles, meta descriptions, indexing rules"
# route_config = "URL patterns, route guards, query parameters"
# api_contract = "Backend endpoint dependencies, request/response shapes"
```

---

## Extended Examples

### Example 1: Simple Button Component

**Source:** `src/components/ui/Button.tsx`
```typescript
interface ButtonProps {
  children: React.ReactNode;
  variant?: 'primary' | 'secondary' | 'ghost';
  size?: 'sm' | 'md' | 'lg';
  disabled?: boolean;
  onClick?: (e: React.MouseEvent) => void;
}
```

**TOML:** `toml_docs/components/ui/Button.toml`
```toml
[metadata]
name = "Button"
type = "component"
path = "src/components/ui/Button.tsx"
description = "Polymorphic action trigger supporting multiple visual variants, sizes, and states. Core primitive for all user interactions requiring explicit confirmation."

[properties]
children = { type = "React.ReactNode", required = true, description = "Button label or icon content" }
variant = { type = "enum['primary', 'secondary', 'ghost']", required = false, default = "primary", description = "Visual hierarchy: 'primary' for main actions, 'secondary' for alternatives, 'ghost' for tertiary actions" }
size = { type = "enum['sm', 'md', 'lg']", required = false, default = "md", description = "Physical dimensions matching 8px grid system" }
disabled = { type = "boolean", required = false, default = false, description = "Prevents interaction and applies visual disabled state" }
onClick = { type = "function(e: React.MouseEvent) => void", required = false, description = "Click handler receiving synthetic event. For async operations, component consumer must manage loading state." }

[context]
state_management = "Stateless presentational component. All state managed by parent."
dependencies = []
usage_guideline = "Use as primary interactive element. For navigation, prefer Link component. For form submissions, wrap in form element. Always provide accessible children content."
architectural_role = "Presentation layer primitive. Part of design system foundation."
accessibility_notes = "Semantic button element ensures keyboard navigation and screen reader support. Disabled state prevents focus."
```

### Example 2: Custom Hook

**Source:** `src/hooks/useUser.ts`
```typescript
export function useUser(userId: string) {
  const { data, error, isLoading, mutate } = useSWR(`/api/users/${userId}`, fetcher);
  return { user: data, loading: isLoading, error, refetch: mutate };
}
```

**TOML:** `toml_docs/hooks/useUser.toml`
```toml
[metadata]
name = "useUser"
type = "hook"
path = "src/hooks/useUser.ts"
description = "Fetches and caches individual user data with automatic revalidation. Provides loading states and refetch capability."

[properties]
userId = { type = "string", required = true, description = "User UUID for API request" }

[properties.return]
user = { type = "User | undefined", description = "User object when loaded, undefined while loading or on error" }
loading = { type = "boolean", description = "True during initial fetch and revalidation" }
error = { type = "Error | undefined", description = "Fetch error object if request failed" }
refetch = { type = "function() => Promise<void>", description = "Manual refetch trigger bypassing cache" }

[context]
state_management = "Uses SWR for data fetching with stale-while-revalidate strategy. Cache key: /api/users/{userId}. Auto-revalidates on window focus."
dependencies = ["useSWR", "fetcher"]
usage_guideline = "Call at component level. Suspends during SSR - wrap in Suspense boundary. Multiple components can safely call with same userId (deduplicated)."
architectural_role = "Data access layer. Abstracts API communication from UI components."
caching_behavior = "30-second cache. Revalidates on: window focus, network reconnect, component remount. Shares cache across all consumers."
```

### Example 3: Utility Function

**Source:** `src/utils/formatDate.ts`
```typescript
export function formatDate(date: Date | string, format: 'short' | 'long' = 'short'): string {
  const d = typeof date === 'string' ? new Date(date) : date;
  // formatting logic...
}
```

**TOML:** `toml_docs/utils/formatDate.toml`
```toml
[metadata]
name = "formatDate"
type = "utility"
path = "src/utils/formatDate.ts"
description = "Locale-aware date formatter with short and long format options. Handles both Date objects and ISO strings."

[properties]
date = { type = "Date | string", required = true, description = "Date object or ISO 8601 string to format" }
format = { type = "enum['short', 'long']", required = false, default = "short", description = "'short' returns MM/DD/YYYY, 'long' returns 'Month DD, YYYY'" }

[properties.return]
formatted = { type = "string", description = "Formatted date string in en-US locale" }

[context]
state_management = "Pure function - no state"
dependencies = []
usage_guideline = "Use for user-facing date displays. For relative time ('2 hours ago'), use formatRelativeTime. For date comparisons, use raw Date objects."
architectural_role = "Presentation utility. Part of shared utilities layer."
localization_notes = "Hardcoded to en-US locale. For i18n support, pass locale as parameter or use i18n library."
error_handling = "Throws on invalid date strings. Wrap in try-catch or validate input before calling."
```

---

## Tips for Best Results

1. **Start Small** - Document 5-10 critical files first to establish patterns
2. **Use the Script** - Let automation handle the tedious extraction, then enhance manually
3. **Focus on "Why"** - Don't just describe what code does, explain why it exists
4. **Think Like an Agent** - What would another AI need to know to use/modify this code?
5. **Maintain Hierarchy** - Always preserve the exact folder structure from `src/` to `toml_docs/`
6. **Iterate** - Review generated docs after using them once, refine based on usefulness

---

## License

This skill is provided as-is for documentation and AI agent enhancement purposes.
