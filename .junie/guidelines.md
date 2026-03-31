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
- `TRANSPORT`: Communication protocol: `stdio` (default) or `http`.
- `AUTH`: Authentication method: `pat` (default), `oauth`, `uat`, `direct-trust`.

#### Authentication Methods
Tableau MCP supports several authentication methods, configured via the `AUTH` environment variable:
- `pat` (Default): Personal Access Token. Requires `PAT_NAME` and `PAT_VALUE`.
- `oauth`: OAuth 2.0. Requires additional OAUTH_* variables.
- `uat`: User Access Token. Requires `UAT_TENANT_ID`, `UAT_ISSUER`, `UAT_PRIVATE_KEY` (or `UAT_PRIVATE_KEY_PATH`), `UAT_KEY_ID`.
- `direct-trust`: Connected App Direct Trust. Requires `CONNECTED_APP_CLIENT_ID`, `CONNECTED_APP_SECRET_ID`, `CONNECTED_APP_SECRET_VALUE`.

#### Overridable Environment Variables
These variables can be used to customize the server's behavior and the available tools:

**Tool Filtering:**
- `INCLUDE_TOOLS`: Comma-separated list of tool names or tool groups to include.
- `EXCLUDE_TOOLS`: Comma-separated list of tool names or tool groups to exclude.
  *(Note: Cannot use both INCLUDE and EXCLUDE simultaneously)*

**Bounded Context (Data Filtering):**
- `INCLUDE_PROJECT_IDS`: Comma-separated list of Tableau project LUIDs.
- `INCLUDE_DATASOURCE_IDS`: Comma-separated list of Tableau datasource LUIDs.
- `INCLUDE_WORKBOOK_IDS`: Comma-separated list of Tableau workbook LUIDs.
- `INCLUDE_TAGS`: Comma-separated list of tags.
  *(When set, tools will only return resources that match these criteria.)*

**Result Limits:**
- `MAX_RESULT_LIMIT`: Global limit for the number of items returned by list tools.
- `MAX_RESULT_LIMITS`: Tool-specific limits (e.g., `list-views:50,workbook:20`).

**Other Overrides:**
- `DISABLE_QUERY_DATASOURCE_VALIDATION_REQUESTS`: Set to `true` to skip validation before querying.
- `DISABLE_METADATA_API_REQUESTS`: Set to `true` to disable Metadata API calls.

### Tool Names and Groups
**Tool Groups:**
- `datasource`: `list-datasources`, `get-datasource-metadata`, `query-datasource`
- `workbook`: `list-workbooks`, `get-workbook`
- `view`: `list-views`, `get-view-data`, `get-view-image`
- `pulse`: `list-all-pulse-metric-definitions`, `list-pulse-metric-definitions-from-definition-ids`, `list-pulse-metrics-from-metric-definition-id`, `list-pulse-metrics-from-metric-ids`, `list-pulse-metric-subscriptions`, `generate-pulse-metric-value-insight-bundle`, `generate-pulse-insight-brief`
- `content-exploration`: `search-content`

#### Advanced Filtering (`list-views`)
The `list-views` tool supports advanced filtering using `field:operator:value` syntax.
- **Operators:** `eq`, `in`, `gt`, `gte`, `lt`, `lte`.
- **Example:** `name:eq:Overview,projectName:eq:Finance`.

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
