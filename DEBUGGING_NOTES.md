# Debugging Notes - Fusion 360 MCP Server

## Issue: Server Failed to Connect in Claude Desktop

### Symptoms
- Claude Desktop Settings showed "Failed" status for fusion360 server
- Red error badge with validation errors
- Server not appearing in available MCP tools

### Root Causes Found (from logs)

#### 1. Missing JSON-RPC 2.0 Protocol Fields
**Error**: `Expected "jsonrpc": "2.0" in response`

**Problem**: All responses were missing the required `"jsonrpc": "2.0"` field and proper `"id"` field.

**Fix**: Modified `handle_request()` to wrap all responses with:
```python
response = {
    "jsonrpc": "2.0",
    "id": request_id,
    "result": {...}  # or "error": {...}
}
```

**Commit**: `78af821` - "Fix JSON-RPC 2.0 protocol compliance for Claude Desktop"

#### 2. Incorrect Response Structure
**Error**: Response had `{"result": {"result": {...}}}` (double nesting)

**Problem**: Individual handler methods were returning `{"result": {...}}` and then the main handler was wrapping it again.

**Fix**: Changed handler methods to return just the result data, not wrapped:
- `_handle_initialize()` returns `{protocolVersion, serverInfo, capabilities}`
- `_handle_list_tools()` returns `{tools: [...]}`
- `_handle_call_tool()` returns `{content: [...]}` or `{error: {...}}`

**Commit**: `78af821` - Same commit as above

#### 3. Notifications Not Handled Properly
**Error**: `Expected number, received null` for `"id"` field

**Problem**: Claude Desktop sends notifications like `{"method": "notifications/initialized"}` which have NO `"id"` field (per JSON-RPC 2.0 spec). The server was responding with `"id": null` which failed validation.

**Fix**: 
1. Check if `request.get("id")` is `None`
2. If it's a notification (no ID), return `None` from handler
3. Skip writing response for notifications in `run_mcp_server()`

**Commit**: `18389b2` - "Fix notification handling - don't respond to notifications without ID"

### Log File Analysis

Location: `%APPDATA%\Claude\logs\mcp-server-fusion360.log`

Key error patterns found:
```javascript
{
  "code": "invalid_type",
  "expected": "number",
  "received": "null",
  "path": ["id"],
  "message": "Expected number, received null"
}
```

This error appeared when server sent:
```json
{"jsonrpc": "2.0", "id": null, "error": {...}}
```

For the notification: `{"method":"notifications/initialized","jsonrpc":"2.0"}`

### Testing Verification

Tested all three scenarios:

1. **Initialize (with ID)**:
```bash
{"jsonrpc":"2.0","id":0,"method":"initialize",...}
→ {"jsonrpc":"2.0","id":0,"result":{...}} ✅
```

2. **Tools List (with ID)**:
```bash
{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}
→ {"jsonrpc":"2.0","id":1,"result":{"tools":[...]}} ✅
```

3. **Notification (no ID)**:
```bash
{"jsonrpc":"2.0","method":"notifications/initialized"}
→ (no response) ✅
```

## Solution Summary

1. ✅ Added JSON-RPC 2.0 wrapper with proper `"jsonrpc"` and `"id"` fields
2. ✅ Fixed response structure (removed double nesting)
3. ✅ Handled notifications properly (no response for requests without ID)
4. ✅ Updated to MCP protocol version 2024-11-05
5. ✅ All commits pushed to GitHub

## How to Restart Claude Desktop

1. Right-click Claude icon in system tray
2. Click "Quit" (not just close window)
3. Wait 5 seconds
4. Reopen Claude Desktop
5. Check Settings → Developer → Local MCP servers
6. fusion360 should now show as connected ✅

## Verification

After restart, ask Claude:
```
"What MCP servers do you have access to?"
```

Should include: **fusion360** 🎉
