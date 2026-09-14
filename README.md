<!-- @guidance: >>> ${guidances}/readme-content.md 
# Additional Infomation

## Overview 
- Review the relatad web page: `https://machai.machanism.org/bindex-core/index.html` (selector: #bodyColumn).
- Review the relatad web page: `https://machai.machanism.org/mcp-server-maven-plugin/index.html` (selector: #bodyColumn).
- Full description of purpose and benefits.
   
## Download Page
- url: `https://sourceforge.net/projects/machanism/files/machai/bindex-mcp-server/releases/`.

## Usage
- Jar file can be used as a STDIO or HTTP MCP server, `how to use` information: `https://machai.machanism.org/machai-mcp-server/index.html#CLI`. 

## Key Features
- Bulleted list highlighting the primary capabilities of the project.

## Getting Started
- Prerequisites: List of required software and services.
- Basic Usage: Example command to run the plugin.
- Typical Workflow: Step-by-step outline of how to use the project artifacts.
-->

# Bindex MCP Server

[![Maven Central](https://img.shields.io/maven-central/v/org.machanism.machai/bindex-mcp-server.svg)](https://central.sonatype.com/artifact/org.machanism.machai/bindex-mcp-server) [![bindex](https://img.shields.io/badge/bindex-blue.svg)](https://raw.githubusercontent.com/machanism-org/bindex-mcp-server/refs/heads/main/bindex.json)

## Cloning and Getting Started

To clone and set up this project locally, follow these steps:

1. **Clone the repository:**
   ```bash
   git clone https://github.com/machanism-org/bindex-mcp-server.git
   cd bindex-mcp-server
   ```
2. **Build the project using Maven:**
   ```bash
   mvn clean install
   ```

## Overview

Bindex MCP Server is a Java 17 distribution that combines [Bindex Core](https://machai.machanism.org/bindex-core/index.html) with the [Machai MCP Server](https://machai.machanism.org/machai-mcp-server/index.html) runtime. It makes Bindex metadata retrieval, registration, schema access, and natural-language library recommendations available to MCP-compatible AI clients and automation over a standard protocol.

Bindex records describe a library's coordinates, version, purpose, classification, integrations, dependencies, examples, and configuration. The server helps teams validate and register this metadata, retrieve complete or GraphQL-style filtered descriptors, and discover suitable reusable libraries using semantic search. Recommendations combine AI-generated request classifications and embeddings with language, architectural-layer, score, and version-aware filtering against a MongoDB-backed repository. This reduces duplicate implementation effort and makes reusable capabilities easier to find and adopt.

## Project Structure

The server is organized around an MCP runtime, a Bindex tool layer, and the Bindex metadata and recommendation workflow. MCP-compatible clients invoke the runtime through STDIO or HTTP; the runtime dispatches requests to Bindex tools. Those tools read metadata supplied from a project workspace and coordinate retrieval, registration, and recommendation operations. The workflow persists and vector-searches metadata in MongoDB and requests classifications and embeddings from the configured GenAI service.

![Bindex MCP Server component diagram](src/site/resources/images/c4-diagram.png)

## Download

Download packaged releases from [SourceForge](https://sourceforge.net/projects/machanism/files/machai/bindex-mcp-server/releases/). The assembled executable includes the MCP runtime and Bindex Core so it can publish Bindex tools when launched.

## Key Features

- Publishes Bindex operations to MCP-compatible clients through STDIO or HTTP transports.
- Retrieves Bindex metadata by coordinates or URL, with optional GraphQL-style field filtering.
- Registers schema-compliant Bindex records from objects, project-relative JSON files, or remote URLs.
- Recommends libraries from natural-language requests using configurable GenAI and embedding providers.
- Searches MongoDB-backed metadata with similarity thresholds, classification filters, and version selection.
- Provides Bindex schema and metadata-generation guidance to support consistent library descriptors.
- Uses a single runnable Java artifact that can be extended with compatible tool libraries on the runtime classpath.

## AI Provider Configuration

Bindex registration and semantic recommendations use the GenAI provider abstraction supplied by Machai. Configure the generation or classification model with `gw.model` (or `pick.model`) and configure a compatible embedding model with `embedding.model`. The embedding model must produce vectors compatible with the MongoDB repository's vector index.

- **OpenAI** — Use model identifiers such as `OpenAI:gpt-4o-mini` or `OpenAI:text-embedding-3-small` and set `OPENAI_API_KEY`. `OPENAI_BASE_URL` optionally selects an OpenAI-compatible endpoint.
- **Anthropic** — Use an identifier such as `Anthropic:claude-3-5-sonnet` and set `ANTHROPIC_API_KEY`. `ANTHROPIC_BASE_URL` is optional.
- **CodeMie** — Use identifiers such as `CodeMie:gpt-4o-mini`, `CodeMie:claude-3-5-sonnet`, or `CodeMie:text-embedding-005`. Set `GENAI_USERNAME` and `GENAI_PASSWORD`; `AUTH_URL` optionally overrides the token endpoint.
- **Tools and None** — `Tools:yaml` invokes registered local Java tools without an external AI service. `None:disabled` and `None:log` provide no-op and diagnostic providers for safe defaults or tests.

| Parameter | Purpose | Default |
| --- | --- | --- |
| `gw.model` | GenAI model used by the picker unless `pick.model` is set. | Host/application-defined |
| `pick.model` | Model used specifically to classify library-selection requests. | Falls back to `gw.model` |
| `embedding.model` | Provider/model used to encode classifications for semantic search. | Host/application-defined |
| `OPENAI_API_KEY` / `OPENAI_BASE_URL` | Credentials and optional endpoint override for OpenAI-compatible providers. | API key required for OpenAI |
| `ANTHROPIC_API_KEY` / `ANTHROPIC_BASE_URL` | Credentials and optional endpoint override for Anthropic. | API key required for Anthropic |
| `GENAI_USERNAME`, `GENAI_PASSWORD`, `AUTH_URL` | CodeMie credentials and optional OpenID Connect token endpoint. | Provider-specific |
| `GENAI_TIMEOUT` | Request timeout in seconds; `0` or no value uses SDK behavior. | `0` |
| `MAX_OUTPUT_TOKENS` / `MAX_TOOL_CALLS` | Generation-output and OpenAI Responses API tool-call limits. | `18000` / `0` |

## Getting Started

### Prerequisites

- Java 17 or newer.
- Apache Maven when building from source.
- The downloadable release JAR, or a local build of this project.
- MongoDB connectivity and `BINDEX_REPO_URL` (plus `BINDEX_USER` and `BINDEX_PASSWORD` when required) for Bindex registration and search.
- A configured GenAI model and embedding model, along with credentials required by the selected provider, for semantic recommendations.
- An MCP-compatible client and, for HTTP mode, an available TCP port.

### Basic Usage

The assembled release JAR can run as either a STDIO or HTTP MCP server. Start the executable JAR directly; the following starts a stateless HTTP MCP endpoint at `http://localhost:45000/mcp`:

```bash
java -jar bindex-mcp-server.jar --port 45000
```

Omit `--port` to use STDIO, which is suitable for local desktop-client integrations:

```bash
java -jar bindex-mcp-server.jar
```

For streamable HTTP transport, add `--session`. You can also use `--projectDir <path>` to provide a project context and `--config <path>` to load server properties. See the [Machai MCP Server CLI guide](https://machai.machanism.org/machai-mcp-server/index.html#CLI) for all options and client configuration examples.

### Typical Workflow

1. Download a release or build the artifact with Maven.
2. Configure the MongoDB repository, GenAI provider, embedding model, and any required credentials.
3. Start the artifact in STDIO mode for a local client, or with `--port` for HTTP access at `/mcp`.
4. Connect an MCP client and verify that the Bindex tools are available.
5. Use the tools to retrieve or register Bindex metadata, or submit a natural-language request to receive library recommendations.
6. Use the resulting descriptors and recommendations to select dependencies and guide implementation.

## Resources

- [Bindex MCP Server releases](https://sourceforge.net/projects/machanism/files/machai/bindex-mcp-server/releases/)
- [Bindex MCP Server source repository](https://github.com/machanism-org/bindex-mcp-server)
- [Bindex MCP Server on Maven Central](https://central.sonatype.com/artifact/org.machanism.machai/bindex-mcp-server)
- [Bindex Core documentation](https://machai.machanism.org/bindex-core/index.html)
- [Machai MCP Server documentation](https://machai.machanism.org/machai-mcp-server/index.html)
- [Machai MCP Server Maven Plugin documentation](https://machai.machanism.org/mcp-server-maven-plugin/index.html)
- [Machai platform](https://machai.machanism.org/)
