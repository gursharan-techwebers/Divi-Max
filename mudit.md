# Development Prompt: WPMax v1.0.0 — Standalone WordPress SEO Audit & AI Agent Plugin

## 1. Product Overview

Build a production-quality WordPress plugin named **WPMax**.

WPMax is an independent WordPress SEO auditing system. It audits a WordPress website, identifies technical and on-page SEO problems, provides evidence for each finding, prioritizes fixes, and exposes its auditing capabilities to an external AI agent through a secure API and Model Context Protocol (MCP) endpoint.

The plugin must be fully standalone.

### Branding requirements

- Product name: WPMax
- Plugin slug: `wpmax`
- Text domain: `wpmax`
- PHP namespace: `WPMax`
- REST API namespace: `wpmax/v1`
- Author: WPMax
- Do not mention or include Tech Webers anywhere in the plugin, admin UI, source comments, readme, documentation, metadata, or branding.
- Do not depend on another company's proprietary API server.
- Do not require a separate Node.js, Next.js, Python, or SaaS backend for core functionality.
- Do not reuse another plugin's branding, credentials, or proprietary source code.
- Keep this project separate from the Divi Max Connector project. Existing MCP concepts may inform the architecture, but WPMax must not require the builder plugin to function.

## 2. Main Objective

WPMax must make it possible for a website owner or an authorized AI agent to:

1. Scan a WordPress website for SEO problems.
2. Audit individual pages, selected post types, or the complete eligible site.
3. identify site-wide issues and page-specific issues.
4. Prioritize findings by severity, impact, confidence, and effort.
5. Explain why each finding matters and how to address it.
6. Compare findings from previous audits.
7. Export reports for developers, marketers, and clients.
8. Access audit functionality through authenticated API/MCP tools.
9. Avoid modifying website content or settings without explicit authorization.

The primary deliverable is a reliable audit engine—not an arbitrary SEO score generator.

## 3. Product Principles

Follow these principles throughout development:

- Evidence before recommendations.
- Read-only auditing by default.
- Native WordPress APIs and core functions wherever appropriate.
- Compatibility with major SEO plugins.
- Bounded crawling and background processing for large websites.
- Transparent limitations and confidence levels.
- Minimal external API requirements.
- Secure, permission-aware AI agent access.
- Concise responses for AI agents, with detailed findings available on demand.
- No unsupported claims that an audit guarantees rankings, indexing, traffic, or rich results.

Do not present heuristic thresholds as official Google requirements. For example, title length, description length, word count, and the presence of exactly one H1 can be useful review signals, but should not automatically be described as Google ranking rules.

## 4. SEO Audit Categories

Implement the following audit modules. Use a shared finding format so that the dashboard, REST API, and MCP tools report results consistently.

### A. Technical SEO and Indexability

Check:

- Homepage accessibility.
- HTTPS availability and insecure canonical URLs.
- HTTP-to-HTTPS redirect behavior.
- Hostname consistency, including www versus non-www where applicable.
- Response status codes for audited URLs.
- Broken pages returning 4xx or 5xx responses.
- Redirect chains, loops, and unnecessary redirect hops.
- Missing, malformed, or unexpected canonical tags.
- Canonical destinations returning errors or redirecting.
- Pages using `noindex` directives.
- Relevant `X-Robots-Tag` response headers.
- Conflicts between canonical and indexing signals.
- WordPress search visibility setting that discourages search engines from indexing the site.
- Basic robots.txt availability and syntax checks.
- Important resources that appear blocked to crawlers.
- XML sitemap availability and parseability.
- Sitemap URLs that return errors, redirect unexpectedly, or appear to conflict with canonical or indexing signals.
- Internal links pointing to redirecting or non-existent destinations.
- Unexpected URL inconsistencies caused by trailing slashes, parameters, or duplicate paths.

Distinguish between a confirmed problem, a possible problem, and a configuration that requires human review. For example, a `noindex` directive on a private utility page may be intentional.

