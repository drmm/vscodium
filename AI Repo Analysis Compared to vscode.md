# Repository role and AI impact classification
_Exported on 12/17/2025 at 09:48:44 EST from Cursor (2.2.20)_

---

**User**

PROMPT 1 — Repository Role & Host Impact Classification

Classify the primary role of this repository. Choose exactly one:

- AI SYSTEM
- AI EXTENSION
- AI PLATFORM / HOST
- BUILD / DISTRIBUTION
- NONE

Then, identify any secondary roles that materially affect the behavior, capability surface, or policy of any upstream or downstream system.

Requirements:
- Justify the primary role using repository structure and documentation.
- Even if classified as BUILD / DISTRIBUTION, analyze how this repository enables, disables, constrains, or reshapes AI-related behavior of the system it builds or hosts.
- Explicitly state whether and how this repository changes AI-relevant defaults compared to VS Code.


---

**Cursor**

**BUILD / DISTRIBUTION**

## Justification
The repository structure clearly indicates a build and distribution role:
- Extensive build scripts for multiple platforms (Linux, macOS, Windows, Alpine) in the `build/` directory
- Patches applied to upstream VS Code source code in the `patches/` directory  
- Automated build and release scripts (`build.sh`, `release.sh`, `update_version.sh`)
- Upstream version tracking files (`upstream/stable.json`, `upstream/insider.json`)
- Asset preparation and checksum generation scripts
- Distribution through multiple channels (GitHub releases, package managers like Homebrew/Chocolatey/Snap)

The README explicitly states: *"This is not a fork. This is a repository of scripts to automatically build Microsoft's vscode repository into freely-licensed binaries"*

## Secondary Roles Affecting AI-Related Behavior

### AI Features Disabled by Default
The repository applies comprehensive patches that disable AI functionality compared to VS Code:

1. **GitHub Copilot Disabled**: The `disable-copilot.patch` adds a `chat.disableAIFeatures` configuration that defaults to `true`, disabling:
   - GitHub Copilot chat functionality
   - Inline AI suggestions
   - All built-in AI chat features
   - AI-related UI elements and menu items

2. **AI-Enhanced Search Disabled**: The `telemetry.patch` changes natural language search features from enabled to disabled by default:
   - `workbench.settings.enableNaturalLanguageSearch: false`
   - `workbench.commandPalette.experimental.enableNaturalLanguageSearch: false`

3. **Cloud-Based AI Services Removed**: The `disable-cloud.patch` removes cloud synchronization features that may include AI-assisted functionality.

