# Sonic-IDE

Project status and documentation conventions

Status labels used in this README: - **Implemented** means verified in
the current source code and deployment. - **In progress** means work has
started but the feature is not yet complete. - **Planned** means a
design goal that still requires implementation and testing. - **Needs
verification** means the repository must be inspected before a claim can
be made. Do not advertise a feature as production-ready until its tests,
security controls, and deployment behavior have been verified.

Vision

Sonic-IDE aims to provide a focused, approachable coding workspace that
can grow with Sonic AGI OS. The project prioritizes a clear interface,
reliable project files, secure authentication, and predictable
development workflows. The initial language focus is HTML, CSS,
JavaScript, and Python. The long-term goal is to connect editing,
previewing, source control, and isolated execution in a cohesive
experience.

Core principles

-   Preserve existing user work and avoid destructive setup commands.
-   Keep frontend presentation separate from backend business logic.
-   Keep secrets on the server and out of browser bundles.
-   Treat all submitted code and project files as untrusted input.
-   Make errors visible and actionable without leaking sensitive
    details.
-   Prefer small, testable modules over one oversized application file.
-   Document the difference between local development and production
    deployment.

Current repository layout

``` text
sonic-ide/
├── backend/
│   ├── auth/
│   ├── models/
│   ├── routes/
│   ├── services/
│   ├── app.py
│   └── config.py
├── docs/
├── execution/
│   ├── Dockerfile
│   ├── requirements.txt
│   └── runner.py
├── frontend/
│   ├── assets/
│   ├── css/
│   ├── js/
│   ├── index.html
│   ├── login.html
│   └── settings.html
├── tests/
├── .env.example
├── .gitignore
├── LICENSE
├── README.md
├── requirements.txt
└── vercel.json
```

This is the structure visible in the project screenshot and may change
as implementation evolves. Confirm filenames with `git ls-files` before
relying on this tree as an exact inventory.

Technology direction

-   **Frontend:** HTML, CSS, and JavaScript.
-   **Editor:** CodeMirror 5 is a candidate already discussed for the
    editor experience; verify its integration before claiming it is
    active.
-   **Backend:** Python and Flask.
-   **Execution:** a separately isolated service for running untrusted
    Python code.
-   **Source control:** Git and GitHub integration.
-   **Authentication:** GitHub OAuth with server-side session handling.
-   **Deployment:** local-first development, followed by a deployment
    architecture compatible with the chosen hosting providers.
-   **Testing:** automated unit, integration, and security-oriented
    tests.

Feature status

  -----------------------------------------------------------------------
  Capability              Initial status          Completion evidence
  ----------------------- ----------------------- -----------------------
  Repository structure    Present in workspace    Verify with `tree /F`
                          screenshot              and `git ls-files`

  HTML/CSS/JS editor      Planned / needs         Editor loads and edits
                          verification            each language

  Live HTML preview       Planned                 Preview works with safe
                                                  isolation

  Python execution        Planned / needs         Tests run in a
                          verification            restricted execution
                                                  service

  GitHub OAuth            Planned / needs         OAuth callback and
                          verification            session tests pass

  File explorer           Planned / needs         File operations pass
                          verification            permission tests

  Persistent project      Planned                 Data survives restart
  storage                                         and redeployment

  Production deployment   Needs verification      Deployment checklist
                                                  passes
  -----------------------------------------------------------------------

Requirements

Install Git, Python, and a supported code editor such as Visual Studio
Code. Use a currently supported Python version compatible with the
pinned project dependencies. Use a recent Git version and configure your
commit identity. Install Docker only if you are developing or testing
the isolated execution service locally. Use a modern browser with
JavaScript enabled for the frontend. Some features require internet
access, including GitHub OAuth and remote repository operations.

Quick start: inspect the repository

Open a terminal in the existing `sonic-ide` repository. Check the
current branch and working tree before editing files:

``` bash
git status
git branch --show-current
git remote -v
git ls-files
```

Do not run `git reset --hard`, `git clean -fd`, or other destructive
commands as part of routine setup. If you have uncommitted changes,
review and back them up before switching branches or applying broad
changes.

Quick start: create a Python environment

From the repository root, create a virtual environment:

