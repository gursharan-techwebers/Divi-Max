# Production-Grade Development Prompt: Divi Max Connector

## 1. Role and mission

Act as a senior WordPress plugin architect, PHP engineer, MCP protocol engineer, Divi 5 layout specialist, UI/UX designer, accessibility specialist, and application security engineer.

Build **Divi Max Connector**, a production-grade WordPress plugin published by Tech Webers. It must connect an AI agent, particularly Claude on the web, directly to a WordPress website through an authenticated MCP endpoint.

The plugin must provide tools for inspecting WordPress content, understanding the installed Divi environment, creating and validating Divi pages, and safely editing existing layouts.

There must be no separate Node.js server, Next.js application, Python service, hosted MCP backend, or external tool server required for normal operation. The WordPress plugin must host the MCP endpoint and execute WordPress and Divi operations directly within the WordPress environment.

The plugin must be secure, maintainable, compatible with documented WordPress development practices, efficient in its token usage, and capable of creating visually polished Divi 5 layouts.

**Two non-negotiable priorities:**

1. Follow official WordPress plugin development, security, coding, accessibility, privacy, and Plugin Directory guidelines wherever applicable.
2. Build pages with native Divi 5 modules, sections, rows, columns, design settings, responsive controls, global variables, and presets wherever possible. Use custom CSS, HTML, or JavaScript only when the desired result genuinely cannot be achieved reliably with Divi's built-in capabilities.

## 2. Product objective and user experience

A customer should be able to:

1. Install and activate Divi Max Connector on their WordPress site.
2. Open WordPress Admin → Divi Max Connector.
3. Generate an API key.
4. Copy the generated key once and store it safely.
5. Open Claude on the web and add a custom remote MCP connector.
6. Enter the website's HTTPS MCP endpoint and configure an `Authorization` request header.
7. Ask Claude to inspect pages, find drafts, understand available Divi modules, create a page, or modify a layout.
8. Have the agent use native Divi structures and settings as the default implementation strategy.
9. Preview and validate the result before publishing.
10. Revoke the API key immediately when access is no longer needed.

The customer should not have to install Node.js, configure environment variables, create database credentials, use a separate MCP server, or manually edit WordPress core files.

The WordPress site must provide its own MCP endpoint, such as:

`https://example.com/wp-json/divi-max/v1/mcp`

Claude on the web connects from Anthropic's cloud infrastructure. Document that a site available only at a local address, such as `mcp-test.local`, needs a suitable secure tunnel or other public HTTPS access method for testing.

## 3. Architecture and technology decisions

Before implementing the MCP protocol, inspect the current official WordPress MCP Adapter and Abilities API:

- https://github.com/WordPress/mcp-adapter
- https://developer.wordpress.org/
- https://modelcontextprotocol.io/specification/

Evaluate whether the official adapter is appropriate for transport handling, protocol negotiation, tool registration, and permission enforcement.

Prefer maintained WordPress infrastructure over a hand-written MCP protocol implementation when it meets the requirements.

Do not silently add a dependency on another WordPress plugin. If the official adapter is selected and requires separate installation, clearly document the dependency and setup process. If a standalone implementation is necessary, implement the current supported MCP protocol behavior correctly and test it against the intended client.

Use PHP, the WordPress REST API, documented WordPress APIs, and a modular, maintainable architecture.

Do not create a custom database service or external backend unless a clearly documented requirement cannot be met within WordPress.

## 4. WordPress development guidelines — mandatory

Follow the official WordPress Plugin Developer Handbook, WordPress Coding Standards, and applicable Plugin Directory Detailed Guidelines.

Official references:

- https://developer.wordpress.org/plugins/
- https://developer.wordpress.org/coding-standards/wordpress-coding-standards/
- https://developer.wordpress.org/plugins/wordpress-org/detailed-plugin-guidelines/
- https://developer.wordpress.org/plugins/plugin-basics/best-practices/

### Coding standards

