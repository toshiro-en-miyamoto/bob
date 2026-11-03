id:: 6ac78d6a-9fc6-49a2-a8e7-4b2d32e62d73

- [Installing Bob Shell](https://bob.ibm.com/docs/shell/getting-started/install-and-setup)
  - Prerequits
    - Node.js 24+
      ```
      % brew install node
      % node --version
      v26.11.0
      ```
  - Installing Bob Shell using installation scripts
    - ```
      % curl -fsSL https://bob.ibm.com/download/bobshell.sh | bash
      Checking Node.js Installation
      ✓ Node.js version 26.11.0 detected (minimum 22.15 required)
      
      Package Manager Selection
      → Tip: You can skip this selection by using: --package-manager or --pm (e.g., --pm npm)
      → Only npm is available. Using it automatically.
      
      Downloading Bob Shell
      → Fetching latest version...
      ✓ Latest version: 2.0.6
      
      Verifying Package Integrity
      → Fetching package checksum...
      ✓ Expected SHA256: b3e75ad93f2b5236a4b60f1d008c3d6c12bd2748e81e210b80be302a3db0b5bd
      → Downloading package...
      → Verifying integrity...
      ✓ Package integrity verified
      
      Installing Bob Shell
      → Installing Bob Shell 2.0.6 with npm...
      
      Installation Complete! ✓
      ✓ Bob Shell 2.0.6 has been successfully installed
      → You can now run: bob
      
      Usage data is collected by default.
      → To opt out, type /settings, navigate to Enable Usage Metrics and set the flag to false
      ```
  - Or you can download Shell releases (e.g., `bobshell-2.0.6.tgz`) from [Bob Release page](https://bob.ibm.com/releases/?bob=shell) and install it by `npm`:
    - ```
      npm install --reg=https://registry.npmjs.org/ -g "<file-path>"
      ```
  - To uninstall Bob Shell, use the corresponding command for the package manager you used when you installed it:
    - ```
      npm uninstall -g bobshell
      ```
    - After uninstalling Bob Shell, you can optionally remove configuration files and data:
      - ```
        rm -rf ~/.bob
        ```
      - This permanently deletes your Bob Shell settings and any saved data.
- [Authentication](https://bob.ibm.com/docs/shell/getting-started/install-and-setup#authentication)
  - Bob Shell uses the same login entry point as all other Bob clients: `bob.ibm.com/login`. When you first run Bob Shell, or after your session expires, Bob opens your browser to this page.
    - Choose from 2 authentication methods, depending on your use case.
      | Method | Use case | Requirements |
      | ---- | ---- | ---- |
      | **SSO / IBMid** | Interactive sessions | Valid IBMid or corporate SSO account, browser access |
      | **API key** | Automation, CI/CD pipelines, non-interactive environments | API key from the Bob web portal, environment variable |
  - SSO / IBMid authentication (default)
    - When Bob Shell needs to authenticate, it opens bob.ibm.com/login in your browser. Enter your IBMid email address and click Continue to be routed to the right identity provider automatically.
      - If your organization has configured SSO, you are redirected to your company's login page.
      - Otherwise, you are redirected to IBMid.
    - After completing authentication in your browser, return to Bob Shell. Your session is established automatically.
    - Authentication tokens (OAuth) is stored in `~/.bob/settings/auth-secrets.json`:
      - ```
        {
          "bob.telemetry.sessionId": "nnnnnnn-nnnn-nnnn-nnnn-nnnnnnn",
          "bob.auth.tokens-https://api.<region>.bob.ibm.com":
          "{
            \"token\":\"<....secrets....>\",
            \"userId\":\"<your IBMid>\",
            \"expiresAt\":<nnnnnnnn>
          }"
        }
        ```
  - API key authentication
    - [Starting a non-interactive session](https://bob.ibm.com/docs/shell/getting-started/start-bobshell-non-interactive#starting-a-non-interactive-session) (`% bob run`) requires a valid API key set as the `BOB_API_KEY` environment variable. Interactive SSO or IBMid tokens from `bob chat` are not reused for non-interactive sessions. If `BOB_API_KEY` is not set, `bob run` exits with an authentication error.
    - API key authentication is ideal for automation, CI/CD pipelines, and non-interactive environments where browser-based authentication is not available.
      - Create an API key with **Scope** set to **Inference** in the Bob web portal.
      - After generating the key, download it or copy the key value.
      - Set the `BOB_API_KEY` environment variable with your API key value:
        - ```
          echo 'BOB_API_KEY="your-api-key-value"' >> ~/.zshrc
          ```
      - Run Bob Shell with your API key set:
        - ```
          bob run "your prompt"
          ```
        - Requirement: If your API key is of type general (rather than Inference), you must also pass `--team-id <team-id>` when running Bob Shell.
  - [API keys](https://bob.ibm.com/docs/ide/account/api-keys#api-key-types)
    - You can create two kinds of API keys:
      - | API key type | Scope and usage |
        |---|---|
        | General | Scoped to your subscription instance and usable across multiple services. For inference requests, you must also include the team ID header. |
        | Inference | Scoped to a specific subscription instance and team, and limited to inference requests only. No additional instance or team headers are required. |
    - Use API keys when you need to:
      - Authenticate Bob Shell in non-interactive sessions
      - Run Bob in CI/CD pipelines or scheduled automation
      - Avoid browser-based sign-in for scripted workflows
      - Authenticate inference requests with either a general or inference API key
    - Create an API key
      - Go to [IBM Bob site](https://bob.ibm.com/) and log in.
      - Choose a team if you have one you belong to.
      - Open your subscription instance:
        - 2026-10-08-22-22-52
      - Navigate to the API key management section:
        - 2026-10-08-22-26-15
      - Create API key:
        - 2026-10-09-15-31-47
      - On creating an API Key:
        - 2026-10-09-15-38-18
- Why the inference API key?
  - Prompt to Google Gemini
    - > IBM Bob what can you not do with inference api keys
    - Unlike a general API key, an inference API key cannot:
      - **Perform general account or administrative management**: You cannot use an inference key to manage, create, list, view details of, or revoke other API keys across users or subscription instances.
      - **Bypass team or instance scoping boundaries outside its designated scope**: It is locked down to inference requests on the specific subscription instance and team it was generated for.
      - **Require or accept extra team headers**: Unlike general API keys (which may require passing `--team-id <team-id>` when running Bob Shell), inference keys are pre-scoped, meaning they cannot be dynamically reassigned across multiple arbitrary teams without matching the key's explicit scope.
      - **Be viewed again after creation**: Just like other keys, once generated via IBM Bob Docs, the plain-text value cannot be retrieved or viewed a second time from the dashboard.
  - Prompt to Google Gemini
    - > IBM Bob why is inference api key necessary
    - In IBM Bob, an Inference API key is necessary to authenticate and authorize programmatic or automated requests sent to the AI model gateway without requiring an interactive, browser-based sign-in.
    - The primary reasons this specific key is required include:
      - **Non-Interactive Automation**: It allows you to run Bob Shell in non-interactive sessions, execute background scripts, or trigger AI features within CI/CD pipelines.
      - **Bypassing Browser Logins**: Scripted workflows cannot prompt a user to manually log into a web browser. The API key securely passes credentials via environment variables (`BOB_API_KEY`) or runtime arguments.
      - **Scoped Security Restrictions**: Unlike general-purpose API keys, an Inference API key is strictly limited to inference requests only. This limits your security exposure because even if the key is compromised, it cannot be used to modify account configurations or access administration panels.
      - **Streamlined Header Routing**: When you use a specialized Inference API key, it is already pre-scoped to your subscription instance and team. This means your code does not need to pass an explicit team ID header with every payload, simplifying your development setup compared to a general key.
  - Prompt to Google Gemini:
    - > IBM Bob the inference request vs other requests
    - The core distinction between an inference request and other types of requests (often categorized under general administrative or state-altering requests) comes down to scope, authorization, and network headers.
      - | Key Dimension | Inference Request | Other / General Requests |
        |---|---|---|
        | Primary Scope | Restricts actions strictly to model inferencing (e.g., text generation, code completions, chat responses). | Broadly handles workspace, administrative, or platform operations (e.g., listing keys, billing, managing configs). |
        | Context Boundaries | Scoped strictly to a specific subscription instance and team. | Requires explicit, broader tenant or global organization headers to modify high-level resources. |
        | Header Requirements | No additional instance or team headers are required at the payload level. | Typically demands explicit routing/instance headers to ensure cross-tenant separation. |
        | API Key Type | Can use either a general API key or a dedicated inference-only API key. | Requires a general / administrative API key; inference-restricted keys will be denied. |
        | Automation Profile | Optimized for headless, non-interactive sessions (like CI/CD pipelines or automated Bob Shell review scripts). | Designed for interactive browser-based sessions or workspace setups. |
    - Key Technical Differences
      - Security Isolation via Tailored API Keys
        - **Inference Request**: If a developer or a pipeline utilizes an inference-scoped key, the request can only query LLM endpoints via the model gateway. It cannot delete project data, read account billing details, or revoke other tokens.
        - **Other Requests**: Admin operations like listing active keys for all users in the instance or revoking tokens require an administrative key.
      - Stateless Execution vs. Stateful Workspace Adjustments
        - **Inference Requests** are designed to pass a payload (like prompt context, file @mentions, or diff files), receive token generation from the foundation model, and immediately delete the payload from the processing pod to ensure data security.
        - **Other Requests** modify system state, such as updating your workspace settings, toggling tool auto-approval rules, or storing permanent architectural markdown documentation inside your repository.
- [Configuring Bob Shell](https://bob.ibm.com/docs/shell/configuration/configuring)
  - Bob Shell stores settings in two locations:
    - | Scope | File |
      | ---- | ---- | ---- |
      | User (all workspaces) | `~/.bob/settings/settings.json` |
      | Project (current workspace) | `<workspace>/.bob/` |
    - CLI flags always override file values for the current session.
    - You can also change settings interactively with the `/settings` slash command inside `bob chat`.
  - Settings (Bob Shell 2.0.6)
    - | Bob Shell Settings Schema | Settings | Default | Description |
      | ---- | ---- | ---- | ---- |
      | `session.maxTurns` | Max Turns | 100 | Maximum agentic turns per session (0 = unlimited). |
      | `enableFileMentions` | Enable File Mentions | false | Allow @file and @folder mentions to include file content in prompts. |
      | `session.mcp` | Enable MCP Tools for new tasks | true | When disabled, new tasks will not include configured MCP server tools. Existing tasks keep their current tool set. |
      | `session.subagents` | Enable Subagents | true | Allow spawning subagents during a session. |
      | `experiments.webFetch` | Enable Web Fetch | false | When enabled, new tasks can fetch and read content from public HTTP and HTTPS URLs. |
      | `approval.outsideWorkspaceAllowed` | Allow outside workspace tool requests | false | When enabled, read and write file tools can request access to files outside your workspace directory - you will still be asked to approve each request unless auto-approved per tool group. |
      |  `security.folderTrust.enabled` | Folder Trust | true | Prompt to configure trust level before starting Bob Shell in an unfamiliar folder. Requires restart to take effect. |
      | `logging.logLevel` | Log Level | "info" | Verbosity of log output. |
      | `tasks.retentionDays` | Task Retention (days) | 30 | How long tasks are kept. Set to 0 to disable cleanup. |
      | `locale` | Language | auto (system) | Display language, such as "en". |
      | `telemetry.enabled` | Enable Telemetry | true | Telemetry is required for Bobalytics collection to function. Disabling this will prevent usage data from being sent. |
      | `bobShell.autoUpdate` | Enable auto-updates | true | Enable automatic updates of Bob Shell. |
      | `bobShell.vimMode` | Vim Mode | false | Enable vim key-bindings in the input prompt. |
    - My favorite settings
      - `enableFileMentions`: `true`
      - `locale`: `en`
- [Slash commands](https://bob.ibm.com/docs/shell/features/slash-commands)
  - Slash commands provide several key benefits:
    - **Workflow automation**: Turn complex multi-step processes into single commands
    - **Team standardization**: Share commands across your team for consistent practices
    - **Context preservation**: Include project-specific context in every command
    - **Quick access**: Fuzzy search and autocomplete for instant command discovery
  - Bob Shell includes the following built-in slash commands:
  - When you type `/` in Bob Shell, a menu appears showing all available commands.
    - | Built-in slash command | Description |
      |---|---|
      | `/help` | Show all available slash commands |
      | `/hooks` | Open the hooks management dialog. The dialog lists all configured global and workspace hooks with their event type, matcher, scope, status, and command. Toggle individual hooks on or off. |
      | `/init` | Initialize Bob in the current workspace |
      | `/clear` | Clear the screen and reset conversation history |
      | `/copy` | Copy an item from conversation history to the clipboard |
      | `/compact` | Intelligently compact the context window to free up space |
      | `/exit` or `/quit` | Exit the CLI |
      | `/export` | Export the current task to a file (usage: /export <json,markdown> [file-name]) |
      | `/resume` | Or `/history`. Browse and resume a previous conversation. |
      | `/team` | Select a team |
      | `/manage-secrets` | Manage secrets used in MCP server configurations |
      | `/processes` | View all running Shell processes across tasks |
      | `/mcp` | Manage configured MCP servers |
      | `/mode` | Switch the active mode (opens a picker) |
      | `/editor` | Open the current prompt in your preferred terminal editor. The editor is resolved from `$VISUAL`, `$EDITOR`, or `vi`. When you save and close the editor, the text is loaded back into the prompt. |
      | `/vim` | Toggle vim key-binding mode on/off |
      | `/skills` | Insert a skill reference into the prompt |
      | `/status` or `/info` | View session status, usage, and version information |
      | `/docs` | Open Bob Shell documentation in your browser |
      | `/logs` | Open the latest log file with the system viewer |
      | `/bug` | Submit a bug report |
      | `/settings` | View and edit Bob Shell settings |
      | `/permissions` | Or `/trust`. View and manage tool approval permissions. |
      | `/terminal-setup` | Configure your terminal for multiline input (Option+Enter) |
  - These commands come from two sources:
    - | Command Type | Source | Purpose |
      | ---- | ---- | ---- |
      | Custom workflow | `.bob/commands/` or `~/.bob/commands/` | User-created automation for specific tasks |
      | Mode commands | Built-in and custom modes | Switch Bob's operational context |
  - Custom commands extend Bob's functionality by adding markdown files to specific directories:
    - | Location | Scope | Path |
      | ---- | ---- | ---- |
      | Project-specific | Available in current project only | `.bob/commands/` in your workspace root |
      | Global | Available in all projects | `~/.bob/commands/` in your home directory |
  - The filename becomes the command name. For example:
    - ```
      .bob/commands/
      ├── review.md         → /review
      ├── test-api.md       → /test-api
      └── deploy-check.md   → /deploy-check
      ```
  - Command names:
    - lowercase characters only
    - replace spaces with dashes
    - special characters are not allowed
    - leading and trailing dashes are not allowed
  - Commands with frontmatter:
    - ```
      ---
      description: Create a new API endpoint
      argument-hint: <endpoint-name> <http-method>
      ---
      Create a new API endpoint called $1 that handles $2 requests.
      Include proper error handling and documentation.
      ```
    - Frontmatter fields:
      - | Field | Purpose | Example |
        | `description` | Appears in the command menu | "Create a new API endpoint" |
        | `argument-hint` | Shows expected arguments | "<endpoint-name> <http-method>" |
- Tutorial
  - [Start a Project](https://bob.ibm.com/docs/shell/tutorials/start-a-project)
    - Clone the Galaxium Travels demo code. The clone command also checks out the `bob-learning-path-branch` that contains the code you use in the tutorials.
      - ```
        % git clone -b bob-learning-path-branch https://github.com/IBM/galaxium-travels
        ```
    - Start Bob Shell in the project
      - ```
        % cd galaxium-travels
        % bob chat
        ```
      - If Bob Shell asks whether you trust the folder, trust it so Bob can work in the project.
    - Generated `AGENTS.md` with `/init`
      - Make sure that Bob Shell is in `agent` mode.
      - Enter the `/init` command at the Bob Shell prompt:
        ```
        > /init
        ```
      - Bob Shell suggests the following Todo list:
        1. Check for existing `AGENTS.md` and AI assistant rule files
        2. Identify stack, build tools, package manager
        3. Extract commands (build, test, lint, run single test)
        4. Map core architecture and components
        5. Document critical patterns and code style
        6. Compile `AGENTS.md` and mode-specific files
      - Approve tools
        - to read project files
        - to compile `AGENTS.md` files (root and rules)
    - `/init` creates four files with the following response:
      - `AGENTS.md` — General project guidance covering:
        - **AGENTS.md**
          - This file provides guidance to agents when working with code in this repository.
          - **Architecture**
            - Monorepo with two separate sub-projects — commands must be run from their respective directories:
              - `booking_system_backend/` — Python FastAPI + SQLite + FastMCP
              - `booking_system_frontend/` — React 19 + Vite + TypeScript + Tailwind
            - The backend **dual-exposes** all operations as both REST endpoints and MCP tools (via `fastmcp`). The MCP server is mounted at `/mcp` inside the FastAPI app. MCP tools in [`server.py`](booking_system_backend/server.py) open their own `SessionLocal()` directly (no FastAPI dependency injection) — REST endpoints use `Depends(get_db)`.
          - **Backend Commands**
            - All commands run from `booking_system_backend/` with the venv activated:
            - ```
              cd booking_system_backend
              python -m venv .venv && source .venv/bin/activate
              pip install -r requirements.txt
              
              # Run all tests
              pytest
              
              # Run a single test function
              pytest tests/test_rest.py::TestBookEndpoint::test_book_flight_success
              
              # Run a single test file
              pytest tests/test_services.py
              
              # Start server (port 8080, Swagger at /docs)
              python server.py
              ```
          - **Frontend Commands**
            - All commands run from `booking_system_frontend/`:
            - ```
              cd booking_system_frontend
              npm install
              npm run dev       # http://localhost:5173
              npm run build     # tsc -b && vite build
              npm run lint      # eslint
              ```
            - Frontend requires a `.env` file (copy from `.env.example`): `VITE_API_URL=http://localhost:8080`.
          - **Backend Patterns**
            - **Error handling**: Service functions return `ErrorResponse` (a Pydantic model with `success=False`, `error`, `error_code`, `details`) instead of raising exceptions. REST endpoints return `Union[SuccessModel, ErrorResponse]` — all responses use HTTP 200. Callers must check `isinstance(result, ErrorResponse)` or `response.success == False`.
            - **ORM primary keys** use `user_id`, `flight_id`, `booking_id` (not `id`) in both SQLAlchemy models and Pydantic schemas.
            - **Pydantic schemas** require `class Config: from_attributes = True` and use `.model_validate(orm_object)` for conversion.
            - **Test isolation**: `conftest.py` uses in-memory SQLite with `StaticPool`, patches `SessionLocal` in both `db` and `server` modules via `monkeypatch`, and suppresses `seed()` during tests.
          - **Frontend Patterns**
            - All API calls go through the single axios instance in [`src/services/api.ts`](booking_system_frontend/src/services/api.ts). The interceptor re-rejects with the backend's `ErrorResponse` shape on HTTP errors.
            - Use `isErrorResponse(response)` (exported from `api.ts`) to discriminate `User | ErrorResponse` etc. — checks `response.success === false`.
            - All shared types are in [`src/types/index.ts`](booking_system_frontend/src/types/index.ts) and mirror backend Pydantic schemas exactly.
            - Custom Tailwind tokens: `space-dark`, `space-blue`, `cosmic-purple`, `nebula-pink`, `alien-green`, `solar-orange`, `star-white`; gradients `space-gradient`, `cosmic-gradient`; animations `float`, `twinkle`.
            - Common UI components are barrel-exported from [`src/components/common/index.ts`](booking_system_frontend/src/components/common/index.ts).
      - `.bob/rules-agent/AGENTS.md` — Coding rules:
        - **Project Coding Rules (Non-Obvious Only)**
          - **Backend tests must run from `booking_system_backend/`** — pytest's `testpaths = tests` is relative, and `conftest.py` inserts `parent.parent` into `sys.path` so bare module imports (`from models import ...`) resolve. Running pytest from repo root will fail.
          - **Never raise exceptions in service functions** — return `ErrorResponse(...)` instead. MCP tool wrappers in `server.py` then do `raise Exception(result.details or result.error)` to surface errors to agents.
          - **All REST endpoints return HTTP 200 even on logical errors** — the `Union[SuccessType, ErrorResponse]` response model means callers must inspect `success` field, not status code.
          - **MCP tools create their own DB sessions** via `SessionLocal()` with manual `try/finally db.close()` — do NOT use `get_db()` / `Depends` there (MCP tools are not FastAPI request handlers).
          - **Pydantic schema → ORM**: always use `.model_validate(orm_obj)` (not `from_orm`), and ensure `class Config: from_attributes = True` is present on output schemas.
          - **Frontend error discrimination**: import and use `isErrorResponse()` from `src/services/api.ts` — do not check `.error` field directly, as successful responses can also have string fields.
          - **Frontend env var**: the axios base URL falls back to `http://localhost:8080` if `VITE_API_URL` is unset — no `.env` file is strictly required for local dev.
          - **MCP server must be instantiated before `FastAPI()`** — the comment in `server.py` warns this is required to correctly combine lifespans.
      - `.bob/rules-ask/AGENTS.md` — Documentation context:
        - **Project Documentation Context (Non-Obvious Only)**
          - **`services/` in the backend** contains pure business logic (no FastAPI), not route handlers. The same functions are called by both REST endpoints and MCP tools.
          - **No test coverage for the frontend** — there is no frontend test framework configured; only the Python backend has tests.
          - **`booking_system_backend/seed.py`** populates the SQLite DB with initial flights on every server startup (called in lifespan). It is patched to a no-op in all tests.
          - **Swagger UI** is available at `http://localhost:8080/docs` — useful for exploring the full REST API interactively.
          - **MCP endpoint** is at `http://localhost:8080/mcp` — the same process serves both REST and MCP.
          - **`src/components/common/index.ts`** is the barrel export for all shared UI primitives — check there before creating new components.
      - `.bob/rules-plan/AGENTS.md` — Architectural constraints:
        - **Project Architecture Rules (Non-Obvious Only)**
          - **Dual interface constraint**: every user-facing operation must be exposed as both a REST endpoint and an MCP tool (in `server.py`). Adding one without the other is incomplete.
          - **Service layer is the source of truth**: both REST and MCP call the same `services/flight.py`, `services/booking.py`, `services/user.py` functions — logic lives only there, never duplicated in handlers.
          - **Error model is return-based, not exception-based**: service functions always return `BookingOut | ErrorResponse` etc. Adding exceptions would break how MCP tools re-raise them and how REST endpoints serialize the `Union` response.
          - **SQLite file `booking.db`** is created in `booking_system_backend/` at runtime — it is not committed; schema is rebuilt via `init_db()` each startup and seeded via `seed()`.
          - **Pydantic field naming follows the ORM** (`user_id`, `flight_id`, `booking_id`) — frontend `types/index.ts` mirrors these exactly. Renaming in one place requires changing all three layers.
          - **No authentication layer** — `user_id` is trusted from the client; `book_flight` validates that the supplied `name` matches the stored name as a soft integrity check only.
          - **FastMCP + FastAPI lifespan ordering**: `mcp_app = mcp.http_app()` must be called before `FastAPI(lifespan=...)` is constructed. Reordering breaks combined lifespan management.
  - How Bob Shell loads `AGENTS.md` files
    - Bob Shell automatically loads context files such as `AGENTS.md` at the start of every task.
      - Global context: `~/.bob/AGENTS.md` applies to all projects.
      - Project context: `AGENTS.md` in the project root and parent directories applies to the project.
      - Local context: `AGENTS.md` in subdirectories applies to that subdirectory, for component-specific instructions.
    - To give Bob instructions for a single mode, add mode-specific rules in `.bob/rules-<mode>/` directories, such as `.bob/rules-plan/` for Plan mode.
-