``` bash
python -m venv .venv
```

Activate it in Windows PowerShell:

``` powershell
.\.venv\Scripts\Activate.ps1
```

Activate it in Windows Command Prompt:

``` bat
.venv\Scripts\activate.bat
```

Activate it on macOS or Linux:

``` bash
source .venv/bin/activate
```

If PowerShell blocks activation, use an approved local policy or
activate through Command Prompt; do not weaken machine-wide security
settings unnecessarily.

Quick start: install dependencies

Inspect the root dependency file before installing packages:

``` bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

If backend dependencies are maintained separately, install the
backend-specific file only after confirming its purpose. Keep dependency
versions pinned or constrained for repeatable builds. Do not install
packages from unknown sources or commit local virtual-environment
directories.

Quick start: configuration

Copy `.env.example` to a local `.env` file if the application
configuration expects it. Never commit `.env` files containing
credentials. Use placeholder values in `.env.example`, not working
client secrets or access tokens. Review `backend/config.py` to identify
the actual environment variable names before setting them. Fail safely
when required production secrets are missing. Keep debug mode disabled
in production.

Quick start: run the backend

Inspect `backend/app.py` to identify the Flask application object and
supported startup method. Once the entry point is confirmed, use the
project's documented Flask command or development server command.
Example only, if the application exposes `app` from `backend.app`:

``` bash
python -m flask --app backend.app run --debug
```

Do not assume this example works until imports, package layout, and
configuration are checked. The Flask development server is for local
development only, not production hosting.

Quick start: open the frontend

Inspect the frontend files and how API URLs are configured. If the
frontend is static, serve it with a local development server rather than
relying on browser-specific `file://` behavior. Ensure the frontend's
API base URL points to the local backend during development. Do not
embed OAuth client secrets or server-only credentials in JavaScript. Use
browser developer tools to inspect network failures, JavaScript errors,
and failed asset loads.

Windows development notes

Run commands from the repository root unless a step says otherwise. Use
PowerShell commands when the instructions explicitly show PowerShell
syntax. Use `dir` or `tree /F` to inspect files. Use
`Get-Content path\to\file` to inspect a text file. Use
`Test-Path path\to\file` to check whether a file exists. Use `git diff`
before committing changes. Do not mix PowerShell syntax with Bash syntax
without adapting the command.

Git and GitHub workflow

Check repository identity before pushing:

``` bash
git remote -v
git status
```

Authenticate GitHub CLI if required:

``` bash
gh auth status
gh auth login --web
```

Create a focused feature branch for a discrete change. Review staged
changes with `git diff --cached` before committing. Use descriptive
commit messages that explain the change. Never commit credentials,
private user data, generated secrets, or local database files.

Branching and commits

Use short-lived branches such as `feat/editor-tabs`,
`fix/oauth-callback`, or `docs/setup-guide`. Keep commits small enough
to review and revert. Run relevant tests before creating a commit. Check
that the branch is based on the intended upstream branch. Resolve merge
conflicts deliberately; do not blindly accept all incoming or current
changes. Push only to the intended remote and branch. Use pull requests
for changes that benefit from review.

Frontend architecture

The frontend should separate structure, styling, and behavior. HTML
files define semantic page structure. CSS files own layout, typography,
colors, responsive behavior, and component states. JavaScript modules
own editor behavior, API calls, file operations, and UI state. Keep
repeated UI behavior in reusable functions rather than duplicating it
across pages. Show loading, empty, success, and error states for
asynchronous actions. Use semantic HTML and accessible labels for
controls.

Editor experience

The editor should provide a clear active-file indicator. Unsaved changes
should be visible before a file is closed or navigated away from.
Language selection should match the active file type where possible.
Support indentation, line numbers, syntax highlighting, and keyboard
navigation. Keep the editor responsive when the file tree or preview
panel is resized. Provide understandable messages for unsupported file
types. Do not silently discard edits after a failed save.

Language support: HTML

HTML files should support syntax highlighting and standard text editing.
An HTML preview should be isolated from the application interface. Treat
user HTML as untrusted; it can contain scripts, forms, and remote
resource requests. Do not inject arbitrary user HTML into a privileged
application DOM. Prefer an appropriately sandboxed iframe for previews.
Define how local assets are resolved in a project preview. Test
malformed HTML and large files gracefully.