### B. Page Titles and Search Snippets

Inspect:

- Missing or empty HTML title elements.
- Duplicate titles across audited pages.
- Titles that are excessively repetitive.
- Titles that appear too short or long to communicate the page's purpose clearly.
- Missing meta descriptions.
- Duplicate or repetitive meta descriptions.
- Descriptions that are empty or appear truncated.
- Titles or descriptions that do not reasonably match the visible page topic.
- Multiple conflicting SEO metadata outputs.
- Relevant Open Graph and social sharing metadata as a separate presentation category.

Report exact observed values and the affected URLs. Length recommendations must be labeled as editorial guidance, not fixed ranking requirements.

### C. Headings and Page Structure

Inspect:

- Missing or empty H1 headings.
- Multiple H1 headings as a review signal, not an automatic error.
- Empty heading elements.
- Heading order that appears confusing or skips levels in a meaningful way.
- Pages whose title, H1, and main content appear inconsistent.
- Repeated page structures that may indicate template-level problems.

Do not assume every heading hierarchy irregularity affects rankings. Explain the user-experience or content-clarity concern behind each recommendation.

### D. Content Quality Signals

Identify potential issues such as:

- Empty pages or pages with very little meaningful visible content.
- Near-identical page titles and descriptions.
- Likely duplicate or highly repetitive page content.
- Similar pages targeting overlapping topics.
- Content that appears inconsistent with its page title or primary heading.
- Excessive repetition that may indicate keyword stuffing.
- Missing meaningful text on important service, product, or category pages.
- Placeholder text and obvious template remnants.
- Pages that appear to lack a clear purpose or useful information.
- Broken internal references and empty content sections where reliably detectable.

Use configurable heuristics for thin content, duplication, and content similarity.

Do not automatically label a page as poor quality simply because it has a low word count. Different page types have different content needs. Human review may be necessary to assess originality, expertise, usefulness, and intent satisfaction.

Do not transmit full private or unpublished content to an external AI agent unless the connected WordPress user has the required permissions and explicitly requests that level of detail.

### E. Internal Linking and Crawlability

Check:

- Internal links returning 4xx or 5xx responses.
- Internal links that create avoidable redirect chains.
- Empty anchor links.
- Broken image links.
- Links with missing or unclear anchor text as a review opportunity.
- Pages with very few incoming internal links.
- Potential orphan pages, based on the crawl and available WordPress content data.
- Pages that cannot be reached from discovered internal links.
- Important pages that appear buried in the internal link structure.
- Non-crawlable navigation links where reliably identifiable.

Do not label a page as definitively orphaned unless the audit has enough site coverage to support that conclusion. Record crawl coverage and discovery limitations.

### F. Image SEO

Check:

- Missing alt attributes on meaningful images.
- Empty alt attributes where the image appears informative rather than decorative.
- Alt text that looks repetitive or keyword-stuffed.
- Broken image URLs.
- Large image files that may contribute to slow loading.
- Missing image dimensions where detectable.
- Lazy-loading usage and potential above-the-fold image issues when evidence supports a concern.
- Images served over insecure HTTP on HTTPS pages.

An empty alt attribute is valid for decorative images. Do not blindly recommend adding alt text to every image or forcing keywords into alt text.

### G. Structured Data and Schema

Inspect:

- JSON-LD parsing failures.
- Malformed JSON-LD blocks.
- Basic structured-data properties and entity relationships.
- Missing important properties for the detected schema type when reliably checkable.
- Multiple overlapping schema outputs that may conflict.
- Schema types that appear inconsistent with the visible page.
- Structured data that references irrelevant or potentially misleading page information.
- Duplicate or conflicting structured data generated by multiple plugins or themes where detectable.

Support common schema types, including Organization, LocalBusiness, WebSite, WebPage, Article, Product, BreadcrumbList, and FAQPage, while recognizing that rich-result eligibility depends on Google's current feature-specific requirements and policies.

