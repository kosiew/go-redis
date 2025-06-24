# Agent Instructions

This repository is the official Redis client library for Go. Use the following guidelines when
modifying the codebase.

## Formatting

- Use tabs for indentation in Go source files.
- Run `make fmt` before committing to apply `gofumpt` and `goimports`.
- Format Markdown and YAML files with `prettier --write` (see `.prettierrc.yml`).

## Dependency Management

- If dependencies change, run `make go_mod_tidy` to update all `go.mod` files.

## Testing

- Tests rely on Docker containers. Start the environment with `make docker.start` and stop it with
  `make docker.stop` when done.
- Run `make test.ci` to execute the test suite. Use `make test` if you need the containers started
  and stopped automatically.

## Commit Messages

- Follow the style `type(scope): summary`, e.g. `feat(client): add new option` or
  `fix(pubsub): handle nil channel`.
- Keep messages concise and in the imperative mood.

## Pull Requests

- Include unit tests for new features and bug fixes.
- Update documentation when behaviour changes.