- Follow WordPress PHP, JavaScript, CSS, HTML, documentation, and internationalization standards.
- Use clear naming, readable source code, and appropriate PHPDoc comments.
- Keep business logic separate from admin presentation logic.
- Prefer small, focused classes and methods over oversized classes.
- Use WordPress hooks and filters appropriately.
- Use core WordPress APIs rather than reinventing functionality already provided by WordPress.
- Never modify WordPress core files or the files of another plugin or theme.
- Escape output according to context and sanitize and validate incoming data.
- Check user capabilities and verify nonces for administrative state-changing requests.
- Use `$wpdb->prepare()` for custom SQL.
- Handle `WP_Error`, failed writes, unexpected exceptions, and optional PHP extensions safely.
- Enqueue scripts and styles correctly and load them only where needed.
- Use WordPress's bundled libraries where available rather than including duplicate versions unnecessarily.
- Keep source code human-readable and maintainable.
- Include explicit plugin versioning, upgrade handling, uninstall behavior, and documented compatibility requirements.

### Plugin Directory and licensing compliance

Design the plugin to comply with the WordPress.org Plugin Directory guidelines if it will be submitted there.

- Use a compatible license, preferably GPL-2.0-or-later, where applicable.
- Audit all bundled third-party source code, reference files, icons, assets, and libraries for compatible licensing.
- Include accurate attribution and license notices.
- Do not obfuscate the code.
- Do not bundle executable code that is downloaded from an uncontrolled source.
- Do not silently install or modify unrelated plugins or themes.
- Do not make undocumented external network calls.
- Do not collect or transmit telemetry or personal data without appropriate disclosure and consent.
- Do not include unwanted public-site credits or advertisements.
- Do not clutter the WordPress dashboard with intrusive notices.
- Do not add misleading claims, artificial upgrade nags, or unrelated promotional behavior.
- Provide a complete, stable, installable plugin package.
- Provide an accurate README describing requirements, features, external services, privacy implications, and setup.
- Preserve proper versioning and document release procedures.

If a requirement conflicts with a WordPress guideline, explain the conflict and propose a compliant alternative instead of silently implementing it.

Passing code checks does not automatically guarantee Plugin Directory approval. Keep the plugin suitable for review and document anything that still needs verification.

## 5. Plugin identity and compatibility

Plugin name: Divi Max Connector

Author: Tech Webers

Text domain: `divi-max-connector`

Namespace: `TechWebers\DiviMaxConnector`

REST namespace: `divi-max/v1`

Implement:

- A proper plugin entry point.
- Activation, deactivation, and uninstall handlers.
- Dependency and environment checks.
- A documented minimum WordPress version and PHP version based on actual API requirements.
- Divi detection and version reporting.
- Graceful behavior when Divi is inactive, missing, or unsupported.
- A schema version if persistent custom data needs migration.
- Internationalization-ready strings.
- Clear admin notices that are dismissible or disappear when the problem is resolved.
- A maintained and versioned source tree.
- Tested behavior with explicitly documented WordPress and Divi versions.

Do not claim that all Divi 5 versions are supported until they have been verified.

## 6. API key management

Create a clean, minimal settings page named **Divi Max Connector**.

Only appropriately authorized administrators, normally users with `manage_options`, may manage credentials and security settings.

### Key generation and storage

- Generate a cryptographically secure 32-byte random key using a secure system random generator.
- Encode the key in a 64-character hexadecimal representation.
- Support one active API key per WordPress installation for the initial release.
- Provide a **Generate API Key** action.
- Refuse to generate a new key while an existing key remains active unless the existing key has first been revoked.
- Display the full key only once, immediately following successful generation.
- Clearly explain that the key cannot be displayed again and must be saved securely.
- Store only a SHA-256 hash of the high-entropy key, its creation timestamp, and the final four characters.
- Never store the plaintext key in the database, a file, a transient, a URL, a log, an analytics event, or an exception message.
- Verify keys using a constant-time comparison such as `hash_equals()`.
- Display the existing key only in masked form.
- Do not create a separate Copy button; let the user manually copy the one-time displayed key.
- Provide a **Revoke API Key** action.
- Ensure that revocation immediately invalidates further requests using that key.
- Ensure key regeneration does not leave previous credentials usable.
- Never include the key in exported diagnostic reports.

Use nonces and capability checks for admin-side actions and prevent unauthorized or cross-site requests from generating or revoking keys.

### Authentication

For the initial implementation, support the authentication mechanism compatible with Claude's remote custom connector:

`Authorization: Bearer YOUR_API_KEY`

Validate credentials before tool discovery or execution.

Use appropriate HTTP status codes:

- `401 Unauthorized`: missing, invalid, or revoked credentials.
- `403 Forbidden`: authenticated request lacks permission.
- `400 Bad Request`: malformed input.
- `429 Too Many Requests`: rate limit exceeded.
- `500 Internal Server Error`: unexpected server failure, with details recorded privately.