Clearly separate:
1. JSON syntax validation.
2. Basic schema structure checks.
3. Google rich-result eligibility checks, where a suitable validation service is available.
4. Human judgment about whether markup accurately represents visible content.

Never claim that schema is fully valid or eligible for a Google rich result based only on successful JSON parsing.

### H. Mobile and Performance Insights

Provide a basic local inspection and an optional integration with Google's PageSpeed Insights API.

When configured and available, collect relevant information for mobile and desktop, including:

- Core Web Vitals field data where available.
- Largest Contentful Paint (LCP).
- Interaction to Next Paint (INP).
- Cumulative Layout Shift (CLS).
- Lighthouse performance diagnostics.
- Major render-blocking resources.
- Oversized image and resource opportunities.
- Relevant Lighthouse SEO diagnostics.
- Relevant accessibility findings as a separate category.

Report the measurement mode, tested URL, device category, timestamp, and whether the metrics are field data or laboratory data.

Do not invent Core Web Vitals values when field data is unavailable. Do not run external performance tests on every URL automatically without respecting quotas, rate limits, and user-configured scan limits.

Core SEO auditing must still work when no PageSpeed API key is configured.

### I. HTML and Search Presentation

Inspect:

- Missing document title.
- Missing or malformed canonical link elements.
- Missing viewport metadata.
- Missing language declarations or suspiciously inconsistent values.
- Invalid or duplicate key metadata where detectable.
- Pages with unexpected robots metadata.
- Basic HTML response and document parsing problems.

Keep accessibility-related findings distinguishable from direct SEO findings. Do not present every accessibility concern as a Google ranking factor.

### J. WordPress-Specific SEO Checks

Inspect:

- WordPress site URL and home URL consistency.
- Search engine visibility settings.
- Public post types and selected custom post types.
- Published pages and posts missing SEO metadata.
- Public content included in a sitemap but returning errors or carrying contradictory indexing signals.
- Public pages unintentionally excluded from a sitemap, where the plugin can reliably determine intent.
- Conflicts with popular SEO plugins, including Yoast SEO, Rank Math, All in One SEO, and The SEO Framework.
- Metadata duplication caused by themes or plugins where detectable.
- Conflicting canonical tags, title tags, robots directives, or schema outputs.
- Archive, taxonomy, and pagination configurations that deserve review.

Do not override another SEO plugin's settings or generate duplicate metadata. Integrate with existing plugin data where possible and expose the detected source of each metadata value.

### K. Local SEO — Optional Module

Implement a separate, optional local SEO audit module that can inspect available business information and relevant structured data.

Potential checks:

- Missing or inconsistent business name and contact details where source data is available.
- LocalBusiness schema coverage.
- Address and telephone formatting inconsistencies across configured site content.
- Location pages missing useful location-specific information.
- Generic or duplicated location-page content.

Do not claim to verify Google Business Profile status, rankings, reviews, or external directory consistency unless a suitable authorized integration or data source is actually connected.

### L. External Search Data — Optional Integrations

Design an optional Google Search Console integration for authorized users.

Where supported by the APIs and permissions available at implementation time, retrieve and analyze:

- Search clicks and impressions.
- Click-through rate and average position.
- Query and page performance.
- Date-range comparisons.
- Pages receiving impressions but relatively few clicks.
- Declining search performance.
- Search queries that may reveal overlapping page topics.

Do not make claims about indexing status based on Search Console performance data alone. Use an appropriate authorized indexing-data source when a direct indexing check is needed.

The plugin must not require Search Console authorization to run its core SEO audit.

## 5. Finding Format and Prioritization

Every finding must use a consistent schema containing:

