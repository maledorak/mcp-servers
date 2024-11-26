# Time MCP Server

A Model Context Protocol server that provides time and timezone conversion capabilities. This server enables LLMs to get current time information and perform timezone conversions using IANA timezone names, with automatic system timezone detection.

### Available Tools

- `get_current_time` - Get current time in a specific timezone or system timezone.
  - Optional argument: `timezone` (string): IANA timezone name (e.g., 'America/New_York', 'Europe/London')
  - If timezone is not provided, returns time in system timezone

- `convert_time` - Convert time between timezones.
  - Required arguments:
    - `source_timezone` (string): Source IANA timezone name
    - `time` (string): Time in 24-hour format (HH:MM)
    - `target_timezone` (string): Target IANA timezone name

## Installation

### Using uv (recommended)

When using [`uv`](https://docs.astral.sh/uv/) no specific installation is needed. We will
use [`uvx`](https://docs.astral.sh/uv/guides/tools/) to directly run *mcp-server-time*.

### Using PIP

Alternatively you can install `mcp-server-time` via pip:

```bash
pip install mcp-server-time
```

After installation, you can run it as a script using:

```bash
python -m mcp_server_time
```

## Configuration

### Configure for Claude.app

Add to your Claude settings:

<details>
<summary>Using uvx</summary>

```json
"mcpServers": {
  "time": {
    "command": "uvx",
    "args": ["mcp-server-time"]
  }
}
```
</details>

<details>
<summary>Using pip installation</summary>

```json
"mcpServers": {
  "time": {
    "command": "python",
    "args": ["-m", "mcp_server_time"]
  }
}
```
</details>

### Configure for Zed

Add to your Zed settings.json:

<details>
<summary>Using uvx</summary>

```json
"context_servers": [
  "mcp-server-time": {
    "command": "uvx",
    "args": ["mcp-server-time"]
  }
],
```
</details>

<details>
<summary>Using pip installation</summary>

```json
"context_servers": {
  "mcp-server-time": {
    "command": "python",
    "args": ["-m", "mcp_server_time"]
  }
},
```
</details>

### Customization - System Timezone

By default, the server automatically detects your system's timezone. You can override this by adding the argument `--local-timezone` to the `args` list in the configuration.

Example:
```json
{
  "command": "python",
  "args": ["-m", "mcp_server_time", "--local-timezone=America/New_York"]
}
```

## Example Interactions

1. Get current time (using system timezone):
```json
{
  "name": "get_current_time",
  "arguments": {}
}
```
Response:
```json
{
  "timezone": "Europe/London",
  "time": "14:30 BST",
  "date": "2024-11-25",
  "full_datetime": "2024-11-25 14:30:00 BST",
  "is_dst": true
}
```

2. Get current time in specific timezone:
```json
{
  "name": "get_current_time",
  "arguments": {
    "timezone": "America/New_York"
  }
}
```
Response:
```json
{
  "timezone": "America/New_York",
  "time": "09:30 EDT",
  "date": "2024-11-25",
  "full_datetime": "2024-11-25 09:30:00 EDT",
  "is_dst": true
}
```

3. Convert time between timezones:
```json
{
  "name": "convert_time",
  "arguments": {
    "source_timezone": "America/New_York",
    "time": "16:30",
    "target_timezone": "Asia/Tokyo"
  }
}
```
Response:
```json
{
  "source": {
    "timezone": "America/New_York",
    "time": "16:30 EDT",
    "date": "2024-11-25"
  },
  "target": {
    "timezone": "Asia/Tokyo",
    "time": "05:30 JST",
    "date": "2024-11-26"
  },
  "time_difference": "+13.0h",
  "date_changed": true,
  "day_relation": "next day"
}
```

## Tips for Using IANA Timezone Names

Common timezone formats:
- North America: `America/New_York`, `America/Los_Angeles`, `America/Chicago`
- Europe: `Europe/London`, `Europe/Paris`, `Europe/Berlin`
- Asia: `Asia/Tokyo`, `Asia/Shanghai`, `Asia/Dubai`
- Pacific: `Pacific/Auckland`, `Pacific/Honolulu`
- Australia: `Australia/Sydney`, `Australia/Melbourne`

The server will automatically detect and use your system timezone if no specific timezone is provided.

## Debugging

You can use the MCP inspector to debug the server. For uvx installations:

```bash
npx @modelcontextprotocol/inspector uvx mcp-server-time
```

Or if you've installed the package in a specific directory or are developing on it:

```bash
cd path/to/servers/src/time
npx @modelcontextprotocol/inspector uv run mcp-server-time
```

## Examples of Questions for Claude

1. "What time is it now?" (will use system timezone)
2. "What time is it in Tokyo?"
3. "When it's 4 PM in New York, what time is it in London?"
4. "Convert 9:30 AM Tokyo time to New York time"

## Contributing

We encourage contributions to help expand and improve mcp-server-time. Whether you want to add new time-related tools, enhance existing functionality, or improve documentation, your input is valuable.

For examples of other MCP servers and implementation patterns, see:
https://github.com/modelcontextprotocol/servers

Pull requests are welcome! Feel free to contribute new ideas, bug fixes, or enhancements to make mcp-server-time even more powerful and useful.

## License

mcp-server-time is licensed under the MIT License. This means you are free to use, modify, and distribute the software, subject to the terms and conditions of the MIT License. For more details, please see the LICENSE file in the project repository.