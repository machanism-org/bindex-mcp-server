<!-- @guidance:
Generate or update the content as follows.  
**Important:** If any section or content already exists, update it with the latest and most accurate information instead of duplicating or skipping it.
# Page Structure: 
1. Header
   - Project Title: need to use from pom.xml
   - Bindex Badge [![bindex](https://img.shields.io/badge/bindex-blue.svg)](https://raw.githubusercontent.com/machanism-org/[artifactId]/refs/heads/main/bindex.json)
# Overview
   - Full description the project based on package-info.java files in source folder..
   - Use the project structure diagram by the path: `./images/c4-diagram.png` (`src/site/puml/c4-diagram.puml`).
# Supported AI providers
   - Describe all supported AP providers with configurations.
   - Table of common configuration parameters, their descriptions, and default values.
# Resources
   - List of relevant links (platform, GitHub, Maven).
-->

# Bindex MCP Server

[![bindex](https://img.shields.io/badge/bindex-blue.svg)](https://raw.githubusercontent.com/machanism-org/bindex-mcp-server/refs/heads/main/bindex.json)

## Overview

Bindex MCP Server is a Java 17 distribution that combines [Bindex Core](https://machai.machanism.org/bindex-core/index.html) with the [Machai MCP Server](https://machai.machanism.org/machai-mcp-server/index.html) runtime. It exposes Bindex metadata retrieval, registration, schema access, and natural-language library recommendation as Model Context Protocol (MCP) tools for compatible AI clients and automation.

Bindex records describe libraries through coordinates, versions, purpose, classification, integrations, dependencies, examples, and configuration. The server can retrieve complete or GraphQL-style filtered descriptors, validate and register records supplied as JSON objects, project-relative files, or remote URLs, and recommend reusable libraries from natural-language requirements. Recommendations combine AI-generated classifications and embeddings with language, architectural-layer, similarity-score, and version-aware filtering over a MongoDB-backed repository.

The Machai MCP runtime discovers and publishes the Bindex tools and handles request routing. With no port configured, the executable communicates over STDIO; when started with `--port`, it serves MCP over HTTP at `/mcp`, with optional streamable session support. Bindex Core performs metadata normalization, persistence, retrieval, and semantic discovery, while configured GenAI and embedding services provide classification and vector generation.

![Bindex MCP Server component diagram](./images/c4-diagram.png)

## Supported AI providers

Bindex registration and semantic recommendations use Machai's GenAI provider abstraction. Model values use the `Provider:Model` form. Configure the generation or classification model through `gw.model` or `pick.model`, and select a compatible embedding model through `embedding.model`; embedding dimensions must match the MongoDB vector index.

- **OpenAI** — Use identifiers such as `OpenAI:gpt-4o-mini` for generation and `OpenAI:text-embedding-3-small` for embeddings. Set `OPENAI_API_KEY`; optionally set `OPENAI_BASE_URL` for an OpenAI-compatible endpoint.
- **Anthropic** — Use an identifier such as `Anthropic:claude-3-5-sonnet`. Set `ANTHROPIC_API_KEY`; optionally set `ANTHROPIC_BASE_URL`. Supported models can use instructions, function tools, web search, and MCP server forwarding.
- **CodeMie** — Use identifiers such as `CodeMie:gpt-4o-mini`, `CodeMie:claude-3-5-sonnet`, or `CodeMie:text-embedding-005`. Set `GENAI_USERNAME` and `GENAI_PASSWORD`; optionally override the OpenID Connect token endpoint with `AUTH_URL`. CodeMie routes supported GPT, Gemini, Claude, and embedding models to the appropriate compatible API.
- **Tools** — Use `Tools:yaml` to invoke registered local Java tool callbacks from a YAML tool-call descriptor. No external AI credentials are required.
- **None** — Use `None:disabled` for a silent no-op provider or `None:log` for lifecycle diagnostics. These modes require no credentials and are suitable for safe defaults and tests.

### Common configuration parameters

| Parameter | Description | Default value |
| --- | --- | --- |
| `gw.model` | GenAI model used for classification when `pick.model` is not set. | `CodeMie:gpt-5.6-terra-2026-07-09` |
| `pick.model` | Optional model specifically used to classify library-selection requests. | Falls back to `gw.model` |
| `embedding.model` | Provider and model used to create vectors for semantic search. | Host/application-defined |
| `OPENAI_API_KEY` | Credential for OpenAI or an OpenAI-compatible service. | Required when using OpenAI |
| `OPENAI_BASE_URL` | Base URL override for an OpenAI-compatible API. | OpenAI SDK default |
| `ANTHROPIC_API_KEY` | Credential for Anthropic requests. | Required when using Anthropic |
| `ANTHROPIC_BASE_URL` | Base URL override for an Anthropic-compatible API. | Anthropic SDK default |
| `GENAI_USERNAME`, `GENAI_PASSWORD` | Credentials used to authenticate with CodeMie. | Provider-specific |
| `AUTH_URL` | CodeMie OpenID Connect token endpoint override. | CodeMie default endpoint |
| `GENAI_TIMEOUT` | AI request timeout in seconds; `0` or an unset value uses SDK behavior. | `0` |
| `MAX_OUTPUT_TOKENS` | Maximum number of output tokens generated by a model. | `18000` |
| `MAX_TOOL_CALLS` | OpenAI Responses API tool-call limit; `0` leaves the limit unset. | `0` |
| `WebSearchTool.type` | Enables provider-specific web search when configured and supported. | Not set |
| `MCP.url`, `MCP.name`, `MCP.authorization`, `MCP.description` | Defines a provider-visible MCP server; numbered groups such as `MCP_1.url` add servers. | Not set |
| `BINDEX_REPO_URL` | MongoDB URI for Bindex metadata storage and vector search. | `mongodb+srv://cluster0.hivfnpr.mongodb.net/?appName=Cluster0` |
| `BINDEX_USER`, `BINDEX_PASSWORD` | Optional MongoDB authentication credentials. | Not set |
| `score` / `pick.score` | Minimum semantic similarity accepted for recommendations. | `0.85` for the AI tool; `0.86` in built-in pick/assembly acts |
| `search_limits` / `vectorSearchLimits` | Maximum vector-search candidates or recommendations. | `25` |

## Resources

- [Machai platform](https://machai.machanism.org/)
- [Bindex MCP Server source repository](https://github.com/machanism-org/bindex-mcp-server)
- [Machai source repository](https://github.com/machanism-org/machai)
- [Bindex MCP Server on Maven Central](https://central.sonatype.com/artifact/org.machanism.machai/bindex-mcp-server)
- [Bindex Core documentation](https://machai.machanism.org/bindex-core/index.html)
- [Machai MCP Server documentation](https://machai.machanism.org/machai-mcp-server/index.html)
- [GenAI Client documentation](https://machai.machanism.org/genai-client/index.html)
