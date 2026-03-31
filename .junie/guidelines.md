# Tableau MCP Development Guidelines

This document provides essential information for developers working on the Tableau MCP project.

## Build and Configuration

### Prerequisites
- **Node.js**: Version 22.7.5 or newer is required.
- **Dependencies**: Install all dependencies (including devDependencies) using:
  ```bash
  npm install
  ```

### Build Commands
- **Production Build**: Compiles TypeScript to optimized JavaScript in the `build/` directory.
  ```bash
  npm run build
  ```
- **Development Build**: Compiles with source maps and without minification.
  ```bash
  npm run build:dev
  ```
- **Docker Build**: Creates a Docker image named `tableau-mcp`.
  ```bash
  npm run build:docker
  ```

### Configuration
The server can be configured via environment variables (often loaded from a `.env` file) or configuration files:
- `config.stdio.json`: Configuration for Standard I/O transport.
- `config.http.json`: Configuration for HTTP transport.
- `config.docker.json`: Configuration for Docker execution.

Key environment variables:
- `SERVER`: Your Tableau Server URL.
- `SITE_NAME`: The Tableau site name.
- `PAT_NAME`: Personal Access Token name.
- `PAT_VALUE`: Personal Access Token value.

## Testing

### Testing Framework
The project uses [Vitest](https://vitest.dev/) for unit and integration testing, and [Playwright](https://playwright.dev/) for E2E Tableau OAuth testing.

### Running Tests
- **Unit Tests**: Runs tests located in the `src/` directory.
  ```bash
  npm test
  ```
- **E2E Tests**: Runs end-to-end tests.
  ```bash
  npm run test:e2e
  ```
- **OAuth Tests**: Runs Playwright tests for Tableau OAuth.
  ```bash
  npm run test:oauth:tableau
  ```

### Adding New Tests
1. Create a file ending in `.test.ts` (e.g., `src/my-feature.test.ts`).
2. Use `describe`, `it`, and `expect` from `vitest`.
3. If your test requires environment variables, use `vi.stubEnv` or `stubDefaultEnvVars` from `tests/testShared.js`.

**Example Test (`src/example.test.ts`):**
```typescript
import { describe, it, expect } from 'vitest';

describe('Math Utilities', () => {
  it('should add numbers correctly', () => {
    expect(1 + 1).toBe(2);
  });
});
```

To run only your new test:
```bash
npx vitest src/example.test.ts --run
```

## Additional Development Information

### Code Style
- **Linter**: ESLint is used for code quality. Run it with `npm run lint`.
- **Formatter**: Prettier is used for code formatting.
- **Imports**: Sorted using `eslint-plugin-simple-import-sort`.

### Architecture Notes
- **MCP SDK**: The project heavily utilizes `@modelcontextprotocol/sdk`.
- **Transports**: Supports both `stdio` and `http` transports.
- **Logging**: Uses a custom logging system that can mask sensitive information. Set `DISABLE_LOG_MASKING=true` only for debugging non-production environments.
- **Testing Mode**: Setting `TABLEAU_MCP_TEST=true` in the environment silences stderr logging.

### Debugging
- Use the **MCP Inspector** to test the server locally:
  ```bash
  npm run inspect
  ```
- Build and Inspect in one go:
  ```bash
  npm run build:inspect
  ```
