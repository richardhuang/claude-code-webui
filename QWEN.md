# Claude Code Web UI - Project Context

## Project Overview

**Claude Code Web UI** is a modern web-based interface for the Claude Code CLI tool. It transforms command-line interactions into an intuitive web chat interface with real-time streaming responses, making Claude Code accessible from any device with a browser (including mobile).

### Key Technologies

- **Backend**: Deno/Node.js + TypeScript + Hono framework
- **Frontend**: React 19 + Vite + TypeScript + TailwindCSS + React Router
- **Shared**: TypeScript type definitions
- **Build**: Deno compile for single binary distribution
- **Testing**: Vitest + Testing Library (frontend) + Deno test (backend)
- **CI/CD**: GitHub Actions with automated releases via tagpr

### Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Web Browser (Frontend)                   │
│              React + Vite + TailwindCSS + SWC               │
│                    Port: 3000 (dev)                         │
└─────────────────────────────────────────────────────────────┘
                              │
                              │ HTTP API
                              │
┌─────────────────────────────────────────────────────────────┐
│                   Backend Server                            │
│              Deno/Node.js + Hono + TypeScript               │
│                    Port: 8080 (default)                     │
│                                                             │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  Claude CLI Integration                               │  │
│  │  - Stream JSON output from Claude Code SDK            │  │
│  │  - Session management & continuity                    │  │
│  │  - Tool permission handling                           │  │
│  │  - Project directory selection                        │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

### Core Features

- **Real-time streaming responses** - Live Claude Code output in chat interface
- **Project directory selection** - Visual project picker for context-aware sessions
- **Conversation history** - Browse and restore previous chat sessions
- **Tool permission management** - Granular control over Claude's tool access
- **Dark/light theme support** - Automatic system preference detection
- **Mobile-responsive design** - Touch-optimized interface
- **Permission mode switching** - Toggle between normal and plan mode execution
- **Single binary distribution** - Self-contained executable

## Project Structure

```
claude-code-webui/
├── backend/              # Server application
│   ├── cli/             # Entry points (deno.ts, node.ts, args.ts, validation.ts)
│   ├── handlers/        # API route handlers (chat, projects, histories, abort)
│   ├── runtime/         # Runtime abstraction layer (Deno/Node.js)
│   ├── history/         # History processing utilities
│   ├── middleware/      # HTTP middleware (CORS, logging, etc.)
│   ├── utils/           # Utility modules (logger.ts)
│   ├── tests/           # Backend test files
│   ├── scripts/         # Build and packaging scripts
│   ├── app.ts           # Main application entry
│   ├── types.ts         # Backend-specific types
│   ├── deno.json        # Deno configuration & tasks
│   ├── package.json     # Node.js dependencies
│   └── tsconfig.json    # TypeScript configuration
│
├── frontend/            # React application
│   ├── src/
│   │   ├── config/      # API configuration
│   │   ├── utils/       # Utilities and constants
│   │   ├── hooks/       # Custom hooks (streaming, theme, chat state)
│   │   ├── components/  # UI components (chat, messages, dialogs)
│   │   ├── types/       # Frontend type definitions
│   │   └── contexts/    # React contexts
│   ├── tests/           # Frontend test files
│   ├── scripts/         # Demo recording & screenshot scripts
│   ├── index.html       # HTML entry point
│   ├── vite.config.ts   # Vite configuration
│   ├── playwright.config.ts  # Playwright configuration
│   └── package.json     # Dependencies & scripts
│
├── shared/              # Shared TypeScript types
│   └── types.ts         # Common interfaces (StreamResponse, ChatRequest, etc.)
│
├── docs/                # Documentation and assets
├── .github/             # GitHub workflows & templates
├── Makefile             # Unified build & quality commands
├── .lefthook.yml        # Git hooks configuration
├── .tagpr               # Automated release configuration
├── CHANGELOG.md         # Version history
├── CLAUDE.md            # Detailed technical documentation
└── README.md            # User-facing documentation
```

## Building and Running

### Prerequisites

- ✅ **Claude CLI** installed and authenticated
- ✅ **Node.js >=20.0.0** (for development)
- ✅ **Deno** (for backend development)
- ✅ **dotenvx** (optional, for environment management)

### Quick Start (Production)

```bash
# Install via npm
npm install -g claude-code-webui

# Run the server
claude-code-webui

# Access at http://localhost:8080
```

### Development Mode

```bash
# Terminal 1 - Backend (Deno)
cd backend
deno task dev

# Terminal 2 - Backend (Node.js alternative)
cd backend
npm run dev

# Terminal 3 - Frontend
cd frontend
npm run dev
```

**Access**: Frontend http://localhost:3000, Backend http://localhost:8080

