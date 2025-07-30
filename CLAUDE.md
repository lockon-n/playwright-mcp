# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Build and Development
- `npm run build` - Compile TypeScript to lib/ directory
- `npm run watch` - Watch for changes and recompile TypeScript
- `npm run clean` - Remove compiled lib/ directory

### Testing
- `npm run test` - Run all Playwright tests
- `npm run ctest` - Run tests with Chrome only
- `npm run ftest` - Run tests with Firefox only  
- `npm run wtest` - Run tests with WebKit only

### Code Quality
- `npm run lint` - Run ESLint and TypeScript checks (also updates README)
- `npm run lint-fix` - Auto-fix ESLint issues
- `npm run update-readme` - Update tool documentation in README.md

### Running the Server
- `node lib/program.js` - Run the compiled MCP server
- `node cli.js` - Run via the CLI entry point
- `npm run run-server` - Run the browser server backend

## Architecture Overview

### Core Components

**Entry Points:**
- `cli.js` - Main CLI entry point that imports lib/program.js
- `src/program.ts` - Command-line argument parsing and server startup using Commander.js
- `src/index.ts` - Programmatic API entry point with createConnection()

**MCP Server Implementation:**
- `src/mcp/server.ts` - Core MCP server logic with tool registration and request handling
- `src/browserServerBackend.ts` - Main backend that implements ServerBackend interface
- `src/browserContextFactory.ts` - Factory for creating browser contexts (persistent vs isolated)

**Tool System:**
- `src/tools.ts` - Aggregates all available tools and handles capability filtering
- `src/tools/` - Individual tool implementations (navigate, click, type, screenshot, etc.)
- Each tool exports an array of Tool objects with schema validation via Zod

**Configuration:**
- `src/config.ts` - Configuration parsing and validation for CLI options and JSON config files
- Supports browser options, server settings, capabilities, network filtering, and output directories

**Browser Management:**
- `src/context.ts` - Global context management and cleanup
- `src/tab.ts` - Tab management and switching
- `src/browserContextFactory.ts` - Handles both persistent profiles and isolated sessions

### Key Design Patterns

**Tool Registration:** Tools are defined as arrays of objects with Zod schemas for input validation. The server automatically generates JSON schemas for MCP clients.

**Capability System:** Tools are filtered based on enabled capabilities (core, tabs, install, pdf, vision). Core tools are always available.

**Context Management:** The system supports both persistent browser profiles (default) and isolated sessions (--isolated flag).

**Transport Layer:** Supports both STDIO (default) and HTTP/SSE transports for different deployment scenarios.

### Test Infrastructure

- `tests/fixtures.ts` - Custom Playwright fixtures for MCP client testing
- `tests/testserver/` - HTTP/HTTPS test server implementation
- Supports testing across Chrome, Firefox, WebKit, and Edge browsers
- Docker testing mode available for headless Chromium

### Browser Extension Support

- `extension/` - Chrome/Edge extension for connecting to existing browser instances
- `src/extension/` - Extension backend with CDP relay functionality
- Requires the "Playwright MCP Bridge" browser extension to be installed

## Important Implementation Notes

- All source files must include Microsoft copyright headers (enforced by ESLint)
- TypeScript is compiled to ES modules in lib/ directory
- The project uses Node.js 18+ and targets ESNext
- Test files use custom expect matchers for MCP response validation
- Tools should implement proper error handling and return structured responses