Never reveal whether a particular key exists or why an unauthenticated credential failed.

### WordPress user identity and permissions

Bind the API key to an explicitly selected WordPress user. Do not automatically grant administrator privileges merely because a request contains a valid API key.

The selected user must exist and have the capabilities required by each operation. Do not silently fall back to another administrator if the configured user becomes invalid.

Check capabilities at execution time, including object-level permissions for individual pages and templates.

Examples:

- Reading pages requires the appropriate read capability and permission to read the requested object.
- Editing a page requires permission to edit that particular page.
- Creating a page requires the appropriate page-creation capability.
- Publishing requires the appropriate publication capability.
- Site-wide design changes require appropriately privileged access and explicit tool enablement.

A valid API key is authentication, not an unrestricted authorization bypass.

## 7. MCP endpoint and protocol security

The endpoint must implement the supported MCP protocol and transport correctly.

Requirements:

- Correct initialization and protocol version negotiation.
- Valid capability discovery and tool discovery.
- Valid tool calls and tool results.
- Correct JSON-RPC error responses and HTTP status codes.
- Required Streamable HTTP behavior for the supported implementation.
- Appropriate handling of protocol lifecycle and any sessions the selected transport requires.
- No claim of support for protocol versions or features that have not been implemented and tested.
- Validation of `Origin` according to the MCP specification.
- Rejection of explicitly untrusted origins, without reflecting arbitrary origins in CORS responses.
- Correct handling of Host and request targets.
- No wildcard CORS used as a workaround.
- `Cache-Control: no-store` for authenticated responses containing sensitive data.
- Bounded requests and responses.
- Appropriate rate limiting for excessive requests and failed authentication.
- Safe logging that redacts credentials, authorization headers, private page content, and other sensitive information.
- Graceful behavior when an external client disconnects or sends malformed requests.

Require HTTPS for production access.

Do not expose internal stack traces, database details, filesystem paths, secrets, or debug output to remote clients.

## 8. Admin interface and connection setup

The settings page should look and behave like a native WordPress admin screen. It must be clean, modern, minimal, accessible, and easy for non-technical users.

### Connection section

Display:

- MCP endpoint URL.
- Connection and authentication status where it can be determined reliably.
- Plugin, WordPress, and detected Divi versions.
- Key status and creation date.
- Masked key suffix.
- Configured WordPress acting user.
- Available tool groups.
- Last successful request timestamp when available.

Do not show private filesystem paths, server secrets, or unrelated system configuration.

### Setup guide for Claude

Explain:

1. Open Claude → Customize → Connectors → Add custom connector.
2. Enter the website's MCP endpoint.
3. Select **No sign-in** for the fixed API-key header configuration.
4. Select **Streamable HTTP** where supported.
5. Add a request header named `Authorization`.
6. Set its value to `Bearer ` followed by the generated API key.
7. Save the connector and test a read-only tool.

The plugin must not attempt to automate actions inside the customer's Claude account.

### Security section

Include:

- Generate API Key.
- Revoke API Key.
- WordPress acting-user selection.
- Read-only mode.
- Draft-write mode.
- Full-write mode.
- Optional allowed-origin configuration.
- Bounded rate-limit settings.
- Clear guidance about publicly reachable HTTPS sites.
- A warning that revoking the key disconnects clients using it.

### Tool groups

Allow administrators to enable or disable groups independently:

- WordPress Pages.
- Divi Layouts.
- Divi Design System.
- Divi Library, when supported.
- Rendering and Validation.

Only enabled tools should be listed, and disabled tools must also be rejected during execution.

## 9. Divi 5 design and layout quality — mandatory

The purpose of Divi Max is not merely to insert content into WordPress. It must help an AI agent create professional pages using the actual capabilities of the Divi Builder.

**Native Divi modules and layout settings must be the default implementation method. Custom code is an exception, not the default.**

Before implementing a page-generation or layout-editing operation, inspect the installed Divi version, existing page structure, available modules, existing design system, and supported layout storage format.

Official Divi developer and layout references:

- https://www.elegantthemes.com/developers/
- https://help.elegantthemes.com/en/articles/12982611-create-and-build-a-page-with-divi-5-from-scratch
- https://help.elegantthemes.com/en/articles/10316106-the-row-in-divi-5
- https://help.elegantthemes.com/en/articles/10454950-how-responsive-options-work-in-divi-5

