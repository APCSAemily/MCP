# MCP Chat

MCP Chat is a command-line interface application that enables interactive chat with AI models through the Anthropic API. It demonstrates how the Model Context Protocol (MCP) acts as a middle layer between an AI application and external documents or tools.

The demo supports document retrieval, command-based prompts, and extensible tool integrations through an MCP server.

## Why MCP Chat?

An AI model cannot automatically access local files, email, databases, or business applications. An MCP server provides a controlled bridge to those external systems.

In this demo, the MCP server exposes documents to the CLI. The client requests a document, receives its contents, and includes that information in the model's context before generating a response.

```text
User → MCP Chat CLI → MCP client → MCP server → documents or tools
                                      ↓
                              context returned to the AI
```

MCP makes this integration reusable and standardized. The same server pattern can later be used with approved filesystem folders, Gmail, databases, internal APIs, or other MCP-compatible applications.

## Impacts

### Controlled access to external context

The model receives only the information that the MCP server exposes and the user requests. Access can be limited to specific folders, documents, accounts, or operations.

### Extensible AI applications

The chat application does not need custom logic for every external system. New capabilities can be added through MCP tools, resources, and prompts.

### Separation of responsibilities

- The CLI handles the user experience.
- The MCP client manages the connection.
- The MCP server defines available tools and resources.
- External systems hold the source data or perform actions.
- The Anthropic model uses the returned results as context.

### Safer integrations

An MCP server can enforce authentication, authorization, input validation, and operation limits before an AI request reaches a filesystem, email account, or business system.

## Use Cases

- Ask questions about a selected set of project documents.
- Summarize or reformat a document through an MCP prompt.
- Connect the server to approved local folders for controlled file retrieval.
- Search Gmail through the Gmail API and return relevant messages as context.
- Query internal databases or company APIs without exposing unrestricted access.
- Add tools for calendars, issue trackers, repositories, or collaboration platforms.
- Build an internal AI assistant that uses organization-specific knowledge and workflows.

For actions that change external data—such as editing files, sending email, or deleting records—the MCP server should require appropriate permissions and, where needed, user confirmation.

## How This Demo Works

The sample server stores document content in a Python dictionary:

```python
docs = {
    "deposition.md": "This deposition covers the testimony of Angela Smith, P.E.",
}
```

The server exposes that data through MCP:

- `read_doc_contents` reads a document through a tool.
- `edit_document` edits document content in memory.
- `docs://documents` lists available document IDs as a resource.
- `docs://documents/{doc_id}` returns a document's contents as a resource.
- `format` provides instructions for rewriting a document in Markdown.

Because the sample data is stored in memory, edits are not written to real files and are lost when the server exits. A production implementation would add file, database, or API integrations explicitly.

## Prerequisites

- Python 3.9+
- Anthropic API key

## Setup

### Step 1: Configure environment variables

Create or edit the `.env` file in the project root:

```ini
ANTHROPIC_API_KEY=""
```

### Step 2: Install dependencies

#### Option 1: Setup with uv (recommended)

Install uv if needed:

```shell
pip install uv
```

Create and activate a virtual environment:

```shell
uv venv
source .venv/bin/activate
# Windows PowerShell:
# .venv\Scripts\Activate.ps1
```

Install dependencies and run the project:

```shell
uv pip install -e .
uv run main.py
```

#### Option 2: Setup without uv

Create and activate a virtual environment:

```shell
python -m venv .venv
source .venv/bin/activate
# Windows PowerShell:
# .venv\Scripts\Activate.ps1
```

Install dependencies and run the project:

```shell
pip install anthropic python-dotenv prompt-toolkit "mcp[cli]==1.8.0"
python main.py
```

## Usage

### Basic interaction

Type a message and press Enter to chat with the model.

### Document retrieval

Use `@` followed by a document ID to include document content in a query:

```text
> Tell me about @deposition.md
```

### Commands

Use `/` to execute commands defined by the MCP server:

```text
> /summarize deposition.md
```

Commands support tab completion.

## Development

To add sample documents, edit the `docs` dictionary in `mcp_server.py`.

To extend the MCP implementation, add tools, resources, or prompts in `mcp_server.py`, and implement the corresponding client methods in `mcp_client.py` as needed.

This project currently has no linting or type-checking configuration.