- `id`: stable finding identifier.
- `category`: technical, on-page, content, links, images, schema, performance, WordPress, or local SEO.
- `rule_id`: identifier for the specific audit rule.
- `severity`: critical, high, medium, low, or informational.
- `confidence`: high, medium, or low.
- `title`: brief problem statement.
- `summary`: concise explanation.
- `evidence`: observed facts supporting the finding.
- `affected_urls`: a bounded list of affected URLs.
- `affected_count`: number of affected URLs where known.
- `recommended_action`: a concrete next step.
- `why_it_matters`: the relevant SEO, crawlability, performance, or user-experience concern.
- `source`: where the observation came from.
- `first_detected_at` and `last_detected_at`.
- `status`: open, resolved, ignored, or needs_review.
- `rule_version`: audit rule version.

Severity must reflect the practical importance of a confirmed issue. Confidence must reflect how well the evidence supports the conclusion.

Prioritize issues such as important pages that are unexpectedly non-indexable, site-wide canonical conflicts, widespread server errors, broken sitemaps, or large groups of broken internal links. Do not classify every missing description or minor formatting issue as critical.

## 6. Scoring and Reporting

Provide:

- Overall audit health score.
- Category-level scores.
- Count of findings by severity.
- Highest-priority findings.
- Affected page count and crawl coverage.
- Progress and completion status for ongoing scans.
- Comparison with a previous completed audit.
- A list of resolved, new, recurring, and still-open findings where comparable data exists.

The score must be based on documented, configurable heuristics. Explain how it is calculated.

Do not describe the score as Google's score, an official search ranking score, or a prediction of ranking position. A site with a high audit score is not guaranteed to rank well.

Deduplicate repeated issues so the dashboard does not become overwhelmed by identical findings.

## 7. Crawling and Audit Engine

Create a reusable audit engine that can support both the admin dashboard and external agent tools.

Required behavior:

- Support a homepage audit.
- Support an individual URL audit.
- Support selected post types.
- Support a site-wide audit.
- Discover eligible public URLs through WordPress content, internal links, and the sitemap when appropriate.
- Configure maximum URLs, crawl depth, request timeouts, and concurrency.
- Use a queue or background processing system for large scans.
- Show scan progress and a clear completion or failure state.
- Record excluded URLs and explain why they were excluded.
- Respect configured crawl limits and responsible request rates.
- Offer retry handling for temporary network failures.
- Store results incrementally rather than requiring one long-running PHP request.
- Reuse recent findings when appropriate while keeping their timestamps and freshness clear.
- Allow users to cancel an active audit safely.

Do not crawl indefinitely. Make limits configurable and expose truncation clearly in reports.

Respect robots.txt when the plugin performs a public-site crawl intended to model crawler access. Separately label direct WordPress database or metadata inspection that does not represent what an unauthenticated search crawler can see.

Prevent crawling or fetching arbitrary internal network addresses. Validate hostnames and resolved IP addresses, reject disallowed local/private destinations, restrict cross-origin crawling, limit redirects, and protect against SSRF and DNS-rebinding risks. Do not allow a remote agent to use WPMax as a general-purpose HTTP proxy.

## 8. Admin Dashboard and User Experience

Create a clean, modern, responsive WordPress admin experience.

Recommended menu:

- WPMax Dashboard
- Start Audit
- Audit Reports
- Issues
- URL Inspector
- Integrations
- AI Agent / API Access
- Settings
- Logs and Diagnostics

Dashboard widgets should display:

- Latest audit status and date.
- Overall audit health score.
- Critical and high-priority findings.
- Findings grouped by category.
- Affected URLs.
- Audit coverage.
- Recent improvements and regressions.

Provide a detailed issue view with evidence, affected URLs, recommended actions, rule explanations, and source links.

Include filtering by severity, category, URL, status, and detection date.

Support CSV and JSON report exports. A printable report is desirable; PDF generation may be implemented in a later phase if it would introduce unnecessary dependencies.

Do not use misleading green/red indicators without descriptive text. Provide accessible labels, keyboard navigation, useful empty states, progress feedback, and understandable error messages.