### Port Configuration

Create `.env` file in project root:
```bash
PORT=9000
```

Or set environment variables directly:
```bash
PORT=9000 DEBUG=true make dev-backend
```

### Building for Production

```bash
# Build frontend and backend into single binary
make build

# Or build backend only
cd backend && deno task build
```

## Development Commands

### Quality Checks (Run Before Commit)

```bash
# Run all quality checks (format, lint, typecheck, test, build)
make check

# Individual checks
make format          # Format both frontend and backend
make lint            # Lint both
make typecheck       # Type check both
make test            # Test both

# Individual project checks
make format-frontend  make format-backend
make lint-frontend    make lint-backend
make typecheck-frontend  make typecheck-backend
make test-frontend    make test-backend
```

### Development Tasks

```bash
# Start development servers
make dev-backend     # Backend with Deno
make dev-frontend    # Frontend with Vite

# Build tasks
make build-frontend  # Build frontend only
make build-backend   # Build backend only
```

### Specialized Commands

```bash
# Format specific files
make format-files FILES="file1 file2"

# Install dependencies
make install

# Clean build artifacts
make clean
```

### Backend-Specific (Deno)

```bash
cd backend
deno task dev        # Start dev server with auto-reload
deno task build      # Build single binary
deno task format     # Format code
deno task lint       # Lint code
deno task check      # Type check
```

### Frontend-Specific

```bash
cd frontend
npm run dev          # Start dev server
npm run build        # Build for production
npm run test         # Run tests (watch mode)
npm run test:run     # Run tests (one-time)
npm run format       # Format code
npm run lint         # Lint code
npm run typecheck    # Type check
```

## Development Conventions

### Code Quality Standards

1. **TypeScript Strict Mode**: All code uses strict TypeScript checking
2. **Consistent Formatting**: Prettier for frontend, Deno fmt for backend
3. **ESLint**: Custom configurations for both frontend and backend
4. **Testing**: Unit tests with Vitest (frontend) and Deno test (backend)
5. **Auto-formatting**: Lefthook runs `make check` before commits

### Git Workflow

```bash
# Install git hooks (one-time)
lefthook install

# Create feature branch
git checkout -b feature/name

# Commit changes (hooks run automatically)
git commit -m "description"

# Push and create PR
git push origin feature/name
gh pr create --title "..." --body "..." --label "feature"
```

### Labels Convention

- 🐛 `bug` - Bug fixes
- ✨ `feature` - New features
- 💥 `breaking` - Breaking changes
- 📚 `documentation` - Docs updates
- ⚡ `performance` - Performance improvements
- 🔨 `refactor` - Code refactoring
- 🧪 `test` - Test additions/changes
- 🔧 `chore` - Maintenance tasks
- 🖥️ `backend` - Backend-specific changes
- 🎨 `frontend` - Frontend-specific changes

### Release Process (Automated with tagpr)

1. Merge feature PRs to `main`
2. `tagpr` creates a release PR automatically
3. Add version labels if needed (`major-release`, `minor-release`)
4. Merge release PR → automatic tag creation
5. GitHub Actions builds binaries for all platforms

## Key Design Decisions

1. **Runtime Abstraction**: Backend uses a unified Runtime interface supporting both Deno and Node.js, allowing business logic to be platform-agnostic.

2. **Universal CLI Detection**: Claude CLI path detection works across npm, pnpm, asdf, yarn, and native binary installations using script path tracing.

3. **Raw JSON Streaming**: Claude responses are streamed as raw JSON without modification, giving the frontend full flexibility in rendering.

4. **Modular Architecture**: Frontend uses specialized hooks (e.g., `useStreaming`, `useTheme`, `useChatState`) for maintainability.

5. **TypeScript Throughout**: Consistent type safety across backend, frontend, and shared types.

6. **Project Directory Selection**: Users can select working directories, enabling Claude to access files in chosen projects.

7. **Session Continuity**: Conversation history is preserved using Claude Code SDK's session management with `session_id`.

## API Reference

### Backend Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/projects` | List available project directories |
| POST | `/api/chat` | Send chat message with streaming response |
| POST | `/api/abort/:requestId` | Abort ongoing request |
| GET | `/api/projects/:encodedProjectName/histories` | List conversation histories |
| GET | `/api/projects/:encodedProjectName/histories/:sessionId` | Get specific conversation |

### Request/Response Types

See `shared/types.ts` for detailed interfaces:
- `ChatRequest` - Message, sessionId, requestId, allowedTools, workingDirectory, permissionMode
- `StreamResponse` - type (claude_json/error/done/aborted), data, error
- `ProjectInfo` - path, encodedName
- `ConversationSummary` - sessionId, startTime, lastTime, messageCount, lastMessagePreview
- `ConversationHistory` - sessionId, messages, metadata

