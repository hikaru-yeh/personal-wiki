# Using Gemini CLI For Large Codebase Analysis

When analyzing large codebases or many files that may exceed the current context limit, use the Gemini CLI with its large context window. Use `gemini -p` to leverage Gemini's context capacity.

## File And Directory Inclusion Syntax

Use `@` syntax to include files and directories in Gemini prompts. Paths are relative to the directory where the `gemini` command is run.

### Examples

Single file:

```bash
gemini -p "@src/main.py Explain this file's purpose and structure"
```

Multiple files:

```bash
gemini -p "@package.json @src/index.js Analyze the dependencies used in the code"
```

Entire directory:

```bash
gemini -p "@src/ Summarize the architecture of this codebase"
```

Multiple directories:

```bash
gemini -p "@src/ @tests/ Analyze test coverage for the source code"
```

Current directory and subdirectories:

```bash
gemini -p "@./ Give me an overview of this entire project"
```

Or use `--all_files`:

```bash
gemini --all_files -p "Analyze the project structure and dependencies"
```

## Implementation Verification Examples

Check if a feature is implemented:

```bash
gemini -p "@src/ @lib/ Has dark mode been implemented in this codebase? Show me the relevant files and functions"
```

Verify authentication:

```bash
gemini -p "@src/ @middleware/ Is JWT authentication implemented? List all auth-related endpoints and middleware"
```

Check for specific patterns:

```bash
gemini -p "@src/ Are there any React hooks that handle WebSocket connections? List them with file paths"
```

Verify error handling:

```bash
gemini -p "@src/ @api/ Is proper error handling implemented for all API endpoints? Show examples of try-catch blocks"
```

Check for rate limiting:

```bash
gemini -p "@backend/ @middleware/ Is rate limiting implemented for the API? Show the implementation details"
```

Verify caching:

```bash
gemini -p "@src/ @lib/ @services/ Is Redis caching implemented? List all cache-related functions and their usage"
```

Check security measures:

```bash
gemini -p "@src/ @api/ Are SQL injection protections implemented? Show how user inputs are sanitized"
```

Verify test coverage:

```bash
gemini -p "@src/payment/ @tests/ Is the payment processing module fully tested? List all test cases"
```

## When To Use Gemini CLI

Use `gemini -p` when:

- analyzing entire codebases or large directories
- comparing multiple large files
- understanding project-wide patterns or architecture
- the current context window is insufficient
- files total more than 100 KB
- verifying whether specific features, patterns, or security measures are implemented
- checking for coding patterns across an entire codebase

## Important Notes

- Paths in `@` syntax are relative to the current working directory when invoking Gemini.
- The CLI includes file contents directly in the context.
- No `--yolo` flag is needed for read-only analysis.
- Gemini's context window can handle entire codebases that would overflow smaller context windows.
- When checking implementations, be specific about what you are looking for to get accurate results.