## 9. AI Agent and MCP Integration

WPMax must expose its audit engine through a secure remote interface so a compatible external AI agent can request checks and explain results.

Provide:

- A documented MCP endpoint at `/wp-json/wpmax/v1/mcp`.
- A documented REST API under `/wp-json/wpmax/v1/`.
- Clear tool schemas and parameter validation.
- A tool catalog with short descriptions.
- Authentication and authorization on every protected operation.
- Bounded responses with pagination and result limits.
- Stable machine-readable finding IDs.
- Structured errors and clear retryability information.

Use the current MCP specification and a maintained, compatible implementation approach. Verify transport, session handling, initialization, notifications, and protocol-version requirements before claiming conformance. Do not implement a collection of ad hoc JSON endpoints and call it a compliant MCP server.

### Initial MCP tools

Implement these tools first:

1. `wpmax_get_site_info` — return basic site information, detected SEO plugins, enabled audit modules, and supported capabilities.
2. `wpmax_start_audit` — start an authorized audit with bounded URL, post-type, and module options.
3. `wpmax_get_audit_status` — return progress, coverage, timestamps, and completion status.
4. `wpmax_get_audit_summary` — return a concise summary of the latest or specified audit.
5. `wpmax_list_findings` — return filtered, paginated findings.
6. `wpmax_get_finding` — return evidence and recommendations for one finding.
7. `wpmax_audit_url` — inspect a specific eligible URL.
8. `wpmax_compare_audits` — compare two completed audits.
9. `wpmax_get_sitemap_status` — report discovered sitemap information and validation results.
10. `wpmax_get_robots_status` — report robots.txt observations and relevant indexing implications.
11. `wpmax_get_url_metadata` — return observed title, description, canonical, robots directives, headings, and relevant metadata.
12. `wpmax_get_performance_report` — return available performance results or explain why they are unavailable.
13. `wpmax_get_integration_status` — report whether optional Search Console and PageSpeed integrations are configured and usable.

Do not make all tools return the full audit report. Let the agent request a summary, a finding, or a bounded set of URLs when more detail is needed.

Keep tool descriptions concise. Provide minimal structured evidence by default, with optional detailed output.

### Agent response behavior

The plugin must report observations, not fabricate explanations or promise fixes.

For example, it should return:

- What was detected.
- Which URLs are affected.
- How the issue was confirmed.
- Why it may matter.
- A recommended action.
- Any uncertainty or limitation.

The connected AI agent may help interpret the findings and propose an implementation plan, but WPMax must preserve the underlying factual evidence.

## 10. API Key and Authentication Design

Implement an API-key management interface.

Requirements:

- Generate keys using a cryptographically secure random source.
- Use a suitably strong key, such as 32 random bytes encoded as 64 hexadecimal characters.
- Display the full key only once when generated.
- Store only the key's cryptographic hash, never the original key.
- Show a masked representation after creation.
- Allow revocation and regeneration.
- Never accept credentials in URL query parameters.
- Authenticate remote calls using an appropriate secure authorization header, such as `Authorization: Bearer ...`.
- Associate each key with a specific WordPress user and permission scope.
- Provide read-only audit access as the default scope.
- Support revocation without requiring plugin reinstallation.
- Do not expose private pages, unpublished content, user data, credentials, or settings beyond the associated user's capabilities.
- Enforce rate limits and bounded scan requests.
- Avoid recording full API keys, tokens, private content, or sensitive request bodies in logs.
- Use HTTPS for remote production access.

Do not make the API key a substitute for WordPress authorization. Check permissions again when executing each tool, especially when accessing private content or sensitive administrative data.

If the chosen MCP authorization design requires OAuth or another protocol-level flow for compatibility, document the implementation and its trade-offs. Do not claim standards compliance unless tested.

## 11. Read-Only Security Model

Version 1.0 must be an audit-and-recommendation product.

