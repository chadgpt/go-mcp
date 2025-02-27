# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.3.0] - 2025-01-05

This release introduces significant improvements in API consistency and modernizes the codebase by adopting Go 1.23's iterator pattern. Key changes include restructured parameter naming conventions, simplified package organization, enhanced transport interfaces, and improved SSE handling through the integration of `go-sse` library. The adoption of iterators for handling sessions, messages, and streaming operations provides a more efficient and safer alternative to channels for sequential data processing.

### Added

- `Session` interface for handling session on `ServerTransport` interfaces.
- `ProgressReporter` function type for reporting progress in server primitive operations
- `ResourceSubscriptionHandler` interface for handling resource subscriptions.

### Changed

- Refactored parameter naming convention for `Client` request methods to improve consistency between method names and their parameters. Previously, parameter names like `PromptsListParams` and `PromptsGetParams` used noun-verb style while methods used verb-noun style. Now, parameter names follow the same verb-noun pattern as their corresponding methods (e.g., `ListPromptsParams` and `GetPromptParams`).
- Refactored the result name of the request calls, either in `Client` or `Server` interfaces. This is done to improve consistency between method names and their results. For example, `ListPrompts` now returns `ListPromptsResult` instead of `PromptList`.
- Use structured parameter types (such as `ListPromptsParams` or `GetPromptParams`) in `Client` method signatures when making server requests, rather than using individual parameters. For example, instead of passing separate `cursor` and `progressToken` parameters to `ListPrompts`, or `name` and `arguments` to `GetPrompt`, use a dedicated parameter struct.
- Utilize `go-sse` to handle `SSEClient` by @tmaxmax.
- Utilize `go-sse`'s `Session` to sent the event messages from `SSEServer`.
- Moved `mcp` package from `pkg/mcp` to root folder and `pkg/servers` package to `servers` package to simplify import paths. The `pkg` directory added unnecessary nesting and noise in import paths (e.g., from `github.com/MegaGrindStone/go-mcp/pkg/mcp` to `github.com/MegaGrindStone/go-mcp`, and from `github.com/MegaGrindStone/go-mcp/pkg/servers` to `github.com/MegaGrindStone/go-mcp/servers`).
- Split `Transport` interface into `ServerTransport` and `ClientTransport` interfaces. `ClientTransport` doesn't need session-based messages as `Client` would just use one `Session` in its lifecycle.
- Use iterator pattern for sessions in `ServerTransport` and messages in `ClientTransport`.
- Use iterator pattern for streaming logs in `LogHandler`.
- Use iterator pattern for all the updaters. 
- Use `io.ReadCloser` and `io.WriteCloser` for `StdIO` transport.
- Adjust `everything` server for current interfaces.
- Adjust `filesystem` server for current interfaces.

### Removed

- `pkg/mcp` package (moved to root for cleaner imports).
- `pkg/servers` package (moved to `servers` for cleaner imports).
- `Transport` interface (replaced by `ServerTransport` and `ClientTransport`)
- `ProgressReporter` interface (replaced with function type for simpler progress reporting)
- `ResourceSubscribedUpdater` interface (replaced with `ResourceSubscriptionHandler`)

## [0.2.0] - 2024-12-27

This release introduces a major architectural refactor centered around the new `Transport` interface and `Client` struct. The changes simplify the client architecture by moving from multi-session to single-session management, while providing a more flexible foundation for MCP implementations. The introduction of specialized `ServerTransport` and `ClientTransport` interfaces has enabled unified transport implementations and more consistent server implementations. Notable consolidations include merging separate StdIO implementations into a unified struct and relocating request functions from transport-specific clients to the main `Client` struct.

### Added

- `Transport` interface for client-server communication, with specialized `ServerTransport` and `ClientTransport` interfaces.
- `Client` struct for direct interaction with MCP servers.
- `Info` and `ServerRequirement` structs for client configuration management.
- `Serve` function for starting MCP servers.
- `StdIO` struct implementing both `ServerTransport` and `ClientTransport` interfaces.
- `RequestClientFunc` type alias for server-to-client function calls.

### Changed

- Simplified client architecture to support single-session management instead of multi-sessions.
- Relocated request functions from transport-specific clients to the main `Client` struct.
- Implemented unified test suite using the `Transport` interface.
- Enhanced `SSEServer` to implement the `ServerTransport` interface.
- Updated `SSEClient` to implement the `ClientTransport` interface.
- Everything server now utilizes the `Transport` interface.
- Filesystem server now utilizes the `Transport` interface.

### Removed

- Replaced `Client` interface with the new `Client` struct.
- Consolidated `StdIOServer` struct into the unified `StdIO` struct.
- Consolidated `StdIOClient` struct into the unified `StdIO` struct.

## [0.1.0] - 2024-12-24

### Added

- Server interfaces to implement MCP. 
- Client interfaces to interact with MCP servers.
- StdIO transport implementation.
- SSE transport implementation.
- Filesystem server implementation and example.
- Everything server implementation and example.