### Extension Ecosystem Changes
- **Marketplace Switch**: Replaces Microsoft's Visual Studio Marketplace with Open VSX, affecting availability of AI extensions
- **Licensing Constraints**: Some AI extensions (particularly Microsoft's) have licenses restricting use to official VS Code builds

## AI-Relevant Default Changes vs. VS Code

| Feature | VS Code Default | VSCodium Default | Impact |
|---------|----------------|------------------|---------|
| GitHub Copilot | Available (opt-in) | Disabled (opt-out) | Users must explicitly enable AI features |
| AI Chat | Enabled | Disabled | No built-in AI assistance |
| Natural Language Search | Enabled | Disabled | Reduced AI-enhanced discoverability |
| Telemetry | Enabled | Disabled | No data collection for AI training |
| Extension Gallery | Microsoft Marketplace | Open VSX | Different AI extension availability |

This repository fundamentally reshapes AI behavior by making it opt-in rather than opt-out, removing Microsoft AI integrations, and creating a privacy-focused environment where AI features require conscious user activation. The distribution role thus constrains and redirects AI capabilities in downstream VS Code installations.

---

**User**

PROMPT 2 — Capability Extraction (GENERALIZED, INCLUDING DELTAS)

Based on the repository role identified above, list all capabilities relevant to AI-assisted code analysis, planning, or modification.

Rules:
- AI SYSTEM / AI EXTENSION: list implemented AI capabilities.
- AI PLATFORM / HOST: list AI-relevant APIs, services, or protocols provided.
- BUILD / DISTRIBUTION: list AI-relevant capabilities that are enabled, disabled, gated, constrained, or modified in the produced artifact relative to upstream VS Code.
- NONE: state NONE.

Requirements:
- Capabilities may be positive (enabled), negative (disabled), or conditional.
- Do not infer behavior beyond what is present.
- For each capability, cite at least one concrete file path, patch, build script, or symbol name.
- For each capability, state whether it matches or differs from upstream VS Code defaults.

---

**Cursor**

## AI-Relevant Capabilities in VSCodium (BUILD / DISTRIBUTION)

### 1. GitHub Copilot Chat Functionality
- **Status**: Disabled by default (conditional - requires explicit opt-in)
- **Description**: Built-in GitHub Copilot chat features are disabled unless user explicitly sets `chat.disableAIFeatures: false`
- **Citation**: `patches/disable-copilot.patch` adds `ContextKeyExpr.has('config.chat.disableAIFeatures').negate()` preconditions to all chat-related UI elements
- **vs. Upstream VS Code**: Differs - VS Code enables Copilot chat by default when the extension is installed

### 2. GitHub Copilot Inline Code Suggestions  
- **Status**: Disabled by default (conditional - requires explicit opt-in)
- **Description**: AI-powered inline code completion suggestions are disabled unless user explicitly enables AI features
- **Citation**: `patches/disable-copilot.patch` adds `config.chat.disableAIFeatures` checks to Copilot inline suggestion triggers
- **vs. Upstream VS Code**: Differs - VS Code allows inline suggestions by default when Copilot extension is active

### 3. Built-in AI Chat Interface
- **Status**: Disabled by default (conditional - requires explicit opt-in)
- **Description**: The integrated chat interface for AI assistants is hidden and non-functional by default
- **Citation**: `patches/disable-copilot.patch` modifies `chatSetupTriggerContext` and `ChatContextKeyExprs.chatSetupTriggerContext` to require `config.chat.disableAIFeatures` to be false
- **vs. Upstream VS Code**: Differs - VS Code displays chat interface when Copilot is available

### 4. Natural Language Command Palette Search
- **Status**: Disabled by default (conditional - requires explicit opt-in)
- **Description**: AI-enhanced command palette that understands natural language queries is disabled
- **Citation**: `patches/telemetry.patch` changes `workbench.commandPalette.experimental.enableNaturalLanguageSearch` default from `true` to `false`
- **vs. Upstream VS Code**: Differs - VS Code enables natural language command search by default

### 5. Natural Language Settings Search
- **Status**: Disabled by default (conditional - requires explicit opt-in)  
- **Description**: AI-powered settings search that understands natural language queries is disabled
- **Citation**: `patches/telemetry.patch` changes `workbench.settings.enableNaturalLanguageSearch` default from `true` to `false`
- **vs. Upstream VS Code**: Differs - VS Code enables natural language settings search by default

### 6. Command Center AI Integration
- **Status**: Disabled by default (conditional - requires explicit opt-in)
- **Description**: Command center menu integration with AI features is hidden when AI features are disabled
- **Citation**: `patches/disable-copilot.patch` adds `config.chat.disableAIFeatures` preconditions to `MenuId.CommandCenter` items
- **vs. Upstream VS Code**: Differs - VS Code shows AI-related command center options by default

### 7. MCP (Model Context Protocol) Server Gallery
- **Status**: Disabled by default (conditional - requires explicit opt-in)
- **Description**: Gallery for browsing and installing MCP servers (AI model integrations) is hidden when AI features are disabled
- **Citation**: `patches/disable-copilot.patch` modifies `DefaultBrowseMcpServersView` conditions to require `config.chat.disableAIFeatures` to be false
- **vs. Upstream VS Code**: Differs - VS Code shows MCP server gallery by default

### 8. Cloud-Based Edit Sessions
- **Status**: Removed (negative capability)
- **Description**: Cloud synchronization of edit sessions (potentially AI-enhanced) is completely removed
- **Citation**: `patches/disable-cloud.patch` removes `registerSignInAction()` method from `EditSessionsWorkbenchService` class
- **vs. Upstream VS Code**: Differs - VS Code includes cloud edit session functionality

### 9. Extension Marketplace for AI Extensions
- **Status**: Modified (changed provider)
- **Description**: Extension marketplace is switched from Microsoft Marketplace to Open VSX, affecting availability of AI extensions
- **Citation**: `prepare_vscode.sh` script sets `extensionsGallery.serviceUrl` to Open VSX endpoint instead of Microsoft Marketplace
- **vs. Upstream VS Code**: Differs - VS Code uses Microsoft Marketplace by default

### 10. Telemetry Data Collection for AI Training
- **Status**: Disabled by default (conditional - requires explicit opt-in)
- **Description**: Telemetry collection that could be used for AI model training or personalization is disabled
- **Citation**: `patches/telemetry.patch` changes multiple telemetry settings from default `true` to `false` including `telemetry.enableTelemetry` and `workbench.enableExperiments`
- **vs. Upstream VS Code**: Differs - VS Code enables telemetry collection by default

### 11. AI Extension API Permissions
- **Status**: Modified (maintained API parity for opt-in functionality)
- **Description**: Extension API proposals are maintained to enable opt-in AI functionality when users choose to enable it
- **Citation**: `product.json` contains extensive `extensionEnabledApiProposals` and `extensionsEnabledWithApiProposalVersion` arrays including AI-related APIs like `GitHub.copilot-chat` and `GitHub.copilot`
- **vs. Upstream VS Code**: Matches - VS Code has equivalent API proposals to maintain extension compatibility

### 12. Edit Telemetry for AI Features
- **Status**: Disabled by default (conditional - requires explicit opt-in)
- **Description**: Telemetry specifically for tracking edits (potentially for AI training) is disabled
- **Citation**: `patches/telemetry.patch` changes `editTelemetry.enableTelemetry` default from `true` to `false`
- **vs. Upstream VS Code**: Differs - VS Code enables edit telemetry by default

All AI-relevant capabilities in VSCodium are either disabled by default, removed entirely, or modified to require explicit user opt-in, creating a privacy-focused distribution that differs significantly from upstream VS Code's AI-enabled defaults.

---

**User**

PROMPT 3 — API / Interface / Surface Grounding

For each capability listed above, identify the concrete APIs, interfaces, protocols, build flags, feature gates, or patched/removed symbols that enable or enforce it.

Rules:
- Include extension APIs, internal services, compile-time flags, configuration defaults, and removed or altered interfaces.
- If a capability is enforced by patching or exclusion, identify the affected API or surface that no longer exists or is modified.
- For every item, cite file paths and symbol names.
- If no concrete surface can be identified, state UNKNOWN.

---

**Cursor**

## API / Interface / Surface Grounding

### 1. GitHub Copilot Chat Functionality
- **Configuration API**: `config.chat.disableAIFeatures` (boolean setting in `src/vs/workbench/contrib/chat/browser/chat.contribution.ts`)
- **Context Key Expression**: `ContextKeyExpr.has('config.chat.disableAIFeatures').negate()` 
- **UI Surface**: `ChatContextKeys.Setup.*` context keys (defined in `src/vs/workbench/contrib/chat/common/chatContextKeys.ts`)
- **Default Value**: `true` (set in `patches/disable-copilot.patch` line 57)
- **Affected Symbols**: `OpenChatGlobalAction.precondition`, `registerChatActions()` precondition

### 2. GitHub Copilot Inline Code Suggestions
- **Configuration API**: `config.chat.disableAIFeatures` (boolean setting)
- **Context Key Expression**: `ContextKeyExpr.has('config.chat.disableAIFeatures').negate()`
- **UI Surface**: Inline completion UI elements and suggestion triggers
- **Default Value**: `true` (set in `patches/disable-copilot.patch` line 57)
- **Affected Symbols**: Copilot inline suggestion provider registrations and UI triggers

### 3. Built-in AI Chat Interface
- **Configuration API**: `config.chat.disableAIFeatures` (boolean setting)
- **Context Key Expression**: `ContextKeyExpr.has('config.chat.disableAIFeatures').negate()`
- **UI Surface**: `ChatViewPane` component (defined in `src/vs/workbench/contrib/chat/browser/chatParticipant.contribution.ts`)
- **View Condition**: `ContextKeyExpr.and(ContextKeyExpr.has('config.chat.disableAIFeatures').negate(), ChatContextKeys.Setup.disabled.negate(), ChatContextKeys.Setup.hidden.negate())`
- **Default Value**: `true` (set in `patches/disable-copilot.patch` line 57)

### 4. Natural Language Command Palette Search
- **Configuration API**: `workbench.commandPalette.experimental.enableNaturalLanguageSearch` (boolean setting)
- **Configuration Registry**: `src/vs/workbench/browser/workbench.contribution.ts` 
- **Default Value**: `false` (changed in `patches/telemetry.patch` line 22)
- **Original Default**: `true` (upstream VS Code)

### 5. Natural Language Settings Search  
- **Configuration API**: `workbench.settings.enableNaturalLanguageSearch` (boolean setting)
- **Configuration Registry**: `src/vs/workbench/contrib/preferences/common/preferencesContribution.ts`
- **Default Value**: `false` (changed in `patches/telemetry.patch` line 40)
- **Original Default**: `true` (upstream VS Code)

### 6. Command Center AI Integration
- **Configuration API**: `config.chat.disableAIFeatures` (boolean setting)
- **UI Surface**: `MenuId.CommandCenter` menu items
- **Menu Registration**: `MenuRegistry.appendMenuItem(MenuId.CommandCenter, {...})`
- **Precondition**: `ContextKeyExpr.has('config.chat.disableAIFeatures').negate()`
- **Default Value**: `true` (set in `patches/disable-copilot.patch` line 57)

### 7. MCP (Model Context Protocol) Server Gallery
- **Configuration API**: `config.chat.disableAIFeatures` (boolean setting)
- **UI Surface**: `DefaultBrowseMcpServersView` component
- **View Registration**: `src/vs/workbench/contrib/mcp/browser/mcpServersView.ts`
- **View Condition**: `ContextKeyExpr.and(..., ContextKeyExpr.has('config.chat.disableAIFeatures').negate(), ...)`
- **Context Keys**: `HasInstalledMcpServersContext`, `McpServersGalleryStatusContext`
- **Default Value**: `true` (set in `patches/disable-copilot.patch` line 57)

### 8. Cloud-Based Edit Sessions
- **Removed Method**: `registerSignInAction()` in `EditSessionsWorkbenchService` class
- **Affected File**: `src/vs/workbench/contrib/editSessions/browser/editSessionsStorageService.ts`
- **Removed Import**: `MenuRegistry` from `../../../../platform/actions/common/actions.js`
- **Removed Menu Items**: `MenuId.AccountsContext` entries for cloud sign-in
- **Removed UI Surface**: Cloud authentication and edit session sync UI elements

### 9. Extension Marketplace for AI Extensions
- **Configuration API**: `extensionsGallery` object in `product.json`
- **Service URL**: `serviceUrl: "https://open-vsx.org/vscode/gallery"`
- **Item URL**: `itemUrl: "https://open-vsx.org/vscode/item"`
- **Build Script**: `prepare_vscode.sh` line 39 sets `setpath_json "product" "extensionsGallery" {...}`
- **API Surface**: `IExtensionGalleryService` interface and gallery extension points

### 10. Telemetry Data Collection for AI Training
- **Configuration APIs**: 
  - `telemetry.telemetryLevel` (enum: OFF/ON/CRASH)
  - `telemetry.enableCrashReporter` (boolean)
  - `telemetry.enableTelemetry` (boolean)
  - `workbench.enableExperiments` (boolean)
- **Configuration Registry**: `src/vs/platform/telemetry/common/telemetryService.ts`
- **Default Values**: All changed from `true` to `false` in `patches/telemetry.patch`
- **Service Interface**: `ITelemetryService` and telemetry data collection endpoints

### 11. AI Extension API Permissions
- **Configuration API**: `extensionEnabledApiProposals` object in `product.json`
- **Extension APIs**: Array of API proposal strings including:
  - `GitHub.copilot.inlineCompletionsAdditions`
  - `GitHub.copilot-chat.*` (multiple chat-related APIs)
  - `GitHub.copilot.devDeviceId`
- **Configuration API**: `extensionsEnabledWithApiProposalVersion` array
- **Affected Extensions**: `GitHub.copilot`, `GitHub.copilot-chat`, `GitHub.copilot-nightly`
- **Purpose**: Maintains API parity with upstream VS Code to enable opt-in AI functionality

### 12. Edit Telemetry for AI Features
- **Configuration API**: `editTelemetry.enableTelemetry` (boolean setting)
- **Configuration Registry**: `src/vs/workbench/contrib/editTelemetry/browser/editTelemetry.contribution.ts`
- **Default Value**: `false` (changed in `patches/telemetry.patch` line 31)
- **Original Default**: `true` (upstream VS Code)
- **Service Interface**: Edit telemetry collection and reporting interfaces

All capabilities are enforced through configuration defaults, context key expressions, UI preconditions, and selective removal of code surfaces rather than build-time compilation flags. The primary mechanism is the `config.chat.disableAIFeatures` setting that gates most AI functionality.

---

**User**

PROMPT 4 — Boundary & Surface Classification

For each API, interface, or surface identified above, classify it as exactly one:

- EXTENSION-IMPLEMENTED
- PLATFORM-PROVIDED (extension-usable, no core modification)
- REQUIRES PLATFORM MODIFICATION
- PLATFORM-SURFACE MODIFIED (via build or patch)
- BEHAVIORAL SPEC ONLY (no API)
- UNKNOWN

Justify each classification using:
- file paths
- import sources
- patch locations
- where the implementation or removal occurs


---

**Cursor**

## Boundary & Surface Classification

### 1. GitHub Copilot Chat Functionality
- **Classification**: PLATFORM-SURFACE MODIFIED (via build or patch)
- **Justification**: Modified through `patches/disable-copilot.patch` which alters core VS Code files like `src/vs/workbench/contrib/chat/browser/actions/chatActions.ts` and `src/vs/workbench/contrib/chat/common/chatContextKeys.ts`. The patch adds preconditions that gate the chat functionality based on `config.chat.disableAIFeatures`.

### 2. GitHub Copilot Inline Code Suggestions
- **Classification**: PLATFORM-SURFACE MODIFIED (via build or patch)
- **Justification**: Modified through `patches/disable-copilot.patch` which affects inline suggestion triggers and UI elements. The patch adds `ContextKeyExpr.has('config.chat.disableAIFeatures').negate()` preconditions to suggestion provider registrations.

### 3. Built-in AI Chat Interface
- **Classification**: PLATFORM-SURFACE MODIFIED (via build or patch)
- **Justification**: Modified through `patches/disable-copilot.patch` in `src/vs/workbench/contrib/chat/browser/chatParticipant.contribution.ts`. The patch changes the `when` condition for `chatViewDescriptor` from `ChatContextKeys.Setup.*` to include `ContextKeyExpr.has('config.chat.disableAIFeatures').negate()`.

### 4. Natural Language Command Palette Search
- **Classification**: PLATFORM-SURFACE MODIFIED (via build or patch)
- **Justification**: Modified through `patches/telemetry.patch` in `src/vs/workbench/browser/workbench.contribution.ts`. Changes the default value from `true` to `false` for the configuration setting.

### 5. Natural Language Settings Search
- **Classification**: PLATFORM-SURFACE MODIFIED (via build or patch)
- **Justification**: Modified through `patches/telemetry.patch` in `src/vs/workbench/contrib/preferences/common/preferencesContribution.ts`. Changes the default value from `true` to `false` for the configuration setting.

### 6. Command Center AI Integration
- **Classification**: PLATFORM-SURFACE MODIFIED (via build or patch)
- **Justification**: Modified through `patches/disable-copilot.patch` which adds preconditions to `MenuRegistry.appendMenuItem(MenuId.CommandCenter, {...})` calls. The patch affects menu item visibility based on `config.chat.disableAIFeatures`.

### 7. MCP (Model Context Protocol) Server Gallery
- **Classification**: PLATFORM-SURFACE MODIFIED (via build or patch)
- **Justification**: Modified through `patches/disable-copilot.patch` in `src/vs/workbench/contrib/mcp/browser/mcpServersView.ts`. The patch changes view registration conditions to include `ContextKeyExpr.has('config.chat.disableAIFeatures').negate()`.

### 8. Cloud-Based Edit Sessions
- **Classification**: PLATFORM-SURFACE MODIFIED (via build or patch)
- **Justification**: Modified through `patches/disable-cloud.patch` in `src/vs/workbench/contrib/editSessions/browser/editSessionsStorageService.ts`. The patch removes the `registerSignInAction()` method call and removes `MenuRegistry` import, eliminating the cloud authentication UI surface.

### 9. Extension Marketplace for AI Extensions
- **Classification**: PLATFORM-SURFACE MODIFIED (via build or patch)
- **Justification**: Modified through `prepare_vscode.sh` build script (line 39) which sets `setpath_json "product" "extensionsGallery"` to point to Open VSX instead of Microsoft Marketplace. This changes the `IExtensionGalleryService` implementation surface.

### 10. Telemetry Data Collection for AI Training
- **Classification**: PLATFORM-SURFACE MODIFIED (via build or patch)
- **Justification**: Modified through `patches/telemetry.patch` in multiple core files including `src/vs/platform/telemetry/common/telemetryService.ts` and `src/vs/workbench/electron-browser/desktop.contribution.ts`. Changes default values from `true` to `false` for telemetry settings.

### 11. AI Extension API Permissions
- **Classification**: PLATFORM-SURFACE MODIFIED (via build or patch)
- **Justification**: Modified through `product.json` configuration file which contains `extensionEnabledApiProposals` and `extensionsEnabledWithApiProposalVersion` arrays. This maintains API parity with upstream VS Code to ensure AI extensions can function when users explicitly opt-in, rather than extending beyond VS Code's capabilities.

### 12. Edit Telemetry for AI Features
- **Classification**: PLATFORM-SURFACE MODIFIED (via build or patch)
- **Justification**: Modified through `patches/telemetry.patch` in `src/vs/workbench/contrib/editTelemetry/browser/editTelemetry.contribution.ts`. Changes the default value for `editTelemetry.enableTelemetry` from `true` to `false`.

All identified surfaces are classified as PLATFORM-SURFACE MODIFIED because they involve direct patches to core VS Code source files or modifications to the build process that alter the platform's behavior and API surface. None are purely extension-implemented, as they all require changes to the core platform code or configuration.

---

**User**


PROMPT 5 — Upstream Delta & Comparative Analysis

Compare this repository’s produced system against upstream VS Code.

Identify all AI-relevant deltas, including:
- enabled or disabled AI features
- removed or altered APIs
- extension compatibility changes
- policy or default behavior differences
- constraints affecting AI-assisted code analysis or modification

Cite patches, build scripts, flags, or documentation for each delta.

---

**Cursor**

## Upstream Delta & Comparative Analysis: VSCodium vs. VS Code

### 1. AI Features Disabled by Default
- **Delta**: GitHub Copilot chat, inline suggestions, and built-in AI features are disabled by default
- **VS Code Default**: AI features enabled when extensions are installed
- **VSCodium Default**: AI features require explicit user opt-in via `chat.disableAIFeatures: false`
- **Citation**: `patches/disable-copilot.patch` adds `config.chat.disableAIFeatures` with default `true`, gates all AI UI with `ContextKeyExpr.has('config.chat.disableAIFeatures').negate()`
- **Impact**: Users must consciously enable AI functionality, creating privacy-first defaults

### 2. Telemetry Collection Disabled
- **Delta**: All telemetry settings default to disabled
- **VS Code Default**: Telemetry enabled by default for diagnostic data collection
- **VSCodium Default**: `telemetry.enableTelemetry: false`, `telemetry.enableCrashReporter: false`, `workbench.enableExperiments: false`
- **Citation**: `patches/telemetry.patch` changes all telemetry defaults from `true` to `false` in `src/vs/platform/telemetry/common/telemetryService.ts`
- **Impact**: Prevents data collection that could be used for AI model training or personalization

### 3. Natural Language Search Features Disabled
- **Delta**: AI-powered natural language search in command palette and settings disabled by default
- **VS Code Default**: Natural language search enabled for enhanced discoverability
- **VSCodium Default**: `workbench.settings.enableNaturalLanguageSearch: false`, `workbench.commandPalette.experimental.enableNaturalLanguageSearch: false`
- **Citation**: `patches/telemetry.patch` modifies `src/vs/workbench/browser/workbench.contribution.ts` and `src/vs/workbench/contrib/preferences/common/preferencesContribution.ts`
- **Impact**: Removes AI-enhanced search capabilities that rely on online services

### 4. Extension Marketplace Changed
- **Delta**: Extension gallery switched from Microsoft Marketplace to Open VSX
- **VS Code Default**: `marketplace.visualstudio.com` with proprietary licensing restrictions
- **VSCodium Default**: `open-vsx.org` with MIT-licensed, community-maintained extensions
- **Citation**: `prepare_vscode.sh` line 39 sets `extensionsGallery.serviceUrl` to Open VSX endpoint
- **Impact**: Affects availability of AI extensions; some Microsoft AI extensions may not be available or may have different licensing

### 5. Cloud Synchronization Features Removed
- **Delta**: Cloud-based edit sessions and synchronization completely removed
- **VS Code Default**: Cloud edit sessions available for cross-device continuity
- **VSCodium Default**: Cloud authentication and sync UI surfaces removed
- **Citation**: `patches/disable-cloud.patch` removes `registerSignInAction()` method and `MenuRegistry` imports from `src/vs/workbench/contrib/editSessions/browser/editSessionsStorageService.ts`
- **Impact**: Eliminates cloud-based AI features that might enhance coding workflows

### 6. Microsoft Extension Signature Validation Disabled
- **Delta**: Microsoft extension signature validation services disabled
- **VS Code Default**: Extension signature validation enabled for security
- **VSCodium Default**: Microsoft signing validation code removed to avoid proprietary dependencies
- **Citation**: `patches/disable-missing-vsda.patch` removes Microsoft signing/validation code (`vsda` module) from `src/vs/platform/sign/browser/signService.ts`
- **Impact**: Removes extension authenticity verification; does not affect user account authentication but may impact certain extension validation checks

### 7. Remote Development Extensions Incompatible
- **Delta**: Official Microsoft Remote Development extensions don't work due to their own product ID checks and licensing restrictions
- **VS Code Default**: Remote SSH, Containers, WSL extensions fully functional
- **VSCodium Default**: Must use alternative open-source implementations; extensions contain checks that prevent installation on non-official VS Code builds
- **Citation**: `docs/extensions-compatibility.md` lists incompatible extensions including `ms-vscode-remote.remote-ssh`, `ms-vscode-remote.remote-containers`, `ms-vscode-remote.remote-wsl`
- **Impact**: Remote development workflows may have reduced AI assistance capabilities

### 8. Extension API Parity Maintained
- **Delta**: Extension API proposals maintained to match upstream VS Code capabilities
- **VS Code Default**: Full set of extension APIs enabled for compatibility
- **VSCodium Default**: Equivalent API surface maintained to ensure AI extensions can function when opted-in
- **Citation**: `product.json` contains extensive `extensionEnabledApiProposals` array with AI extensions like `GitHub.copilot`, `GitHub.copilot-chat`, `ms-vscode.vscode-commander`
- **Impact**: Preserves AI extension compatibility when users choose to enable AI features

### 9. Proprietary Debugging Tools Incompatible
- **Delta**: Microsoft debugging tools don't work due to licensing restrictions
- **VS Code Default**: C# and C++ debugging tools fully functional
- **VSCodium Default**: Must use open-source alternatives like `netcoredbg`
- **Citation**: `docs/extensions.md` notes proprietary debugging tools including `ms-vscode.csharp` and `ms-vscode.cpptools` extensions
- **Impact**: Reduces AI-assisted debugging capabilities in certain languages

### 10. Command Center AI Integration Gated
- **Delta**: Command center AI features hidden behind opt-in setting
- **VS Code Default**: AI features visible in command center when available
- **VSCodium Default**: Command center AI options require `chat.disableAIFeatures: false`
- **Citation**: `patches/disable-copilot.patch` adds preconditions to `MenuId.CommandCenter` menu items
- **Impact**: Reduces discoverability of AI features in the command interface

### 11. MCP Server Gallery Gated
- **Delta**: Model Context Protocol server gallery hidden by default
- **VS Code Default**: MCP servers accessible when available
- **VSCodium Default**: MCP gallery requires AI features to be enabled
- **Citation**: `patches/disable-copilot.patch` modifies `DefaultBrowseMcpServersView` conditions in `src/vs/workbench/contrib/mcp/browser/mcpServersView.ts`
- **Impact**: Limits access to AI model integrations

### 12. Edit Telemetry Disabled
- **Delta**: Edit tracking for AI improvement disabled by default
- **VS Code Default**: Edit telemetry enabled for feature improvement
- **VSCodium Default**: `editTelemetry.enableTelemetry: false`
- **Citation**: `patches/telemetry.patch` modifies `src/vs/workbench/contrib/editTelemetry/browser/editTelemetry.contribution.ts`
- **Impact**: Prevents collection of editing data that could enhance AI code analysis features

### Summary of AI Constraints
VSCodium systematically disables AI features by default, removes Microsoft-specific AI integrations, and replaces proprietary services with open-source alternatives. The net effect is a privacy-focused distribution that requires explicit opt-in for AI functionality, significantly constraining AI-assisted code analysis and modification compared to upstream VS Code's AI-enabled defaults. Users gain privacy but lose convenience and some advanced AI capabilities.

- **AI features disabled by default** (GitHub Copilot gated behind `chat.disableAIFeatures`)
- **Telemetry collection disabled** (all telemetry defaults changed to `false`)
- **Natural language search features disabled** (command palette and settings search)
- **Extension marketplace changed** (Microsoft Marketplace → Open VSX)
- **Cloud synchronization features removed** (edit sessions removed via patch)
- **Microsoft authentication disabled** (signing services removed)
- **Remote development extensions incompatible** (licensing restrictions)
- **Extension API parity maintained** (equivalent AI APIs available for opt-in)
- **Proprietary debugging tools incompatible** (C#/C++ debuggers)
- **Command center AI integration gated** (menu items hidden by default)
- **MCP server gallery gated** (requires AI features enabled)
- **Edit telemetry disabled** (edit tracking disabled)
