<!-- @guidance:
**Important:** If any section or content already exists, update it with the latest and most accurate information instead of duplicating or skipping it.
1. **Project Title: Local Bindex Repository**  
   - Provide the project name and a brief description based on folder content summary.
2. **Installation Instructions:**  
   - Describe prerequisites and build tools.
   - Describe how to install all required application to build and run docker compose.
3. **Usage:**  
   - Explain how to build and run the bindex repository on the local machine.
   - Environment variables for the bindex-core:
   ```bash
	export BINDEX_REPO_URL=mongodb://localhost:27017/?appName=machanism
    export BINDEX_PASSWORD=pass
	export BINDEX_USER=user
	export GENAI_PASSWORD=...
	export GENAI_USERNAME=...
	export gw_model=CodeMie:gpt-5.4-2026-03-05
   ```
**Formatting Requirements:**
- Use Markdown syntax for headings, lists, code blocks, and links.
- Ensure clarity and conciseness in each section.
- Organize the README for easy navigation and readability.
-->

# Local Bindex Repository

Local Bindex Repository is a Docker-based local repository for Bindex data. It runs MongoDB Atlas Local with a startup script that creates the `machanism` database, configures the `bindex` collection schema validator, and creates Atlas Search indexes, including a vector search index for `classification_embedding`.

## Installation Instructions

### Prerequisites

Install the following before running the local repository:

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) or Docker Engine with Docker Compose v2 support.
- A terminal or shell capable of running `docker compose` commands.
- Network access to pull the `mongodb/mongodb-atlas-local` container image.
- The bindex-core application or service that will connect to this local MongoDB instance.

No separate build tool is required for this Docker setup. Docker Compose pulls and runs the required MongoDB Atlas Local image automatically.

### Install required applications

1. Install Docker Desktop or Docker Engine for your operating system.
2. Start Docker and confirm it is available:

   ```bash
   docker --version
   docker compose version
   ```

3. From the project root, confirm the Docker Compose files are available:

   ```bash
   ls docker/mongodb/docker-compose.yml
   ls docker/mongodb/init-db.js
   ```

## Usage

### Build and run the local Bindex repository

From the project root, start MongoDB Atlas Local with Docker Compose:

```bash
cd docker/mongodb
docker compose up -d
```

The first startup pulls the `mongodb/mongodb-atlas-local` image if it is not already available locally. The `init-db.js` script runs during initialization and prepares the local repository by:

- Creating or selecting the `machanism` database.
- Creating or updating the `bindex` collection validator.
- Creating the `id` Atlas Search index.
- Creating the `vector_index` vector search index for `classification_embedding` with `700` dimensions and cosine similarity.

The service exposes MongoDB on local port `27017` and stores data in persistent Docker volumes:

- `mongodb_config`
- `mongodb_data`
- `mongodb_mongot`

### Configure bindex-core

Set the environment variables required by bindex-core before starting the application:

```bash
export BINDEX_REPO_URL=mongodb://localhost:27017/?appName=machanism
export BINDEX_PASSWORD=pass
export BINDEX_USER=user
export GENAI_PASSWORD=...
export GENAI_USERNAME=...
export gw_model=CodeMie:gpt-5.4-2026-03-05
```

The default MongoDB credentials are defined in `docker/mongodb/docker-compose.yml`:

- Username: `user`
- Password: `pass`
- Database: `machanism`
- Collection: `bindex`

### Verify the repository is running

From `docker/mongodb`, check the container status:

```bash
docker compose ps
```

View MongoDB logs:

```bash
docker compose logs -f mongodb
```

### Stop the local repository

From `docker/mongodb`, stop the service while keeping persistent data volumes:

```bash
docker compose down
```

To stop the service and delete all persistent MongoDB data, run:

```bash
docker compose down -v
```

Use `docker compose down -v` only when you want to remove local data and reinitialize the repository from scratch on the next startup.