### 9.1 Prefer native Divi components

Use, in order of preference:

1. Native Divi 5 sections and section settings.
2. Native rows and supported column layouts.
3. Native Divi modules, such as Text, Heading, Image, Button, Blurb, Icon, Divider, Accordion, Tabs, Gallery, Video, and other modules actually supported by the installed environment.
4. Native module content and design settings.
5. Native spacing, sizing, typography, background, border, shadow, and alignment controls.
6. Native responsive settings and device-specific layout options.
7. Native global colors, design variables, presets, and shared Components where available and appropriate.
8. Divi's supported advanced settings and custom CSS when native options genuinely cannot accomplish the requirement.
9. Custom HTML or JavaScript only when there is no suitable native module or supported setting for the requested behavior.

This hierarchy is mandatory.

Do not generate an entire page as raw HTML inside a single Code module when the same layout can be built with native Divi sections, rows, columns, and modules.

Do not create custom shortcodes or custom PHP rendering logic to imitate a feature already available through a native Divi module.

Do not use custom CSS to recreate settings that already exist in Divi's Design or Advanced tabs.

Do not introduce unnecessary custom modules when existing Divi modules can achieve the design.

### 9.2 Use Divi's actual layout capabilities

The implementation must understand and preserve the installed Divi version's supported structure.

Depending on the detected version and available APIs, use its real layout elements, supported module attributes, responsive configuration, global design settings, and preset references.

The tool registry should make the available module and layout capabilities discoverable to the AI. Where practical, expose or retrieve compact schemas or reference information for supported modules rather than forcing the agent to invent attributes.

Before creating a layout:

- Identify the correct native modules for the content.
- Establish the section, row, column, and module hierarchy.
- Set module content and relevant settings explicitly.
- Apply the intended spacing, typography, color, background, borders, and alignment through Divi settings.
- Use global variables and presets to maintain consistency when appropriate.
- Use the correct layout data format for the detected Divi version.
- Validate the resulting structure before saving.
- Never overwrite unknown module attributes, existing styles, or unsupported elements simply because they cannot be interpreted.

If a layout setting is unavailable in the installed version, report the limitation and choose the least invasive supported alternative.

### 9.3 Professional visual design requirements

Pages created by the agent should be **modern, minimal, polished, purposeful, and suitable for a professional business website**.

Use these design principles:

- A clear visual hierarchy.
- Strong, readable headings and concise supporting copy.
- Generous but consistent whitespace.
- Balanced section spacing.
- A coherent grid and alignment system.
- Restrained color palettes that suit the website brand.
- Consistent typography, font sizing, and line height.
- Clear calls to action.
- Good contrast and readable content widths.
- Appropriate image sizes and aspect ratios.
- Consistent button, card, form, and link treatments.
- Balanced use of background colors and section separation.
- Thoughtful negative space instead of unnecessary decorative elements.
- A clear flow from the hero section through supporting content to the final call to action.

Do not produce cramped layouts, excessive nested sections, oversized text without purpose, random combinations of colors, excessive gradients, unnecessary shadows, excessive pill-shaped elements, or decorative animations that compete with the content.

Avoid generic, repetitive, template-like sections when the specific site's purpose calls for a more tailored layout.

The design should feel coherent as a complete page, not merely a collection of individually styled modules.

### 9.4 Respect the existing website design system

For an existing website, first inspect:

- Global colors.
- Typography and heading styles.
- Design variables.
- Existing button and module presets.
- Page width and spacing conventions.
- Existing header and footer patterns.
- Common section backgrounds.
- Reusable Divi Library layouts and Components when available.

Use the existing design system unless the user specifically asks for a redesign.

Do not introduce a new color palette, typography system, or button style without a clear reason.

For a new page without a defined design system, choose a restrained palette and a small, consistent set of typography and spacing values, preferably represented through Divi's native global variables or presets when supported.

Do not modify site-wide global settings simply to style one page. Prefer local module settings for a page-specific variation and reusable global variables or presets when the styling should be shared across the site.

### 9.5 Responsive layout — required

Every created or substantially redesigned page must be considered for desktop, tablet, and mobile.

Use Divi's native responsive controls for:

- Heading and body font sizes.
- Column structures and stacking.
- Section and module spacing.
- Image sizing and alignment.
- Button width and placement.
- Responsive visibility only when appropriate.
- Flexbox or grid options exposed by Divi's supported layout settings.