Language support: CSS

CSS files should support syntax highlighting and normal text editing.
Preview changes should be applied only to the relevant project preview.
Do not allow preview CSS to restyle the privileged IDE interface. Define
behavior for missing selectors, invalid CSS, and linked stylesheets.
Preserve file paths and relative imports in previews. Test responsive
CSS using narrow and wide viewport sizes.

Language support: JavaScript

JavaScript files should support syntax highlighting and standard
editing. Preview execution must be isolated from privileged application
state. Do not run arbitrary project scripts with the IDE's
authentication tokens or privileged APIs exposed. Provide a clear way to
inspect console output and runtime errors. Set appropriate timeouts and
resource limits for preview execution where feasible. Do not treat
client-side execution isolation as a substitute for server-side
authorization.

Language support: Python

Python editing can be available in the browser without executing code on
the server. Running Python requires a separate execution design and
explicit security controls. Never pass user-provided source to a shell
command assembled by string concatenation. Use fixed executable paths
and structured process arguments where processes are used. Apply
timeouts, memory limits, CPU limits, filesystem restrictions, and
network restrictions. Use a disposable container or equivalent sandbox
with a non-root user. Do not mount application secrets or the host
filesystem into the execution environment.

File explorer requirements

Display a clear hierarchy of project directories and files. Allow file
creation only within the authorized project root. Validate file names
and reject traversal paths. Check user permissions on every read, write,
rename, and delete request. Use explicit confirmation for destructive
operations. Handle conflicts when a file has changed since it was
opened. Keep the file tree synchronized after successful server
responses. Report failed operations without pretending that changes were
saved.

Project management

Each project should have a stable identifier. Each project should have
an owner or explicit access-control policy. Project metadata should be
stored separately from file contents where appropriate. Validate project
names and avoid using user-provided names directly as filesystem paths.
Define archive, export, import, and deletion behavior before
implementing them. Consider quotas for project count, file count, total
storage, and file size. Ensure deleting a project cannot delete another
user's files.

Backend architecture

Keep Flask route handlers small and focused. Move business logic into
service modules. Validate request data before calling services. Use
consistent JSON response shapes for API endpoints. Use suitable HTTP
status codes for client errors and server errors. Keep database access
and persistence concerns behind clear interfaces. Centralize
configuration without making secrets globally accessible. Log
operational events without logging passwords, tokens, or full private
source files.

API design

Prefix API endpoints with a versioned namespace when the API stabilizes.
Use JSON request bodies for structured data. Validate types, required
fields, lengths, and allowed values. Return a useful error code and safe
human-readable message. Do not expose stack traces in production
responses. Add pagination to potentially large listings. Use request
size limits and rate limits where appropriate. Document authentication
requirements for every protected endpoint.

Authentication overview

GitHub OAuth should be implemented on the backend. Use the OAuth
authorization-code flow. Validate the OAuth `state` value to protect
against request forgery. Use a fixed, registered callback URL in each
environment. Keep the OAuth client secret on the server. Store only the
minimum user and token data needed for the product. Use secure session
cookies in production. Provide a clear logout flow that invalidates the
application session.

GitHub OAuth setup

Create an OAuth App in GitHub Developer Settings. Set the homepage URL
to the correct development or production origin. Set the authorization
callback URL to the actual backend callback route. Register separate
OAuth applications for development and production when practical. Set
client ID and client secret through environment variables. Do not commit
a real client secret to `.env.example` or source control. Confirm the
callback route in the code before registering it in GitHub. Test
success, denial, invalid state, expired state, and callback failure
cases.

Session security

Set session cookies to `HttpOnly`. Set `Secure` on cookies served over
HTTPS in production. Use an appropriate `SameSite` policy for the OAuth
flow. Rotate session identifiers after authentication. Set session
expiration and logout behavior deliberately. Protect state-changing
requests against cross-site request forgery where relevant. Do not store
long-lived access tokens in browser local storage when avoidable. Do not
trust a username or user ID supplied by the client as proof of identity.

Authorization and permissions