The remote agent must not be able to:

- Edit or publish pages or posts.
- Change SEO titles or descriptions.
- Modify canonical tags or robots directives.
- Edit theme files, PHP, CSS, or JavaScript.
- Change WordPress settings or install plugins.
- Delete content or backups.
- Execute arbitrary SQL, PHP, shell commands, or arbitrary filesystem operations.
- Request arbitrary external URLs through WPMax.
- Access unrelated private WordPress data.

Do not add a hidden write mode.

Future versions may support optional fix application, but any write capability must be explicitly scoped, require appropriate authorization and confirmation, save and verify a recoverable before-state, verify the saved result, and attempt rollback if the write fails.

## 12. Integrations

Core auditing must work without external API credentials.

Build optional, independently configurable integrations for:

- Google PageSpeed Insights API.
- Google Search Console APIs.

Requirements:

- Use official documentation and supported authorization methods.
- Make missing credentials and permission errors clear.
- Store sensitive credentials securely and never include them in agent responses.
- Avoid making a paid service mandatory.
- Cache results appropriately and respect API quotas.
- Store source, timestamp, URL, device, and measurement type with external results.
- Provide connection testing and disconnect functionality.
- Do not claim that an integration is connected until a real test succeeds.

Do not make assumptions about API availability, quotas, or future endpoint support. Check the current documentation during implementation.

## 13. WordPress Engineering Requirements

Follow the official WordPress Plugin Developer Handbook, WordPress coding standards, secure REST API practices, and applicable Plugin Directory requirements.

Use:

- WordPress core APIs where suitable.
- Capability checks.
- Input validation and sanitization.
- Output escaping.
- Prepared database queries.
- Nonces for relevant admin actions.
- Permission callbacks for REST routes.
- Safe HTTP request handling.
- Internationalization-ready user-facing strings.
- Proper activation, deactivation, and uninstall behavior.
- A documented database schema and versioned migrations if custom tables are necessary.
- Efficient queries and bounded memory usage.
- A clear separation between admin UI, auditing rules, crawl engine, data persistence, API, MCP transport, and integrations.

Avoid unnecessary dependencies and do not load large libraries on every admin screen.

Use standard WordPress extension points instead of duplicating existing functionality. Test compatibility with common hosting configurations, supported PHP versions, multisite where explicitly supported, caching plugins, and popular SEO plugins.

Choose and document the minimum supported WordPress and PHP versions based on the APIs and libraries actually used.

## 14. Audit Storage and Privacy

Persist audit history using an appropriate data model. Evaluate custom tables for large finding collections rather than placing large datasets into autoloaded options.

Store:

- Audit ID and status.
- Configuration and scope.
- Start and finish timestamps.
- Crawl coverage.
- Finding records.
- Rule versions.
- Integration metadata.
- Audit comparison references.
- Error and recovery information where relevant.

Provide retention settings and a documented cleanup process.

Never delete active audit data or the only relevant recovery information as a side effect of a failed operation. Do not collect private information unnecessarily. Explain what audit data is stored, what leaves the website through optional external integrations, and how users can erase stored data.

Use reasonable defaults for audit history and disk usage. Record failures without storing secrets.

## 15. Testing Requirements

Create automated tests for the audit rules, data model, API routes, security controls, and agent interface.

At minimum, test:

- Missing, duplicate, and unusually long titles.
- Missing and duplicate descriptions.
- Heading findings and exceptions.
- Canonical conflicts.
- `noindex` and robots directives.
- Robots.txt and sitemap parsing.
- Broken URLs and redirect chains.
- JSON-LD syntax errors.
- Missing and decorative-image alt attributes.
- Duplicate and similar content heuristics.
- Crawl limits and background-job failures.
- Cancelled audits and retry handling.
- Missing or invalid API keys.
- Key rotation and revocation.
- Capability and object-level access controls.
- Request validation and rate limits.
- SSRF and cross-origin protections.
- MCP protocol and tool schema compatibility.
- External APIs being unavailable or rate-limited.
- SEO plugin compatibility.
- Large-site memory and execution limits.
- Multisite behavior if supported.

