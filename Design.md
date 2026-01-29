Scan and analyze Kubernetes/Rancher repository structures to understand microservices architecture, component relationships, and error-to-code mapping. Use when the user wants to (1) Understand a repository's structure and what each component does, (2) Map error messages or codes to specific components/files, (3) Analyze K8s/Go codebases, (4) Get component trigger conditions and dependencies, (5) Navigate large microservices repositories. Particularly useful for Kubernetes deployments, Rancher projects, and Go-based microservices.Repo Scanner Skill
Analyze repository structures to understand microservices architecture, component relationships, and map errors to specific code locations.
Phase-Based Workflow
Phase 1: Repository Access & File Tree Construction
Objective: Access the repository and build a complete file tree with language detection.

Access the repository:

If given a Git URL: Clone with git clone <url> /home/claude/scan-target
If given a local path: Use the path directly (likely in /mnt/user-data/uploads/)


Build file tree: Use the scripts/build_tree.py script to generate a complete directory structure with file counts and sizes.
Detect languages: Use the scripts/detect_languages.py script to identify programming languages and their distribution.

Phase 2: Component Detection
Objective: Identify microservices, modules, and components using heuristics and patterns.

Read component detection guide: Load references/k8s_patterns.md to understand Kubernetes/Rancher-specific patterns.
Run component detector: Use scripts/detect_components.py to identify:

Microservice directories (based on folder structure)
Entry points (main.go, cmd/, etc.)
Configuration files (Dockerfile, k8s manifests, Helm charts)
API definitions (gRPC, REST, GraphQL)


Group related paths: Organize files into logical component groups.

Phase 3: Component Analysis & Documentation
Objective: Generate explanations for what each component does and when it triggers.

Static analysis first:

Parse entry points to understand initialization
Extract API routes and handlers
Identify event listeners and triggers
Map dependencies between components


Generate component map: Create a structured document showing:

Component name and purpose
Entry points and main files
Trigger conditions (HTTP endpoints, events, cron jobs, etc.)
Dependencies on other components
Key configuration files


For deeper analysis: Read key files to understand business logic (use sparingly to manage context).

Phase 4: Error-to-Component Mapping
Objective: When given an error message or code, identify relevant components to check.

Extract error keywords: Parse the error message for:

Service names
File paths
Function/method names
Error codes or types
Technology keywords (database, auth, API, etc.)


Match to components: Use scripts/error_matcher.py to:

Search for keyword matches in component names
Check file paths mentioned in errors
Match error types to component responsibilities
Rank components by relevance


Provide navigation guidance: Return:

Top 3-5 most likely components
Specific files to check within each component
Related components that might be affected
Suggested search patterns for the codebase



Phase 5: Output Generation
Objective: Present findings in clear, actionable formats.

Markdown report (default): Human-readable documentation including:

Repository overview (languages, structure)
Component inventory with descriptions
Architecture diagram (text-based)
Error-to-component quick reference


JSON output (for tooling): Machine-readable format including:

File tree structure
Component definitions
Dependency graph
Error keyword mappings


Save outputs: Place files in /mnt/user-data/outputs/ for user access.

Quick Start Examples

**Example 1**: Scan a repository
User: "Analyze this Rancher repository and tell me what each component does"
Claude: [Runs phases 1-3, generates markdown report]

**Example 2**: Map an error
User: "I'm getting 'auth service connection refused' - which components should I check?"
Claude: [Runs phase 4, suggests auth-service, API gateway, and related config files]


**Example 3:** Understand triggers
User: "When does the backup-manager component run?"
Claude: [Analyzes component, finds cron schedule, event handlers, or API triggers]
Bundled Resources

scripts/build_tree.py: Generate directory tree with stats
scripts/detect_languages.py: Identify languages and distribution
scripts/detect_components.py: Find microservices and modules using heuristics
scripts/error_matcher.py: Match error messages to components
references/k8s_patterns.md: Kubernetes/Rancher component patterns
references/error_keywords.md: Common error keywords and mappings

Tips for Best Results

Start broad (phases 1-2) before diving deep into specific files
Use component detection to guide which files to read in detail
For large repos (>1000 files), focus on key directories first
Error matching works best with specific error messages including stack traces
JSON output is ideal for building tools on top of this analysis