Authentication answers who the user is; authorization answers what the
user may do. Check authorization on every protected backend operation.
Do not rely on hidden buttons or frontend route guards for security.
Scope repository operations to the authenticated user's authorized
repositories. Prevent one user from reading or changing another user's
project by guessing an ID. Use explicit permission checks for
collaborator features. Test unauthenticated and unauthorized requests
separately.

GitHub repository integration

Request the narrowest OAuth scopes needed for each feature. Explain why
each requested permission is needed. Handle revoked permissions and
expired tokens gracefully. Use GitHub APIs through the backend when
secrets or user tokens are required. Respect rate limits and handle
retry responses. Never log access tokens. Validate repository owner and
name before performing operations. Show users which repository is being
imported, updated, or pushed.

Storage design

Choose storage based on the actual deployment model. Do not assume a
serverless function's local filesystem is persistent. Use a database for
structured records and a suitable persistent store for project files.
Define backup and restoration procedures before production launch. Use
unique ownership keys and authorization checks for every stored project.
Limit upload sizes and validate file names. Set storage quotas and
monitor usage. Document retention and deletion behavior.

Database considerations

Use migrations for schema changes. Keep database credentials in
environment configuration. Use parameterized queries or a safe ORM. Do
not concatenate untrusted values into SQL statements. Create indexes for
common lookup and ownership queries. Test migrations against a
disposable development database. Back up important data before
destructive schema changes. Document supported database engines and
versions.

Execution service architecture

Keep untrusted code execution separate from the main web application.
Use a queue or controlled API between the Flask backend and execution
workers when needed. Authenticate internal service-to-service requests.
Apply strict execution time limits. Apply memory, CPU, process-count,
disk, and output-size limits. Disable outbound network access by default
unless a reviewed feature requires it. Use temporary workspaces and
destroy them after execution. Return structured output with a clear
execution status.

Container security

Run execution containers as a non-root user. Use a minimal, maintained
base image. Do not mount the Docker socket into an untrusted workload.
Do not pass host secrets into the container. Drop unnecessary Linux
capabilities. Use read-only filesystem settings where feasible. Set CPU,
memory, process, and file-size limits. Keep the execution image patched
and rebuild it regularly. Do not claim a container alone makes arbitrary
code completely safe.

Execution output

Distinguish standard output, standard error, exit status, and timeout.
Truncate oversized output. Escape output before rendering it in the
browser. Do not interpret program output as trusted HTML. Preserve
useful line breaks in console output. Report execution failures without
exposing host filesystem paths or secrets. Do not return environment
variables to the submitted program unless explicitly allowed.

Preview security

Use a separate origin for high-risk previews where feasible. Apply an
iframe sandbox appropriate to the preview feature. Avoid combining
script permission and same-origin permission casually. Do not pass
application credentials into the preview context. Set a Content Security
Policy suited to the product architecture. Restrict preview access to
only the files and resources required. Document whether remote scripts
and network requests are allowed. Test preview isolation with hostile
sample HTML and JavaScript.

Input validation

Treat all browser input as untrusted. Validate file paths, project IDs,
repository names, language names, and content sizes. Reject unexpected
fields when strict schemas are practical. Normalize inputs consistently.
Do not rely only on client-side validation. Use allowlists for supported
operations and file types. Handle Unicode file names and unusual path
separators carefully. Add tests for empty, oversized, malformed, and
adversarial input.

Error handling

Use a consistent error response schema. Return client errors for invalid
input and permission failures. Return generic server errors to users
while logging diagnostic details safely. Include a request identifier
when it helps correlate reports. Never reveal secret configuration in an
exception message. Make retryable errors distinguishable from permanent
errors. Ensure failed saves and executions remain visibly failed.

Logging and observability

Use structured logs where practical. Record request identifiers,
operation names, durations, and outcome codes. Redact OAuth codes,
tokens, passwords, cookies, and secrets. Avoid recording full source
files by default. Set log retention according to operational and privacy
requirements. Monitor error rates, execution timeouts, queue depth, and
storage failures. Provide health checks that do not reveal sensitive
environment details.

Accessibility

