<!-- @guidance:
Generate or update the content as follows.  
**Important:** If any section or content already exists, update it with the latest and most accurate information instead of duplicating or skipping it.
# Page Structure: 
1. Header
   - Project Title: need to use from pom.xml
   - Maven Central Badge ([![Maven Central](https://img.shields.io/maven-central/v/[groupId]/[artifactId].svg)](https://central.sonatype.com/artifact/[groupId]/[artifactId])
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

[![Maven Central](https://img.shields.io/maven-central/v/org.machanism.machai/bindex-mcp-server.svg)](https://central.sonatype.com/artifact/org.machanism.machai/bindex-mcp-server)
[![bindex](https://img.shields.io/badge/bindex-blue.svg)](https://raw.githubusercontent.com/machanism-org/bindex-mcp-server/refs/heads/main/bindex.json)

## Overview

Bindex MCP Server packages **Bindex Core** with the Machai MCP Server runtime as a Java 17 application. It exposes Bindex metadata retrieval, registration, and natural-language library recommendation as Model Context Protocol (MCP) tools, allowing compatible AI clients and automation to discover reusable libraries and maintain their metadata through a standard interface.

A Bindex record describes a library's Maven coordinates, version, purpose, classification, integrations, dependencies, examples, and configuration. The Bindex workflow validates and registers these records, generates classification embeddings, and searches MongoDB-backed metadata semantically. Recommendations can be narrowed by language and architectural layer, filtered by similarity score, and consolidated to useful library versions. Clients can retrieve a complete descriptor or a GraphQL-style field selection, register JSON records or files, and request schema or generation guidance.

The executable uses the Machai runtime to discover and publish the Bindex tools. With no port it communicates through STDIO; with `--port` it exposes HTTP MCP at `/mcp`. Bindex Core owns the metadata and recommendation workflow, while the runtime provides transport, request routing, and tool publication.

![Bindex MCP Server component diagram](./images/c4-diagram.png)

## Supported AI providers

Bindex registration and semantic recommendations use the GenAI Client provider abstraction supplied by the Machai dependencies. Configure the provider/model identifier (for example, `gw.model` or `embedding.model`) and the credentials appropriate to the selected provider. The configured embedding model must produce vectors compatible with the Bindex repository's vector index.

- **OpenAI** — Use identifiers such as `OpenAI:gpt-4o-mini` for generation or `OpenAI:text-embedding-3-small` for embeddings. Set `OPENAI_API_KEY`; optionally set `OPENAI_BASE_URL` for an OpenAI-compatible endpoint.
- **Anthropic** — Use an identifier such as `Anthropic:claude-3-5-sonnet`. Set `ANTHROPIC_API_KEY`; `ANTHROPIC_BASE_URL` is optional. It supports instructions, function tools, web search where supported, and MCP server forwarding.
- **CodeMie** — Use a CodeMie model identifier, such as `CodeMie:gpt-4o-mini`, `CodeMie:claude-3-5-sonnet`, or `CodeMie:text-embedding-005`. Set `GENAI_USERNAME` and `GENAI_PASSWORD`; optionally set `AUTH_URL`. CodeMie obtains a bearer token and delegates `gpt-`, `gemini-`, and supported embedding models to its OpenAI-compatible endpoint, and `claude-` models to Anthropic.
- **Tools** — Use `Tools:yaml` to invoke registered local Java tool callbacks from a YAML tool-call descriptor. It requires no external AI service or credentials.
- **None** — Use `None:disabled` for a silent no-op provider or `None:log` for INFO-level lifecycle diagnostics. It requires no credentials and is useful for safe defaults and tests.

### Common configuration parameters

| Parameter | Description | Default value |
| --- | --- | --- |
| Provider/model identifier | Provider and model, normally `Provider:Model`; for Bindex, `pick.model` overrides `gw.model` for request classification. | Required; `pick.model` falls back to `gw.model` |
| `gw.model` | GenAI model used by the Bindex picker when `pick.model` is not configured. | `CodeMie:gpt-5.6-terra-2026-07-09` |
| `embedding.model` | Provider/model used to embed classifications for semantic search. | Host/application-defined |
| `OPENAI_API_KEY` | API key for OpenAI or OpenAI-compatible requests. | Required for OpenAI-compatible providers |
| `OPENAI_BASE_URL` | Base URL override for an OpenAI-compatible API. | OpenAI SDK default |
| `ANTHROPIC_API_KEY` | API key or authorization token for Anthropic requests. | Required for Anthropic |
| `ANTHROPIC_BASE_URL` | Base URL override for Anthropic-compatible APIs. | Anthropic SDK default |
| `GENAI_USERNAME`, `GENAI_PASSWORD` | Credentials used by CodeMie password-grant or client-credentials authentication. | Provider-specific |
| `AUTH_URL` | CodeMie OpenID Connect token endpoint override. | CodeMie default endpoint |
| `GENAI_TIMEOUT` | Request timeout in seconds; `0` or unset uses SDK behavior. | `0` |
| `MAX_OUTPUT_TOKENS` | Maximum tokens generated by a model. | `18000` |
| `MAX_TOOL_CALLS` | OpenAI Responses API tool-call limit; `0` leaves it unset. | `0` |
| `WebSearchTool.type` | Enables supported provider-specific web search when present. | Not set |
| `MCP.url`, `MCP.name`, `MCP.authorization`, `MCP.description` | Connection details for a provider-visible MCP server; additional servers use numbered groups such as `MCP_1.url`. | Not set |
| `BINDEX_REPO_URL` | MongoDB connection URI for Bindex metadata and vector search. | `mongodb+srv://cluster0.hivfnpr.mongodb.net/?appName=Cluster0` |
| `BINDEX_USER`, `BINDEX_PASSWORD` | MongoDB authentication credentials. | Not set |
| `score` / `pick.score` | Minimum semantic similarity for returned recommendations. | `0.85` for the AI tool; `0.86` in built-in pick/assembly acts |
| `search_limits` / `vectorSearchLimits` | Maximum vector-search candidates or recommendations. | `25` |

## Resources

- [Machai platform](https://machai.machanism.org/)
- [Bindex MCP Server source repository](https://github.com/machanism-org/bindex-mcp-server)
- [Machai source repository](https://github.com/machanism-org/machai)
- [Bindex MCP Server on Maven Central](https://central.sonatype.com/artifact/org.machanism.machai/bindex-mcp-server)
- [Bindex Core documentation](https://machai.machanism.org/bindex-core/index.html)
- [Machai MCP Server documentation](https://machai.machanism.org/machai-mcp-server/index.html)
- [GenAI Client documentation](https://machai.machanism.org/genai-client/index.html)
- [Bindex metadata schema](https://raw.githubusercontent.com/machanism-org/machai/refs/heads/main/bindex-core/src/main/resources/schema/bindex-schema-v2.json)
