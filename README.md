# cripple-ai-retardation-with-es-lint-rules

This project provides a set of custom ESLint rules designed to enforce safer and more maintainable patterns for accessing environment variables in your Node.js projects. By preventing raw `process.env` access and ensuring all environment variables are explicitly declared and validated, these rules help to catch potential issues during development, thereby preventing runtime errors and making your AI (or any application) less prone to "retarded" mistakes due to misconfigured environments.

## Rules

| Rule File                                                       | Description                                                                                                |
| --------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| `raw-process-env-usage.eslint-rules.mjs`                        | Disallows direct member access on `process.env` (e.g., `process.env.DATABASE_URL`), enforcing computed access. |
| `require-env-keys-usage-in-env-validation.eslint-rules.mjs` | Ensures that every environment variable enum member has a corresponding validation rule in the validation file.      |

## Configuration

The main ESLint configuration is in `eslint.config.mjs`. It uses the flat config format. Here's an example of how it should be configured:

```javascript
import globals from "globals";
import pluginJs from "@eslint/js";
import tseslint from "typescript-eslint";
import noRawProcessEnv from "./eslint-rules/raw-process-env-usage.eslint-rules.mjs";
import requireEnvKeysUsage from "./eslint-rules/require-env-keys-usage-in-env-validation.eslint-rules.mjs";

/** @type {import('eslint').Linter.Config[]} */
export default [
  {
    plugins: {
      "no-raw-process-env": {
        rules: {
          "no-raw-process-env": noRawProcessEnv
        }
      },
      "require-env-keys-usage": {
        rules: {
          "require-env-keys-usage": requireEnvKeysUsage
        }
      }
    }
  },
  {
    files: ["**/*.{js,mjs,cjs,ts}"],
    languageOptions: {
      globals: {
        ...globals.browser,
        ...globals.node, // Add Node.js globals
      },
      parserOptions: {
        ecmaVersion: 2020,
        sourceType: 'module'
      }
    },
    rules: {
      "no-raw-process-env/no-raw-process-env": "error",
      "require-env-keys-usage/require-env-keys-usage": "error",
      // NOTE: Uncomment the following line if you want to enforce no-console
      // "no-console": "warn",
      "no-unused-vars": ["warn", { "argsIgnorePattern": "^_" }],
      "prefer-const": "error",
      "semi": ["error", "always"],
      "no-duplicate-imports": "error"
    }
  },
  pluginJs.configs.recommended,
  ...tseslint.configs.recommended,
  {
    // TypeScript-specific rules
    files: ["**/*.ts"],
    rules: {
      "@typescript-eslint/explicit-function-return-type": "off", // Keep it off as per existing config
      "@typescript-eslint/no-explicit-any": "warn",
      "@typescript-eslint/no-unused-vars": ["warn", { "argsIgnorePattern": "^_" }],
      // "@typescript-eslint/type-annotation-spacing": "error"
    }
  }
];
```

## Some Ideas

Here are some ideas for additional ESLint rules to further enhance code quality and maintainability:

| Rule Name                       | Description                                                                                                                                                              |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `no-large-functions`            | Disallow functions exceeding a certain line count or complexity, promoting smaller, more focused functions.                                                              |
| `enforce-consistent-imports`    | Enforce a consistent ordering and grouping of import statements (e.g., external imports first, then internal, then relative).                                            |
| `no-unnecessary-await`          | Identify and disallow `await` keywords that are not strictly necessary (e.g., awaiting a non-Promise value or a Promise that is immediately returned).                      |
| `prefer-template-literals`      | Enforce the use of template literals over string concatenation where appropriate.                                                                                        |
| `no-magic-numbers`              | Disallow the use of magic numbers (undeclared numeric constants) outside of a dedicated constants file or specific contexts.                                            |