Use semantic headings and landmarks. Associate labels with form fields.
Ensure keyboard navigation for the editor shell and dialogs. Show
visible focus indicators. Maintain readable contrast in light and dark
themes. Do not communicate status using color alone. Provide accessible
names for icon-only buttons. Respect reduced-motion preferences. Test
the interface at browser zoom levels above 100 percent.

Responsive design

Support narrow laptop windows and common desktop resolutions. Allow
panels to resize without hiding essential controls. Prevent horizontal
overflow in normal application views. Make menus and dialogs usable on
touch screens where supported. Keep editor controls discoverable at
smaller widths. Test the layout with long filenames and large project
trees. Ensure console output and preview panels remain readable.

Design system

Define reusable spacing, color, typography, and radius tokens. Use
consistent button sizes and interaction states. Differentiate primary,
secondary, destructive, and disabled actions. Keep navigation consistent
across the editor and settings pages. Use restrained animation to
communicate state changes. Ensure error and warning states are visually
distinct. Keep the interface focused on code and project navigation.

Performance

Lazy-load features that are not needed at startup. Avoid repeatedly
rebuilding large file trees unnecessarily. Debounce expensive search and
autosave operations. Use pagination or virtualization for very large
file lists. Set reasonable maximum file sizes. Measure startup time and
editor responsiveness. Cache only data that can safely become stale. Do
not sacrifice authorization checks for performance.

Testing strategy

Write unit tests for validation and service logic. Write API tests for
success and failure responses. Test authentication and authorization
boundaries. Test file operations with traversal and permission edge
cases. Test execution timeout, output truncation, and isolation
behavior. Add frontend tests for core editor interactions where
practical. Run tests in CI before merging changes. Use disposable test
data and avoid real user credentials in tests.

Suggested test categories

-   Unit tests: pure functions and small service methods.
-   API tests: request validation, status codes, and response bodies.
-   Integration tests: database and storage interactions.
-   Authentication tests: OAuth state, session renewal, and logout.
-   Authorization tests: cross-user access attempts.
-   Security tests: path traversal, injection, and unsafe preview
    behavior.
-   Execution tests: timeout, memory limits, output limits, and cleanup.
-   Smoke tests: application starts and key routes respond.

Quality checks

Run Python syntax checks before launching the backend. Run the
configured test suite before committing. Check JavaScript console errors
during manual smoke tests. Check network requests for incorrect API
URLs. Review `git diff` to ensure unrelated files were not changed.
Check that no credentials are staged. Confirm that documentation matches
the actual behavior.

Local development checklist

-   [ ] Confirm the repository root and active branch.
-   [ ] Review `git status` before making changes.
-   [ ] Create or activate the Python virtual environment.
-   [ ] Install dependencies from the project requirements file.
-   [ ] Configure local environment variables.
-   [ ] Start the backend using the verified entry point.
-   [ ] Serve the frontend using the project's chosen local method.
-   [ ] Verify frontend-to-backend communication.
-   [ ] Run the relevant automated tests.
-   [ ] Review the final diff before committing.

Deployment strategy

Deploy the frontend and API only after local behavior is stable. Confirm
that the chosen host supports the application's runtime and routing
model. Treat serverless compute as ephemeral unless persistence is
explicitly provided. Host code execution separately from the frontend
and web API. Use a persistent database and storage service where
required. Configure environment variables in the hosting provider's
secure settings. Use HTTPS for production authentication and application
traffic. Test a production-like deployment before inviting users.

Vercel considerations

The repository contains a `vercel.json` file, but its correctness must
be checked against the actual application. Verify whether the configured
Python runtime and route mappings are supported by the current Vercel
platform. Do not assume Vercel serverless functions can host a
persistent worker or run arbitrary Docker workloads. Do not write
durable project data to an ephemeral deployment filesystem. Use a
separate execution host for container-based Python execution. Test OAuth
callbacks against the exact production domain. Document required
environment variables and build settings.

Environment separation

Use separate development and production settings. Use different OAuth
applications or callback registrations where practical. Never reuse
development secrets in production. Keep debug mode and verbose error
pages disabled in production. Use test repositories and test accounts
for integration tests. Confirm that production storage points to
production resources. Document rollback steps for each deployment.

Production security checklist

-   [ ] HTTPS is enforced.
-   [ ] Secrets are stored in the hosting provider's secret manager or
    environment settings.