Use fixtures and controlled mock responses for external services. Do not depend on live Google API availability for basic test success.

Include negative tests to prove that the remote agent cannot write content, read unauthorized private data, execute arbitrary code, or use WPMax to access internal network services.

Do not claim tests passed unless they were actually run. Document any untested environments.

## 16. Documentation and Deliverables

Deliver:

1. Complete WPMax plugin source.
2. Installable ZIP: `wpmax-v1.0.0.zip`.
3. README with installation and configuration instructions.
4. SEO audit rules reference explaining detection logic and limitations.
5. API endpoint documentation.
6. MCP setup guide for compatible AI agents.
7. API-key security and permissions documentation.
8. Optional integration setup guides.
9. Admin-user guide and sample audit report.
10. Automated tests and test instructions.
11. Compatibility and known-limitations document.
12. Changelog and upgrade/uninstall notes.

Document the MCP endpoint URL format, authentication requirements, tool names, arguments, response schemas, errors, limits, and supported capabilities.

## 17. Required Development Workflow

Work incrementally. Do not generate the entire plugin in one unverified step.

### Phase 1 — Architecture and audit-rule design

Inspect the current workspace before creating or replacing files.

Produce the plugin architecture, audit rule catalog, finding schema, threat model, and implementation plan. Verify current official WordPress, Google, PageSpeed, Search Console, and MCP documentation.

### Phase 2 — Plugin foundation

Implement plugin setup, activation/deactivation, admin navigation, settings, permission checks, data storage, logging, and the common finding model.

Verify that activation and deactivation work cleanly.

### Phase 3 — Core SEO audit engine

Implement technical SEO, indexability, metadata, headings, internal links, images, sitemap, robots.txt, and basic structured-data checks.

Build test fixtures and verify findings against known example pages.

### Phase 4 — Crawl scheduling and reporting

Implement bounded crawling, background processing, progress display, cancellation, audit history, issue filtering, export, and comparison.

Verify behavior with a large simulated site and partial network failures.

### Phase 5 — API and MCP access

Implement API-key creation, hashed storage, scoped permissions, read-only tools, MCP transport, response limits, rate limiting, and security tests.

Verify the interface using a compatible MCP client before claiming that it works with an external agent.

### Phase 6 — Optional integrations

Add PageSpeed Insights and Search Console integrations with independent configuration, connection testing, error handling, and quota-aware behavior.

Verify that the core audit still works without either integration.

### Phase 7 — Regression testing and packaging

Run the automated test suite, WordPress coding-standard checks, static analysis where practical, security tests, installation tests, and MCP protocol tests.

Verify the ZIP contents and install the package into a clean WordPress test site.

Do not overwrite a working implementation without making a verified backup first. Do not silently remove or disable existing functionality during later updates.

## 18. Definition of Done

WPMax v1.0.0 is complete only when:

- It installs and activates as a standalone WordPress plugin.
- It performs a real audit of an eligible WordPress site.
- Findings include evidence, affected URLs, and clear recommendations.
- The crawl is bounded and its coverage is reported honestly.
- The dashboard provides actionable summaries and detailed issue views.
- Reports can be exported.
- API access is authenticated, scoped, and read-only.
- A compatible client can invoke the documented MCP tools successfully.
- Optional external integrations fail safely when unconfigured or unavailable.
- SEO plugin conflicts are handled or reported clearly.
- Automated and security tests have been run and their results documented.
- No Tech Webers branding or external backend dependency exists.
- The installable ZIP and documentation are delivered.

Begin with Phase 1. Inspect the existing project and prepare the architecture and audit-rule catalog before implementing code. If the current project files are not available, create a new standalone WPMax project rather than assuming access to another plugin's source code.
