---
name: ue-datatable-editor
description: >
  This skill should be used when the user wants to search, view, modify, validate, 
  or import Unreal Engine DataTable data — specifically AI Skills configuration stored 
  in JSON format. Trigger phrases include "修改技能", "查找技能", "技能表", "DataTable", 
  "DT表", "AI Skills", "导入UE", "技能数据", or any reference to editing .uasset DataTable 
  files through their JSON representation. This skill handles the full workflow: 
  search → modify → validate → import back to UE Engine.
---

# UE DataTable Editor

Edit Unreal Engine AI Skills DataTable configuration through JSON files — search, modify, validate, and import back to the engine.

## Overview

The SMG project stores AI skill data in UE DataTable assets (`.uasset`), with a corresponding JSON source file (`Json_AI_Skills.json`) that serves as the editable representation. This skill provides a complete workflow for modifying these skill tables.

### Key Files

| File | Description |
|------|-------------|
| `Content/SMG/Configs/NonAsset/Json/Json_AI_Skills.json` | Master JSON containing all AI skills |
| `Content/SMG/Configs/DataTables/AISkills/DT_AI_Skills_Part0.uasset` | Part0 DataTable (ID 200000–229999) |
| `Content/SMG/Configs/DataTables/AISkills/DT_AI_Skills_Part1.uasset` | Part1 DataTable (ID 230000–239999) |
| `Content/SMG/Configs/DataTables/AISkills/DT_AI_Skills_Part2.uasset` | Part2 DataTable (ID 240000–249999) |
| `Content/SMG/Configs/DataTables/AISkills/DT_AI_Skills_Rogue.uasset` | Rogue DataTable |

### Important Notes

- The JSON file is encoded in **UTF-16 LE (with BOM)**. All scripts handle this automatically.
- The UE project root must be identified before running any operations. Common path: `C:\zongjunli_ZONGJUNLI-PC2_313\SMG`
- The `.uasset` files are binary and cannot be edited directly — always modify through JSON then import.

## Workflow

### Step 1: Identify the UE Project Root

Determine the UE project root directory. Check for the `ProjectDir` environment variable, or ask the user. The JSON file is located at: `<project_root>/Content/SMG/Configs/NonAsset/Json/Json_AI_Skills.json`

### Step 2: Search for Target Skills

Use `scripts/dt_search.py` to locate the skill(s) to modify.

```bash
# Search by skill ID
python {SKILL_DIR}/scripts/dt_search.py --json <json_path> --id 230000

# Search by name keyword
python {SKILL_DIR}/scripts/dt_search.py --json <json_path> --name "蓄力"

# Search by any field
python {SKILL_DIR}/scripts/dt_search.py --json <json_path> --field DevName --value "瞬移"

# List all skills in a Part
python {SKILL_DIR}/scripts/dt_search.py --json <json_path> --part 1

# Show full JSON for results
python {SKILL_DIR}/scripts/dt_search.py --json <json_path> --id 230000 --full

# List all available fields
python {SKILL_DIR}/scripts/dt_search.py --json <json_path> --list-fields
```

Present search results to the user before proceeding with modifications.

### Step 3: Modify Skill Data

Use `scripts/dt_modify.py` to modify fields. The script automatically:
- Wraps plain text in `NSLOCTEXT()` format for localization fields
- Validates all data after modification
- Creates a timestamped backup before saving

```bash
# Modify LOC_Desc (auto-wraps in NSLOCTEXT)
python {SKILL_DIR}/scripts/dt_modify.py --json <json_path> --id 230000 --set-loc-desc "对随机角色造成伤害"

# Modify LOC_Name
python {SKILL_DIR}/scripts/dt_modify.py --json <json_path> --id 230000 --set-loc-name "新技能名"

# Modify any field
python {SKILL_DIR}/scripts/dt_modify.py --json <json_path> --id 230000 --set "bUseAlert=true" --set "DevName=新名称"

# Preview without saving (dry-run)
python {SKILL_DIR}/scripts/dt_modify.py --json <json_path> --id 230000 --set-loc-desc "测试" --dry-run

# Validate entire JSON
python {SKILL_DIR}/scripts/dt_modify.py --json <json_path> --validate
```

#### NSLOCTEXT Format Rules

For localization fields (`LOC_Name`, `LOC_Desc`, `LOC_AlertText`, `LOC_InterruptMessage`, `LOC_NoticeText`):
- When user provides plain text, the script auto-wraps it as: `NSLOCTEXT("<PartName>", "<SkillID>_<FieldName>", "<text>")`
- When user provides a full `NSLOCTEXT(...)` or `INVTEXT(...)` string, it is used as-is
- Use `--no-wrap` flag to disable auto-wrapping

#### Field Modification via Direct File Edit

For complex modifications (nested objects, arrays, or bulk changes), directly edit the JSON file using `replace_in_file` or `read_file` + `write_to_file` tools. Always validate after editing:

```bash
python {SKILL_DIR}/scripts/dt_modify.py --json <json_path> --validate
```

### Step 4: Import into UE Engine

After modifying and validating the JSON, generate the UE import command. The user must execute this command in the **UE Editor Output Log**:

```
py "{SKILL_DIR}/scripts/dt_import_ue.py" --json "<json_path>" --part <part_number>
```

Parameters:
- `--part 0` — Import Part0 (ID 200000–229999)
- `--part 1` — Import Part1 (ID 230000–239999)
- `--part 2` — Import Part2 (ID 240000–249999)
- `--part all` — Import all Parts

**Determine the correct `--part` number from the skill ID being modified.** Always provide the full command with absolute paths for the user to copy-paste.

### Step 5: Verify

After the user reports the UE import result, check the log output for:
- `Imported DataTable '...' - 0 Problems` = Success
- Any `Error` lines = Investigation needed

Refer to `references/field_schema.md` for the complete field documentation when needed.

## Error Handling

| Error | Cause | Solution |
|-------|-------|----------|
| `UnicodeDecodeError` | Wrong encoding detection | Scripts auto-detect; if issues persist, check file BOM bytes |
| `fill_data_table_from_json_string` fails | JSON fields don't match RowStruct | Validate JSON, check for missing/extra fields |
| `DataTable not found` | Wrong asset path | Verify the asset path exists in the UE Content Browser |
| P4 checkout failure | File locked by another user | Coordinate with team or force checkout |