-   [ ] Debug mode is disabled.
-   [ ] Session cookies use appropriate security attributes.
-   [ ] OAuth state validation is enabled.
-   [ ] Protected routes enforce server-side authorization.
-   [ ] API request sizes and rates are limited.
-   [ ] Execution workloads are isolated and resource-limited.
-   [ ] Logs redact credentials and tokens.
-   [ ] Backups and restoration have been tested.
-   [ ] Dependency updates and security alerts are reviewed.

Privacy principles

Collect only the user data required for the product's operation. Explain
what GitHub information is accessed and why. Do not store repository
contents longer than needed without a clear product reason. Provide a
documented way to request project or account data deletion when accounts
are supported. Protect stored files and tokens with appropriate access
controls. Do not use private project source code for unrelated purposes
without a clear, informed basis. Document any third-party services that
receive user data.

Backup and recovery

Define what must be backed up: database records, project files, and
essential configuration metadata. Never include secrets in public
backups or documentation. Automate backups for production data where
supported. Test restoring a backup into a separate environment. Document
expected recovery time and acceptable data loss. Keep recovery
instructions accessible to authorized maintainers. Review backup
retention and deletion policies periodically.

Dependency management

Pin or constrain dependencies for reproducible environments. Review
package maintainers and package names before adding dependencies. Use
lock files where supported by the chosen package manager. Update
dependencies in small, testable batches. Run tests after dependency
updates. Remove unused dependencies. Do not commit vendored libraries
without a documented reason.

Contribution workflow

Open an issue for significant features or architectural changes.
Describe the problem, intended behavior, and acceptance criteria. Keep
pull requests focused. Include screenshots or recordings for substantial
UI changes when useful. Include tests for bug fixes and new logic.
Update documentation when commands, routes, configuration, or behavior
changes. Request review for security-sensitive changes.

Issue reports

Describe the expected behavior. Describe the actual behavior. List
reproducible steps. Include the operating system, browser, Python
version, and relevant package versions. Include redacted error messages
and request identifiers when available. Remove access tokens, OAuth
codes, passwords, private source code, and personal data. State whether
the problem occurs locally, in production, or in both environments.

Roadmap: foundation

-   [ ] Verify current repository and dependency state.
-   [ ] Confirm Flask startup and configuration behavior.
-   [ ] Confirm how the frontend is served.
-   [ ] Add a repeatable local development guide.
-   [ ] Add automated baseline tests.
-   [ ] Define API response conventions.
-   [ ] Establish safe error handling and logging.

Roadmap: editor

-   [ ] Integrate and verify the selected code editor.
-   [ ] Add HTML, CSS, JavaScript, and Python modes.
-   [ ] Add file tabs and active-file state.
-   [ ] Add unsaved-change indicators.
-   [ ] Add save and save-error states.
-   [ ] Add keyboard navigation.
-   [ ] Add responsive editor panels.

Roadmap: project explorer

-   [ ] Define project and file data models.
-   [ ] Add authorized project listing.
-   [ ] Add file read and write operations.
-   [ ] Add create, rename, and delete operations.
-   [ ] Validate all file paths on the server.
-   [ ] Add size limits and quotas.
-   [ ] Add tests for cross-user access.

Roadmap: preview

-   [ ] Design an isolated HTML preview.
-   [ ] Define asset path resolution.
-   [ ] Prevent preview code from accessing application credentials.
-   [ ] Add console and runtime error reporting.
-   [ ] Test script and style isolation.
-   [ ] Document preview limitations.

Roadmap: authentication

-   [ ] Register a development OAuth application.
-   [ ] Implement server-side OAuth callback handling.
-   [ ] Validate OAuth state.
-   [ ] Create and rotate application sessions safely.
-   [ ] Add logout.
-   [ ] Add permission checks for project operations.
-   [ ] Test denied authorization and revoked permissions.

Roadmap: Python execution

-   [ ] Define the execution request and response schema.
-   [ ] Build a minimal execution image.
-   [ ] Run as a non-root user.
-   [ ] Add timeout and resource limits.
-   [ ] Restrict filesystem and network access.
-   [ ] Limit console output.
-   [ ] Clean up temporary workspaces.
-   [ ] Add adversarial execution tests.

