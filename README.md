I'll organize this README to make it more structured and professional for GitHub. Here's the improved version:

```markdown
# IDA Pro MCP

A simple [MCP Server](https://modelcontextprotocol.io/introduction) that enables AI-assisted reverse engineering in IDA Pro through the Model Context Protocol.

![Demo](https://github.com/user-attachments/assets/6ebeaa92-a9db-43fa-b756-eececce2aca0)

> **Note**: The binaries and prompt used in the demo video are available in the [mcp-reversing-dataset](https://github.com/mrexodia/mcp-reversing-dataset) repository.

## Features

### Core Functions
- **Connection & Metadata**
  - `check_connection()`: Verify IDA plugin connectivity
  - `get_metadata()`: Retrieve current IDB metadata
  - `get_current_address()`: Get user's current selection address
  - `get_current_function()`: Get user's current selected function

- **Function Operations**
  - `get_function_by_name(name)`: Lookup function by name
  - `get_function_by_address(address)`: Lookup function by address
  - `list_functions(offset, count)`: Paginated function listing
  - `list_functions_filter(offset, count, filter)`: Filtered function search (supports regex/substring)
  - `decompile_function(address)`: Decompile function at address
  - `disassemble_function(start_address)`: Get assembly with comments
  - `get_function_cfg(start_address)`: Generate control-flow graph edges

- **Data Operations**
  - `convert_number(text, size)`: Convert between number representations
  - `list_globals(offset, count)`: Paginated global variables listing
  - `list_globals_filter(offset, count, filter)`: Filtered globals search
  - `list_strings(offset, count)`: Paginated strings listing
  - `list_strings_filter(offset, count, filter)`: Filtered strings search
  - `list_local_types()`: List all Local types
  - `list_exports(offset, count)`: List binary exports
  - `list_segments()`: List segments with permissions

- **Analysis Operations**
  - `get_xrefs_to(address)`: Cross-references to address
  - `get_xrefs_from(address)`: Cross-references from address
  - `get_xrefs_to_field(struct_name, field_name)`: References to struct fields
  - `get_entry_points()`: All database entry points
  - `find_bytes(pattern, start, end, max_results)`: Search for byte patterns (supports wildcards)

- **Modification Operations**
  - `set_comment(address, comment)`: Set disassembly/pseudocode comments
  - `rename_local_variable(function_address, old_name, new_name)`: Rename function variables
  - `rename_global_variable(old_name, new_name)`: Rename global variables
  - `set_global_variable_type(variable_name, new_type)`: Set global variable types
  - `rename_function(function_address, new_name)`: Rename functions
  - `set_function_prototype(function_address, prototype)`: Set function prototypes
  - `declare_c_type(c_declaration)`: Create/update types from C declarations
  - `set_local_variable_type(function_address, variable_name, new_type)`: Set local variable types
  - `patch_bytes(address, hex_bytes)`: Patch raw bytes
  - `create_function(address)`: Create function at address
  - `delete_function(address)`: Delete function containing address
  - `jump_to(address)`: Navigate UI to address
  - `save_database(path?)`: Save database (optional path)

### Debugging Functions (Require `--unsafe` flag)
- `dbg_get_registers()`: Get register values (debugging only)
- `dbg_get_call_stack()`: Get current call stack
- `dbg_list_breakpoints()`: List all breakpoints
- `dbg_start_process()`: Start debugger
- `dbg_exit_process()`: Exit debugger
- `dbg_continue_process()`: Continue execution
- `dbg_run_to(address)`: Run to specified address
- `dbg_set_breakpoint(address)`: Set breakpoint
- `dbg_delete_breakpoint(address)`: Remove breakpoint
- `dbg_enable_breakpoint(address, enable)`: Enable/disable breakpoint

## Prerequisites

- **Python 3.11+** (Use `idapyswitch` to switch Python versions)
- **IDA Pro 8.3+** (9 recommended) - *IDA Free not supported*
- **MCP Client** (choose one):
  - [Cline](https://cline.bot)
  - [Roo Code](https://roocode.com)
  - [Claude](https://claude.ai/download)
  - [Cursor](https://cursor.com)
  - [VSCode Agent Mode](https://github.blog/news-insights/product-news/github-copilot-agent-mode-activated/)
  - [Windsurf](https://windsurf.com)
  - [Other MCP Clients](https://modelcontextprotocol.io/clients#example-clients)

## Installation

1. Install the latest package:
```bash
pip uninstall ida-pro-mcp
pip install https://github.com/mrexodia/ida-pro-mcp/archive/refs/heads/main.zip
```

2. Configure MCP servers and install IDA Plugin:
```bash
ida-pro-mcp --install
```

> **Important**: Completely restart IDA, Visual Studio Code, and/or Claude (quit from tray icon) for changes to take effect.

![Installation](https://github.com/user-attachments/assets/65ed3373-a187-4dd5-a807-425dca1d8ee9)

*Note: Load a binary in IDA before the plugin menu appears.*

## Prompt Engineering

LLMs are prone to hallucinations, especially with number conversions in reverse engineering. Use this prompt template:

```
Your task is to analyze a crackme in IDA Pro. You can use the MCP tools to retrieve information. In general use the following strategy:
- Inspect the decompilation and add comments with your findings
- Rename variables to more sensible names
- Change the variable and argument types if necessary (especially pointer and array types)
- Change function names to be more descriptive
- If more details are necessary, disassemble the function and add comments with your findings
- NEVER convert number bases yourself. Use the convert_number MCP tool if needed!
- Do not attempt brute forcing, derive any solutions purely from the disassembly and simple python scripts
- Create a report.md with your findings and steps taken at the end
- When you find a solution, prompt to user for feedback with the password you found
```

*Share your improvements by opening an issue!*

## Tips for Enhancing LLM Accuracy

- Use `convert_number` tool for mathematical operations
- Consider [math-mcp](https://github.com/EthanHenrickson/math-mcp) for complex calculations
- Pre-process obfuscated code before LLM analysis:
  - Remove string encryption
  - Handle import hashing
  - De-flatten control flow
  - Decrypt code sections
  - Counter anti-decompilation techniques
- Use Lumina or FLIRT to resolve library code and C++ STL

## SSE Transport & Headless MCP

### Standard SSE Server
```bash
uv run ida-pro-mcp --transport http://127.0.0.1:8744/sse
```

### Headless SSE Server (requires [idalib](https://docs.hex-rays.com/user-guide/idalib))
```bash
uv run idalib-mcp --host 127.0.0.1 --port 8745 path/to/executable
```

*Note: Headless feature contributed by [Willi Ballenthin](https://github.com/williballenthin).*

## Manual Installation

<details>
<summary>Advanced installation instructions</summary>

### Manual MCP Server Setup (Cline/Roo Code)

1. Install [uv](https://github.com/astral-sh/uv):
   - Windows: `pip install uv`
   - Linux/Mac: `curl -LsSf https://astral.sh/uv/install.sh | sh`