## Claude Code SDK Integration

The backend uses `@anthropic-ai/claude-code` SDK to execute commands:

```typescript
// Key options
--output-format stream-json  // Streaming JSON responses
--verbose                     // Detailed execution info
-p <message>                  // Prompt mode
```

### Message Types

- **system**: Initialization message with `cwd` field
- **assistant**: Response content with nested `message.content` array
- **result**: Execution summary with `subtype` field

### Session Management

1. First message starts new Claude session
2. Frontend extracts `session_id` from SDK messages
3. Subsequent messages include `session_id` for context
4. Backend passes `session_id` to SDK via `options.resume`

## Testing

### Frontend Testing

```bash
# Run tests (watch mode)
npm run test

# Run tests (one-time)
npm run test:run

# Run with coverage
npm run test -- --coverage
```

### Backend Testing

```bash
# Run backend tests
cd backend
deno test tests/**/*.ts

# Or via Makefile
make test-backend
```

### Playwright (Demo/Recording)

```bash
# Install Playwright browsers
cd frontend && npm run playwright:install

# Record demo
cd frontend && npm run record-demo

# Capture screenshots
cd frontend && npm run capture-screenshots
```

## Configuration Files

### Backend (`backend/deno.json`)

- Deno compiler options
- Task definitions (dev, build, format, lint, check)
- Import mappings (Hono, Claude Code SDK, Logtape)

### Frontend (`frontend/vite.config.ts`)

- React SWC plugin
- Path aliases
- Build configuration

### Quality (`Makefile`, `.lefthook.yml`)

- Unified quality check commands
- Pre-commit hook running `make check`

## Security Considerations

- **No built-in authentication** - Designed for local/single-user use
- **Local execution** - Claude CLI runs locally, no external servers
- **Network exposure** - Configurable via `--host 0.0.0.0` but use with caution
- **File access** - Claude can read/write files in selected project directories

**Best Practice**: Use default localhost binding for local development. Only expose to network on trusted networks.

## Common Development Tasks

### Adding a New API Endpoint

1. Define types in `shared/types.ts`
2. Add handler in `backend/handlers/`
3. Register route in `backend/app.ts`
4. Add frontend API call in `frontend/src/config/`
5. Update tests

### Adding a New Feature

1. Create feature branch: `git checkout -b feature/name`
2. Implement in appropriate module (backend/frontend)
3. Add tests
4. Run `make check` to verify quality
5. Commit with descriptive message
6. Push and create PR

### Debugging

```bash
# Enable debug mode
claude-code-webui --debug

# Or set environment variable
DEBUG=true make dev-backend
```

## Troubleshooting

### Claude CLI Path Detection

If Claude CLI isn't found:

```bash
# Find Claude path
which claude

# Or for environment managers
volta which claude
asdf which claude

# Run with explicit path
claude-code-webui --claude-path "$(which claude)"
```

### Port Already in Use

```bash
# Use different port
claude-code-webui --port 9000

# Or set environment variable
PORT=9000 claude-code-webui
```

### Build Errors

```bash
# Clean and rebuild
make clean
make build

# Or for individual projects
cd frontend && rm -rf node_modules dist && npm install
cd backend && rm -rf deno.lock dist && deno cache cli/deno.ts
```

## Related Documentation

- **README.md**: User-facing documentation with quick start and screenshots
- **CLAUDE.md**: Detailed technical documentation (architecture, SDK types, MCP integration)
- **CHANGELOG.md**: Version history and release notes
- **docs/**: Additional documentation and assets

## Important Notes

1. **Always run from project root** - Use full paths for cd commands
2. **Type consistency** - Update both frontend and backend Claude Code SDK versions together
3. **Quality checks** - Run `make check` before every commit
4. **Testing** - Add tests for new features or bug fixes
5. **Documentation** - Update README/CLAUDE.md for user-facing changes

## Claude Code Dependency Management

**Policy**: Fixed versions (no caret `^`) for consistency

**Update Procedure**:
1. Check current versions in `frontend/package.json` and `backend/deno.json`
2. Update frontend: `npm install @anthropic-ai/claude-code@version`
3. Update backend imports in `deno.json`
4. Update backend package.json
5. Run `make check` to verify

## MCP Integration

Playwright MCP server is configured for automated browser testing:

```json
{
  "mcpServers": {
    "playwright": {
      "type": "stdio",
      "command": "npx",
      "args": ["@playwright/mcp@latest"]
    }
  }
}
```

Use by saying "**playwright mcp**" in chat requests for browser automation.