Roadmap: deployment

-   [ ] Verify frontend build and hosting settings.
-   [ ] Verify Flask API deployment compatibility.
-   [ ] Select persistent database and file storage.
-   [ ] Deploy the execution service separately.
-   [ ] Configure production OAuth callback URLs.
-   [ ] Configure monitoring and backups.
-   [ ] Run the production security checklist.

API documentation template

For each endpoint, document: - Method and path. - Whether authentication
is required. - Required permission or ownership rule. - Request content
type and schema. - Success response schema. - Error response codes. -
Rate and size limits. - Side effects and retry behavior. - Relevant
tests.

Example API error format

The following is a suggested response format, not a claim about the
current implementation:

``` json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "The request contains invalid fields.",
    "request_id": "example-request-id"
  }
}
```

Do not expose internal stack traces or sensitive implementation details
to the browser.

Configuration reference template

Document each actual environment variable after checking the code.
Include its purpose, whether it is required, and which environments use
it. Use placeholders for example values. Mark secrets clearly as
server-only. Explain safe local setup and production configuration. Do
not invent configuration names and then imply that the application
already reads them.

Operational runbook

Start with the reported symptom and its scope. Check application health
without exposing secrets. Review recent deployment changes. Inspect
redacted logs and request identifiers. Check database and storage
connectivity. Check authentication callback URLs and session settings.
Check execution queue and worker health separately. Rollback only when
the rollback plan is known and data migrations are understood.

Troubleshooting: backend startup

Confirm the terminal is in the repository root. Confirm the virtual
environment is active. Confirm dependencies installed successfully.
Check Python import paths and package layout. Inspect configuration
errors without printing secret values. Check whether the configured port
is already in use. Read the first relevant traceback rather than
repeatedly restarting blindly.

Troubleshooting: frontend API errors

Open browser developer tools. Inspect the failed request URL and HTTP
status. Check the frontend API base URL. Check whether the backend is
running. Check CORS configuration when frontend and API use different
origins. Do not solve CORS by allowing every origin with credentials.
Check session cookies and HTTPS requirements. Confirm the API response
matches the frontend's expected schema.

Troubleshooting: GitHub OAuth

Confirm the GitHub OAuth App client ID is correct. Confirm the callback
URL exactly matches the registered callback. Confirm the server has the
client secret configured. Check that the OAuth `state` value is stored
and validated. Confirm local and production URLs are not mixed. Check
browser cookie behavior and HTTPS requirements. Do not paste OAuth
codes, client secrets, or access tokens into an issue.

Troubleshooting: Git

Use `git status` to see the current state. Use `git remote -v` to
confirm the repository destination. Use `git branch --show-current` to
confirm the branch. Use `git diff` to inspect unstaged edits. Use
`git diff --cached` to inspect staged edits. Resolve identity errors by
configuring the intended commit name and email. Never publish a commit
merely to test whether credentials work.

Security review questions

Can a user read another user's project by changing an identifier? Can a
file path escape the authorized project directory? Can preview code
access the IDE's cookies or privileged APIs? Can submitted Python access
the host filesystem or environment secrets? Can an unauthenticated user
call protected routes? Can an OAuth callback be forged or replayed? Can
a large request exhaust memory, storage, or worker capacity? Are error
messages and logs free of credentials?

Release checklist

-   [ ] All changes are reviewed.
-   [ ] Automated tests pass.
-   [ ] The working tree contains only intended changes.
-   [ ] No secrets are staged or committed.
-   [ ] Documentation matches shipped behavior.
-   [ ] Database migrations have been reviewed.
-   [ ] Production environment variables are configured.
-   [ ] Authentication and permissions are tested.
-   [ ] Monitoring and rollback steps are ready.
-   [ ] A smoke test passes after deployment.

License and ownership

Keep the repository's existing `LICENSE` file unless the project owner
intentionally changes licensing. Do not replace a license based on an
assumption about the project's intended distribution. Respect licenses
for third-party editor libraries, icons, fonts, and other assets.
Include required copyright and license notices for redistributed
dependencies. Consult appropriate legal advice for licensing decisions
that materially affect distribution.

Acknowledgements