2. Clone this repository (e.g., `C:\MCP\ida-pro-mcp`)

3. Configure MCP servers in client settings (`cline_mcp_settings.json`):

```json
{
  "mcpServers": {
    "github.com/mrexodia/ida-pro-mcp": {
      "command": "uv",
      "args": [
        "--directory",
        "c:\\MCP\\ida-pro-mcp",
        "run",
        "server.py",
        "--install-plugin"
      ],
      "timeout": 1800,
      "disabled": false
    }
  }
}
```

4. Test connection with:
```xml
<use_mcp_tool>
<server_name>github.com/mrexodia/ida-pro-mcp</server_name>
<tool_name>check_connection</tool_name>
<arguments></arguments>
</use_mcp_tool>
```

### Manual IDA Plugin Installation

1. Copy `src/ida_pro_mcp/mcp-plugin.py` to `%appdata%\Hex-Rays\IDA Pro\plugins`
2. Open IDB and click `Edit -> Plugins -> MCP` to start server

</details>

## Comparison with Other MCP Servers

This server was created for:
1. Fully automated installation
2. Minimal boilerplate for new functionality
3. Learning experience

Other IDA Pro MCP servers:
- [taida957789/ida-mcp-server-plugin](https://github.com/taida957789/ida-mcp-server-plugin) (SSE only, requires IDAPython dependencies)
- [fdrechsler/mcp-server-idapro](https://github.com/fdrechsler/mcp-server-idapro) (TypeScript, more boilerplate)
- [MxIris-Reverse-Engineering/ida-mcp-server](https://github.com/MxIris-Reverse-Engineering/ida-mcp-server) (custom socket protocol)

*PRs welcome to add other servers!*

## Development

### Adding New Features
Simply add a new `@jsonrpc` function to [`mcp-plugin.py`](https://github.com/mrexodia/ida-pro-mcp/blob/main/src/ida_pro_mcp/mcp-plugin.py) - no additional boilerplate required!

![Development Demo](https://github.com/user-attachments/assets/951de823-88ea-4235-adcb-9257e316ae64)

### Testing
```bash
# Test MCP server
uv run mcp dev src/ida_pro_mcp/server.py

# Install for testing (enable symbolic links first)
uv run ida-pro-mcp --install
```

### Changelog Generation
```bash
git log --first-parent --no-merges 1.2.0..main "--pretty=- %s"
```
```

This organized version features:

1. **Clear section hierarchy** with proper headings and subheadings
2. **Grouped functionality** into logical categories
3. **Improved formatting** with consistent spacing and emphasis
4. **Better visual flow** with organized lists and details sections
5. **Enhanced readability** through consistent formatting
6. **Professional structure** suitable for GitHub documentation
7. **Mobile-friendly** formatting with proper line lengths

The content maintains all the original information while making it much more accessible and professional-looking.