Avoid fixed widths and hard-coded heights that break on smaller screens.

Do not create separate duplicated versions of entire sections merely to solve ordinary responsive layout problems when Divi's responsive settings can achieve the result.

Verify the content order, touch target sizes, readability, overflow, and button placement at each supported breakpoint.

Use a mobile-first mindset: the page should remain clear and usable on a narrow screen, not simply shrink the desktop layout.

### 9.6 Accessibility and usability

Respect semantic heading order and clear content structure.

Use descriptive link and button labels, appropriate alternative text for meaningful images, accessible contrast, visible focus states, and native controls when available.

Do not rely on color alone to convey information.

Avoid unnecessary motion, flashing effects, or interaction patterns that impede accessibility.

Aim to meet applicable WordPress accessibility guidance and WCAG AA expectations for the generated design. Do not claim legal or accessibility compliance based solely on automatic generation.

### 9.7 Custom code policy

Custom code should be used only after determining that native Divi functionality is inadequate for the requested result.

Before adding custom CSS, HTML, JavaScript, or a custom module, the agent must ask:

1. Can an existing native Divi module produce this result?
2. Can a built-in Divi layout or design setting produce it?
3. Can a responsive setting, preset, variable, or native interaction achieve it?
4. Can the layout be improved by using native rows, columns, and modules differently?
5. Is there a supported Divi API or setting that avoids custom code?

If the answer to any suitable native option is yes, use that option.

When custom code is truly required:

- Keep the code small, targeted, and specific to the requirement.
- Document why the native Divi option was insufficient.
- Avoid global selectors that unintentionally affect other site elements.
- Scope CSS to a unique, page-specific or component-specific selector.
- Avoid duplicating styles already controlled by Divi's settings.
- Avoid unnecessary third-party libraries.
- Do not generate PHP or execute arbitrary model-generated code.
- Do not inject untrusted HTML or JavaScript into the site.
- Validate and sanitize inputs and escape outputs.
- Preserve WordPress and Divi security boundaries.
- Include responsive behavior and accessibility considerations.
- Explain exactly where the code is stored and how it can be reversed.

For page layouts, prefer native Divi modules and settings over code. For connector functionality, use secure WordPress PHP APIs rather than embedding implementation logic into generated pages.

### 9.8 Design quality verification

After generating a page, validate both the technical structure and the intended visual hierarchy.

Use native Divi layout structures wherever possible. Run structural validation, inspect the rendered page when supported, and report issues such as unsupported modules, missing content, incorrect nesting, overflow, or responsive limitations.

A successful database write is not proof of a visually correct design.

Do not claim that a page has been visually verified unless the implementation actually inspected the rendered output or an equivalent reliable test result.

## 10. MCP tool design

Build a focused initial toolset rather than exposing dozens of tools unnecessarily.

Use a shared tool registry that validates arguments, checks permissions, applies enabled-group settings, bounds output sizes, and handles errors consistently.

### Tool: `site_info`

Return a concise summary of the site's WordPress and Divi environment, supported capabilities, and relevant version information.

Do not reveal secrets, database credentials, salts, private file paths, or unrelated server configuration.

### Tool: `list_pages`

List all eligible WordPress pages with filters for status, search term, parent ID, pagination, and page size.

By default, include eligible statuses such as publish, draft, pending, private, and future, subject to WordPress permissions. Exclude trashed pages unless explicitly requested and authorized.

Return concise page summaries with ID, title, status, slug, URL where available, modification time, and pagination information.

### Tool: `get_page`

Retrieve a page by ID or slug.

Default to a summary. Support explicit modes for full content or a compact Divi layout outline.

Enforce hard output limits. If content is truncated, return an explicit truncation flag and original content length.

Preserve raw stored content when full Divi markup is requested. Do not return enormous layouts unnecessarily.

### Tool: `get_divi_environment`

Detect the installed Divi version, available layout capabilities, supported module information, and relevant feature limitations.

Do not assume that Divi 4 and Divi 5 use identical structures.

### Tool: `get_divi_reference`

Retrieve relevant Divi documentation, module schemas, layout rules, and examples in small, focused sections.

Avoid automatically returning entire reference documents. Fetch only relevant material for the current task.

Use official Divi documentation and source material that corresponds to the detected version.

### Tool: `get_design_system`

Return global colors, variables, presets, typography conventions, and relevant design-system information compactly.

Allow focused retrieval rather than requiring the entire design system to be returned for every page request.

