<!-- @guidance:
Generate or update the content as follows.  
**Important:** If any section or content already exists, update it with the latest and most accurate information instead of duplicating or skipping it.
# Page Structure: 
1. Header
   - Project Title: need to use from pom.xml  
2. Overview
   - Review the relatad web page: `https://machai.machanism.org/bindex-core/index.html` (selector: #bodyColumn).
   - Review the relatad web page: `https://machai.machanism.org/mcp-server-maven-plugin/index.html` (selector: #bodyColumn).
   - Full description of purpose and benefits.
3. Download Page
   - url: `https://sourceforge.net/projects/machanism/files/machai/bindex-mcp-server/releases/`.
3. Usage
   - Jar file can be used as a STDIO or HTTP MCP server, `how to use` information: `https://machai.machanism.org/machai-mcp-server/index.html#CLI`. 
4. Key Features
   - Bulleted list highlighting the primary capabilities of the project.
5. Getting Started
   - Prerequisites: List of required software and services.
   - Basic Usage: Example command to run the plugin.
   - Typical Workflow: Step-by-step outline of how to use the project artifacts.
6. Resources
   - List of relevant links (platform, GitHub, Maven).
-->

# Bindex MCP Server

## Overview

Bindex MCP Server packages Machai Bindex capabilities as a standalone Java Model Context Protocol (MCP) server. It combines Bindex Core, which provides metadata retrieval, registration, and natural-language library recommendation services, with the Machai MCP Server runtime, which exposes Machai-compatible tools to MCP clients over STDIO or HTTP.

Bindex Core helps Ghostwriter, Maven integrations, build automation, and AI-assisted development agents work with accurate, structured library metadata instead of relying only on free-form model knowledge. A Bindex record can describe an artifact, its purpose, examples, installation guidance, and classification metadata. Registration workflows normalize and enrich Bindex JSON with classification and embedding data, while recommendation workflows classify user requirements, create embeddings, run semantic search, and return version-aware candidates that meet the configured relevance threshold.

The related MCP Server Maven Plugin launches Machai MCP servers directly from Maven projects. It starts HTTP-based stateless or streamable MCP servers that are aligned with the current Maven project directory, metadata, runtime parameters, and optional Maven `settings.xml` credentials. Bindex MCP Server is the packaged server artifact for Bindex-enabled tools, making it useful for local development, demonstrations, integration testing, build automation, project-aware AI workflows, and repeatable library discovery or registration tasks.

## Download Page

Packaged releases are available from the Bindex MCP Server releases page:

- <https://sourceforge.net/projects/machanism/files/machai/bindex-mcp-server/releases/>

## Usage

The Bindex MCP Server JAR can be used as either a STDIO MCP server for local process-based clients or an HTTP MCP server for network-accessible clients. The assembled artifact uses `org.machanism.machai.mcp.server.McpServer` as its main class and includes the Bindex Core and Machai MCP Server dependencies.

Build the executable JAR:

```bash
mvn clean install
```

Run the server in default STDIO mode for a project workspace:

```bash
java -jar target/bindex-mcp-server-1.2.0.jar --projectDir /path/to/project
```

Run the server in stateless HTTP mode:

```bash
java -jar target/bindex-mcp-server-1.2.0.jar --port 8080 --projectDir /path/to/project
```

Run the server in streamable HTTP mode:

```bash
java -jar target/bindex-mcp-server-1.2.0.jar --port 8080 --session --projectDir /path/to/project
```

Useful CLI options include `--name`, `--version`, `--config`, `--projectDir`, `--port`, and `--session`. If no port is provided, the server starts in STDIO mode. If a port is provided, the server starts in HTTP mode; adding `--session` selects the streamable HTTP variant.

For Maven-native project workflows, the MCP Server Maven Plugin can start a Machai MCP server from a Maven project. Start a stateless HTTP MCP server on port 8080:

```bash
mvn org.machanism.machai:mcp-server-maven-plugin:stateless -Dmcp.port=8080
```

Start a streamable HTTP MCP server on port 8080:

```bash
mvn org.machanism.machai:mcp-server-maven-plugin:streamable -Dmcp.port=8080
```

After the server is running, connect an MCP-compatible client to the selected STDIO process or HTTP endpoint and use the exposed Bindex operations to retrieve known metadata, register a `bindex.json` file or JSON record, and recommend libraries that match a natural-language requirement.

## Key Features

- Standalone executable MCP server packaging for Bindex-enabled Machai tools.
- STDIO transport for local MCP client integrations.
- HTTP transport with stateless and streamable session-capable modes.
- AI-callable Bindex retrieval, file-based registration, JSON-based registration, and library recommendation capabilities.
- Semantic library discovery from natural-language prompts using GenAI classification and embedding generation.
- Structured Bindex metadata support for artifact purpose, examples, installation guidance, and classification data.
- MongoDB-backed Bindex repository integration with document persistence and vector-search support.
- Repository abstraction for lookup, save, delete, and semantic find operations provided by Bindex Core.
- Classification normalization, configurable relevance thresholds, and version-aware recommendation result consolidation.
- Project workspace context propagation so MCP tools can operate against the intended project directory.
- Executable JAR assembly with dependencies for straightforward Java-based deployment.
- Compatibility with Maven-driven MCP workflows through the MCP Server Maven Plugin.

## Getting Started

### Prerequisites

- Java 17 or later.
- Apache Maven, if building from source or using Maven-launched workflows.
- A packaged Bindex MCP Server release or a local build of this project.
- An MCP-compatible client or integration that can communicate over STDIO or HTTP.
- Network access to any configured AI provider services used by Machai tools.
- Configuration for the GenAI model used by Ghostwriter/Machai workflows, such as `gw.model` or `pick.model`.
- Configuration for an embedding model, such as `embedding.model`, when using Bindex registration or recommendation workflows.
- MongoDB/Bindex repository connectivity for lookup, registration, persistence, and semantic search operations.
- Optional Maven `settings.xml` server credentials when credentials should be resolved from Maven settings in Maven-plugin workflows.

### Basic Usage

Build the server artifact:

```bash
mvn clean install
```

Run the assembled server artifact in STDIO mode:

```bash
java -jar target/bindex-mcp-server-1.2.0.jar --projectDir /path/to/project
```

Run the assembled server artifact in HTTP mode:

```bash
java -jar target/bindex-mcp-server-1.2.0.jar --port 8080 --projectDir /path/to/project
```

Or start a Maven-launched stateless MCP server for the current project:

```bash
mvn org.machanism.machai:mcp-server-maven-plugin:stateless -Dmcp.port=8080
```

### Typical Workflow

1. Download a packaged release or build this project with Maven to create the executable Bindex MCP Server artifact.
2. Configure the required Machai, model, embedding, and Bindex repository settings for your environment.
3. Choose a transport mode: STDIO for local client process integration, stateless HTTP for request/response integrations, or streamable HTTP for compatible MCP clients.
4. Start the server directly with Java, or start a project-aware HTTP MCP server by using the MCP Server Maven Plugin.
5. Connect an MCP-compatible client to the running STDIO process or HTTP endpoint.
6. Use Bindex tools to retrieve metadata for known Bindex IDs, register project metadata, or request library recommendations from natural-language requirements.
7. Review recommended libraries and retrieve detailed Bindex metadata when needed.
8. Apply selected libraries, installation guidance, and examples in your project workflow.
9. Stop the server from the client if a stop tool is available, or terminate the Java or Maven process manually.

## Resources

- Machai Platform: <https://machai.machanism.org/>
- Bindex MCP Server releases: <https://github.com/machanism-org/bindex-mcp-server/releases>
- Bindex MCP Server GitHub repository: <https://github.com/machanism-org/bindex-mcp-server.git>
- Bindex Core documentation: <https://machai.machanism.org/bindex-core/index.html>
- Machai MCP Server documentation: <https://machai.machanism.org/machai-mcp-server/index.html>
- MCP Server Maven Plugin documentation: <https://machai.machanism.org/mcp-server-maven-plugin/index.html>
- Bindex Core on Maven Central: <https://central.sonatype.com/artifact/org.machanism.machai/bindex-core>
- Machai MCP Server on Maven Central: <https://central.sonatype.com/artifact/org.machanism.machai/machai-mcp-server>
- MCP Server Maven Plugin on Maven Central: <https://central.sonatype.com/artifact/org.machanism.machai/mcp-server-maven-plugin>
- Machai GitHub repository: <https://github.com/machanism-org/machai>
- Machai issue tracker: <https://github.com/machanism-org/machai/issues>
