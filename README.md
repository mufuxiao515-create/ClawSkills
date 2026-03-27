# UE DataTable Editor

Edit Unreal Engine AI Skills DataTable configuration through JSON files — search, modify, validate, and import back to the engine.

## Features

- **Search** — Find skills by ID, name, keyword, or Part range
- **Modify** — Edit any field with automatic `NSLOCTEXT()` wrapping for localization fields
- **Validate** — Type checking, required field verification, ID uniqueness check
- **Import** — Generate UE Editor Python commands to import JSON back into DataTable assets

## Use Cases

- Batch editing AI skill descriptions, names, or parameters
- Searching and reviewing skill configurations across multiple DataTable partitions
- Safely modifying `.uasset` DataTable files through their JSON representation
- Importing validated changes back into Unreal Engine

## Workflow

1. **Search** → Locate target skill(s) in the JSON data
2. **Modify** → Edit fields with validation and automatic backup
3. **Import** → Run the generated command in UE Editor to update the DataTable

## Trigger Phrases

- "修改技能" / "查找技能" / "技能表"
- "DataTable" / "DT表" / "AI Skills"
- "导入UE" / "技能数据"

## Supported DataTable Partitions

| Part | ID Range | Asset |
|------|----------|-------|
| Part0 | 200000–229999 | `DT_AI_Skills_Part0` |
| Part1 | 230000–239999 | `DT_AI_Skills_Part1` |
| Part2 | 240000–249999 | `DT_AI_Skills_Part2` |

## Requirements

- Python 3.x (for search and modify scripts)
- Unreal Engine Editor with Python plugin enabled (for import)
- JSON source file (`Json_AI_Skills.json`) in UTF-16 LE encoding

## License

MIT-0