### Tool: `validate_divi_layout`

Validate the proposed native Divi layout structure, supported block formats, required attributes, parent/child relationships, payload size, and known schema constraints.

Report warnings for unsupported or uncertain elements.

Do not claim that markup validation alone guarantees correct rendering.

### Tool: `create_page`

Create a WordPress page using supported native Divi layout data where applicable.

Default to draft status.

Validate the layout, check permissions, bound content size, and return the page ID and compact metadata.

Do not automatically publish pages.

### Tool: `update_page`

Update only the requested fields or supported layout elements.

Check the acting user's permission to edit the specific page. Use revision-aware updates to prevent overwriting intervening changes.

Create recoverable revisions or backups as appropriate. Fail safely if a required backup cannot be made.

Preserve unrelated modules, metadata, styles, design tokens, and content.

When practical, prefer a narrow module or content update rather than replacing the complete page layout.

### Optional write tools

Additional operations such as publication or broader design-system changes must be separately controlled by administrator settings and the appropriate WordPress capabilities.

Do not provide arbitrary PHP execution, unrestricted SQL, unrestricted file editing, or permanent-delete tools in the initial release.

## 11. Native layout generation workflow

When Claude receives a request to create or redesign a Divi page, it should follow this workflow:

1. Inspect the site information and installed Divi version.
2. Check whether the target page already exists.
3. Inspect the existing page and relevant design system when applicable.
4. Retrieve only the Divi reference information needed for the requested design or module.
5. Plan a compact page structure using native Divi sections, rows, columns, and modules.
6. Select the appropriate native modules for the content and interaction.
7. Define the design hierarchy, colors, typography, spacing, and responsive behavior through Divi settings.
8. Reuse existing global variables and presets when appropriate.
9. Generate the supported native layout structure.
10. Validate the markup and attributes before writing.
11. Save the new page as a draft or apply the authorized, targeted update.
12. Verify the saved content and rendered output where supported.
13. Return a concise report containing the page ID, status, changes made, warnings, and verification outcome.

The agent must not automatically read the entire documentation bundle, retrieve the full page repeatedly, or dump complete rendered HTML unless a specific operation requires it.

For small changes, keep the workflow small. For complex landing pages, inspect more of the existing design system and perform appropriate validation.

## 12. Token-efficiency requirements

Minimize context consumption without reducing layout quality or correctness.

- Advertise only enabled tools.
- Use concise descriptions and schemas.
- Return compact metadata by default.
- Retrieve full content only when requested.
- Apply hard output limits.
- Use focused documentation retrieval.
- Avoid repeated retrieval of unchanged pages or entire design systems.
- Return concise validation and rendering summaries.
- Make truncation explicit.
- Avoid repeated tool calls when existing results are sufficient.
- Do not include large HTML or block markup in success summaries unless necessary.

Do not reduce token use by omitting information needed to generate a correct Divi layout. Use targeted retrieval rather than indiscriminate truncation.

## 13. Safe write modes

### Read-only mode — enabled by default

- Expose only read tools.
- Reject write attempts during execution even if the client calls the tool manually.

### Draft-write mode

- Permit creating draft pages.
- Permit explicitly authorized, narrow edits.
- Do not publish, trash, or delete pages.

### Full-write mode

- Permit only operations explicitly enabled by the administrator.
- Enforce WordPress capabilities, validation, revisions, backups, and audit logging on every write.

Do not rely solely on MCP tool visibility or tool annotations as a security control.

## 14. Data integrity, backups, and logging

Use WordPress APIs appropriately and preserve unrelated data.

- Handle `WP_Error` returns.
- Prevent stale writes and duplicate operations where feasible.
- Preserve Divi block comments, JSON attributes, global variables, presets, and unknown module data.
- Back up affected fields and metadata before destructive or broad changes.
- Fail closed if a required backup cannot be created.
- Roll back partial multi-step writes where practical.
- Log key creation/revocation, authentication failures, and write operations without logging secrets or full private content.
- Use correlation IDs for safe debugging.
- Keep logs bounded and define their retention.
- Keep uninstall behavior conservative and documented.

## 15. Production-grade engineering and testing

Use WordPress coding standards, static analysis, PHP linting, security reviews, and automated tests.

Test at minimum:

