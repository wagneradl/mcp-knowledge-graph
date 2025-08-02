# Wagner's Enhancements for MCP Knowledge Graph

This branch contains specific enhancements developed by Wagner for improved Claude Desktop integration and memory management.

## 🎯 Key Enhancements

### 1. **Hybrid Format Support**
- **Auto-detection** of JSON structured vs JSONL formats
- **Seamless conversion** between formats
- **Backward compatibility** with existing memory files

### 2. **MCP Protocol Fixes**
- **Missing handlers** for `resources/list` and `prompts/list` (prevents EPIPE errors)
- **Proper capabilities** declaration for resources and prompts
- **Enhanced error handling** for malformed data

### 3. **Environment Variable Support**
- **MEMORY_FILE_PATH** environment variable support
- **Flexible path configuration** for different deployment scenarios
- **Maintains backward compatibility** with command-line args

### 4. **Data Compatibility**
- **Automatic removal** of 'type' field from entities/relations for MCP compatibility
- **Robust parsing** with error recovery for malformed lines
- **Improved data validation** and sanitization

## 🚀 Usage

### Environment Variable Configuration
```bash
export MEMORY_FILE_PATH="/path/to/your/memory.json"
node index-wagner-patch.ts
```

### Command Line Configuration
```bash
node index-wagner-patch.ts --memory-path /path/to/your/memory.json
```

### Claude Desktop Configuration
```json
{
  "mcpServers": {
    "memory-graph": {
      "transport": "stdio",
      "command": "node",
      "args": ["/path/to/mcp-knowledge-graph/index-wagner-patch.ts"],
      "env": {
        "MEMORY_FILE_PATH": "/path/to/your/current_memory.json"
      }
    }
  }
}
```

## 🔧 Technical Details

### Hybrid Format Detection
The enhanced loader automatically detects file format:

```typescript
// JSON structured format
{
  "entities": [...],
  "relations": [...]
}

// JSONL format (line-by-line)
{"type": "entity", "name": "...", ...}
{"type": "relation", "from": "...", ...}
```

### MCP Protocol Compliance
Added missing handlers to prevent crashes:

```typescript
server.setRequestHandler(ListResourcesRequestSchema, async () => {
  return { resources: [] };
});

server.setRequestHandler(ListPromptsRequestSchema, async () => {
  return { prompts: [] };
});
```

### Data Sanitization
Automatic removal of incompatible fields:

```typescript
if (item.type === "entity") {
  const { type, ...entityData } = item;
  graph.entities.push(entityData as Entity);
}
```

## 🎉 Benefits

1. **Zero EPIPE Errors** - Proper MCP protocol implementation
2. **Format Flexibility** - Works with any memory file format
3. **Environment Agnostic** - Supports various deployment scenarios
4. **Data Integrity** - Robust parsing and error recovery
5. **Claude Desktop Ready** - Optimized for Claude Desktop integration

## 🔄 Migration from Original

To use Wagner's enhanced version:

1. Replace `index.ts` with `index-wagner-patch.ts` in your configuration
2. Set `MEMORY_FILE_PATH` environment variable if needed
3. Existing memory files work without modification
4. All original functionality preserved with enhancements

## 📋 Changelog

- **v1.0.1-wagner**: Initial Wagner patch release
  - Hybrid format support
  - MCP protocol fixes
  - Environment variable support
  - Enhanced error handling
  - Data compatibility improvements