Sonic-IDE is part of the Sonic Tech / Sonic AGI OS project vision.
Third-party libraries and services should be acknowledged in the
repository when required by their licenses or terms. Add credits only
after verifying the dependency and its licensing requirements.

Maintainer notes

Keep this README aligned with the actual codebase. Remove roadmap items
when they are completed and verified. Record important architecture
decisions in `docs/`. Keep setup steps reproducible on a clean
development machine. Prefer documented, tested behavior over
aspirational claims. Review the README during release preparation.

## Appendix: Development task template

1.  Task title: describe one observable change.
2.  Problem: explain the user or maintainer need.
3.  Scope: list what will change and what will not change.
4.  Dependencies: identify required services, packages, or
    configuration.
5.  Security impact: describe trust boundaries and permissions.
6.  Acceptance criteria: write measurable outcomes.
7.  Tests: name the unit, integration, and manual checks.
8.  Documentation: list the pages or comments that must change.
9.  Rollback: explain how to revert safely.

## Appendix: Feature acceptance checklist

1.  The feature has a clearly stated purpose.
2.  The feature has an owner or maintenance path.
3.  Inputs are validated on the server when applicable.
4.  Authorization is enforced server-side.
5.  Loading states are visible.
6.  Empty states are understandable.
7.  Failure states do not imply success.
8.  The feature has tests for expected behavior.
9.  The feature has tests for invalid input.
10. The feature has a documented limitation section.

## Appendix: Frontend review checklist

1.  HTML uses semantic elements.
2.  Interactive controls have accessible names.
3.  Keyboard users can reach all essential controls.
4.  Focus is visible.
5.  Colors meet readable contrast expectations.
6.  Layout works at narrow widths.
7.  Long filenames do not break the layout.
8.  Errors are shown near the relevant action.
9.  Network failures are handled.
10. No secrets are embedded in frontend assets.

## Appendix: Backend review checklist

1.  Routes validate incoming data.
2.  Services own business logic.
3.  Permissions are checked for each resource.
4.  Errors use consistent response shapes.
5.  Logs redact sensitive data.
6.  External requests have timeouts.
7.  Uploads and responses have size limits.
8.  Database queries are parameterized.
9.  Configuration is environment-driven.
10. Tests cover failure paths.

## Appendix: Code execution review checklist

1.  The worker runs separately from the web process.
2.  The worker uses a non-root identity.
3.  The worker receives no production secrets.
4.  The work directory is temporary.
5.  The filesystem is restricted.
6.  Network access is disabled by default.
7.  CPU usage is limited.
8.  Memory usage is limited.
9.  Execution time is limited.
10. Output size is limited.
11. Processes are cleaned up.
12. The worker reports a reliable exit status.

## Appendix: Authentication test cases

1.  A user can begin the OAuth flow.
2.  A valid callback completes authentication.
3.  A denied authorization returns safely.
4.  An invalid state value is rejected.
5.  A missing authorization code is handled.
6.  A provider error is handled.
7.  A session identifier changes after login.
8.  Logout invalidates the session.
9.  An expired session cannot access protected routes.
10. Revoked permissions are handled gracefully.

## Appendix: File operation test cases

1.  A user can list authorized project files.
2.  A user can open an authorized file.
3.  A user can save an authorized file.
4.  A user cannot read a file outside the project root.
5.  A user cannot overwrite another user's file.
6.  A user cannot use traversal sequences to escape a root.
7.  A rename conflict is reported.
8.  A missing file is reported clearly.
9.  An oversized file is rejected.
10. A failed write is not reported as successful.

## Appendix: Deployment verification cases

1.  The application starts with production configuration.
2.  Debug mode is disabled.
3.  HTTPS is configured.
4.  The frontend uses the production API URL.
5.  The OAuth callback uses the production domain.
6.  Database connectivity is verified.
7.  Persistent file storage is verified.
8.  Execution workers are reachable only through intended interfaces.
9.  Health checks do not expose secrets.
10. A rollback path is documented.

## Appendix: Documentation maintenance checklist

1.  Setup commands are tested on a clean environment.

2.  The repository tree matches the current files.

<!-- End of README.md: 1000 lines. Keep documentation aligned with verified behavior. -->