- API-key generation, masking, revocation, and invalid credentials.
- WordPress capability and object-level access checks.
- Read-only enforcement.
- Enabled and disabled tool groups.
- Published, draft, pending, private, scheduled, and trashed pages.
- Invalid IDs and malformed requests.
- Large content and response limits.
- Divi 5 native layout structures, nested modules, and unknown block types.
- Preservation of existing layout data during narrow edits.
- Backup failure and partial-write failure.
- Concurrent edits and stale timestamps.
- MCP protocol initialization and tool calls.
- Origin validation and request limits.
- Plugin activation, deactivation, upgrades, and uninstall.
- Divi missing or unsupported.
- PHP extension compatibility.
- Accessibility, responsive behavior, and frontend rendering for generated layouts.

Use staging sites and test the actual Divi versions declared as supported.

Do not claim visual, runtime, security, or compatibility tests passed unless they were actually executed.

## 16. Deliverables

Produce:

1. Complete plugin source tree.
2. Installable ZIP named `divi-max-connector.zip`.
3. README with installation, Claude connection, requirements, security, privacy, tool documentation, and troubleshooting.
4. Architecture documentation explaining the MCP implementation choice.
5. Tool reference containing inputs, outputs, permission requirements, and read/write classification.
6. API-key lifecycle and security documentation.
7. Divi module/layout support documentation.
8. A design and layout generation guide describing the native-module-first policy.
9. Automated tests and recorded results.
10. Compatibility matrix listing the tested PHP, WordPress, and Divi versions.
11. A staging-to-production release checklist.

The plugin must be installable through WordPress Admin → Plugins → Add New → Upload Plugin, without deploying a separate application.

## 17. Required working process

Work in stages. Do not dump a large, untested implementation or replace files without inspecting the existing project.

**Stage 1 — Research and architecture**
- Inspect the official WordPress MCP Adapter and Abilities API.
- Review the current WordPress Plugin Handbook, coding standards, security guidance, privacy requirements, and applicable Plugin Directory rules.
- Review relevant official Divi documentation for native modules, layouts, presets, global variables, and responsive controls.
- Document the endpoint, authentication flow, permission model, tool registry, and layout-generation strategy.

**Stage 2 — Secure connection**
- Implement the plugin bootstrap, settings page, API-key management, acting-user configuration, and authenticated MCP transport.
- Test protocol initialization and authentication before adding write tools.

**Stage 3 — Read tools**
- Implement site information, page listing, individual-page inspection, Divi environment discovery, and focused reference retrieval.
- Test status filters and object-level permissions.

**Stage 4 — Divi layout tools**
- Implement design-system inspection and native-layout validation.
- Ensure layout generation uses the actual supported Divi modules and settings rather than raw HTML as the default.
- Test responsive settings and preservation of existing layout structures.

**Stage 5 — Safe writes**
- Implement draft creation and revision-aware, targeted updates.
- Add backups and rollback before expanding write capabilities.
- Keep publishing and broader site-wide changes disabled unless explicitly enabled.

**Stage 6 — Verification**
- Run automated tests and static checks.
- Test the MCP connection with the intended client.
- Install the ZIP on a staging WordPress site with Divi 5.
- Verify that the generated draft uses native Divi layout structures and renders correctly.
- Check desktop, tablet, and mobile behavior.
- Report failures, unsupported cases, and unverified features honestly.

At each stage, explain which files changed, what was tested, what passed, what failed, and what remains incomplete.

## Definition of done

The deliverable is complete only when:

- A customer can install the plugin and connect a compatible remote MCP client directly to the WordPress HTTPS endpoint.
- The API key is securely generated, verified, masked, and revocable.
- Every tool is authorized using the configured WordPress identity and relevant capabilities.
- Read-only mode is enabled by default.
- Pages of authorized statuses can be discovered and inspected.
- Draft pages can be created and verified.
- Existing pages can be modified without unnecessary layout replacement.
- The MCP protocol behavior has been tested against the intended client.
- The plugin follows applicable WordPress coding, security, privacy, licensing, accessibility, and Plugin Directory requirements.
- Divi page generation uses native Divi modules and supported layout settings as the default.
- Custom code is added only when native Divi capabilities are insufficient.
- Generated layouts have a modern, minimal, consistent visual hierarchy and are responsive.
- The output is bounded and optimized for low token consumption.
- The installable ZIP, source code, documentation, tests, and accurate verification report are delivered.

Do not mark the plugin production-ready solely because it builds or passes PHP syntax checks. All remaining compatibility, protocol, security, and visual-rendering limitations must be documented clearly.
