# Upload labour group allocations to WFM using Integration Central

Follow this guide to upload labour group allocation records in bulk to Humanforce WFM using the Integration Central file import tool. This allows you to create, update, and delete labour groups, assign shift types to labour groups, and configure location/department/role allocations.

If you're not familiar with the Integration Central file import feature, read **Configure and run file import tasks in Integration Central** before continuing.

## Part 1: Prepare the labour group allocation import file

1. Go to Management > Advanced > Integration Central > Add New > File Import.
2. Set the Data type to **Labour Group Allocation Import**.
3. Click **Download template** to get the latest version.
4. Open the newly downloaded template.
5. Review the field requirements in the reference section below to ensure your data is valid and complete.
6. Enter labour group allocation data into the file using the required headers.
7. Save the file locally in .CSV format.

## Part 2: Upload and configure the labour group allocation import

1. Go to Management > Advanced > Integration Central > Add New > File Import.
2. Set the Data type to **Labour Group Allocation Import**.
3. In the File section:
   - For an ad-hoc upload, drag and drop your file into the upload box, or click **Browse** to select the file from your computer.
   - For a scheduled upload, click **Humanforce SFTP import**.
   - Note: You must have SFTP configured. To learn how, see **How to setup the Humanforce SFTP folder**.

4. Configure import options:
   - Choose a labour group matching identifier (Labour Group Name, Labour Group Export Code, or Labour Group GuidKey). This step is mandatory.
   - The matching strategy you select determines which labour group identifier field must be populated in your CSV file.

5. (Optional) In Task completion notification, enter an email address to be notified when the import finishes.
6. Click **Next**.
7. Review the field mapping screen and configure lookup types for entity fields (Location, Department, Role, Shift Type).
8. Click **Next**.
9. Choose **Save mapping & import** or **Import only**.

Once complete, you'll see a confirmation summary including number of rows successfully imported and any failed rows with error details.

## Reference: Labour Group Allocation Import file field definitions

| Field Name | Description | Format / Rules |
|------------|-------------|----------------|
| **Labour Group Name** | Name of the labour group. Used to match existing labour groups when matching strategy is Name. | Optional (required only with Name matching strategy). Max 100 characters. Min 1 character. Matching is case-sensitive and whitespace-sensitive. |
| **Labour Group Export Code** | Export code of the labour group. Used to match existing labour groups when matching strategy is Export Code. | Optional (required only with Export Code matching strategy). Max 50 characters. Matching is case-sensitive and whitespace-sensitive. |
| **Labour Group GuidKey** | GuidKey of the labour group. Used to match existing labour groups when matching strategy is GuidKey. | Optional (required only with GuidKey matching strategy). Max 36 characters. Must be a valid GUID format. |
| **Labour Group Deleted** | Set to Y or TRUE to delete the matching labour group record. | Optional. Boolean field. TRUE values: Y, T, 1, True, TRUE, yes, YES. FALSE values: N, F, 0, False, FALSE, no, NO. Case-insensitive. Deletion only sets the Deleted flag and does not cascade to child records. |
| **Shift Type** | Shift types to assign to the labour group. Multiple shift types can be separated by semicolon (;). | Optional. Supported lookups: Name, ShortName, ExportCode, GuidKey. Leave types are automatically filtered out. All rows for the same labour group must have identical Shift Type values. For new labour groups, at least one shift type is required unless the group is being deleted. |
| **Location** | Location for the labour group allocation. | Optional (required only for allocation rows). Location names are matched against the LocationName field in WFM. Supported lookups: Name (LocationName), ExportCode, GuidKey. Matching is case-sensitive and whitespace-sensitive. If provided, Department, Role, and Allocation Value must also be provided. |
| **Department** | Department for the labour group allocation. | Optional (required only for allocation rows). Supported lookups: Name, ExportCode, GuidKey. Matching is case-sensitive and whitespace-sensitive. If provided, Location, Role, and Allocation Value must also be provided. |
| **Role** | Role for the labour group allocation. | Optional (required only for allocation rows). Supported lookups: Name, ExportCode, GuidKey. Matching is case-sensitive and whitespace-sensitive. If provided, Location, Department, and Allocation Value must also be provided. |
| **Allocation Value** | Percentage value for the labour group allocation. | Optional (required only for allocation rows). Must be a decimal number between 0 and 100 (inclusive). Maximum 2 decimal places. If provided, Location, Department, and Role must also be provided. |
| **Allocation Deleted** | Set to Y or TRUE to mark this specific allocation as deleted. | Optional. Boolean field. TRUE values: Y, T, 1, True, TRUE, yes, YES. FALSE values: N, F, 0, False, FALSE, no, NO. Case-insensitive. The labour group and its shift types remain unchanged. |

## Additional information

### Row types

The import supports three row types:

1. **Group-only rows** – Specify a labour group identifier and optionally set Labour Group Deleted = Y. No shift types or allocations.
2. **Group + Shift Type rows** – Specify a labour group identifier and Shift Type values. No allocation fields.
3. **Full allocation rows** – Specify a labour group identifier, optionally Shift Type values, and all four allocation fields (Location, Department, Role, Allocation Value).

All allocation fields (Location, Department, Role, Allocation Value) must be provided together.

### Update behavior

- **Creating new labour groups**: If no labour group matches the identifier, a new labour group is created. For new labour groups, at least one shift type is required unless the group is being deleted.
- **Updating existing labour groups**: If a labour group matches the identifier, the system updates shift types (if provided) and merges allocations.
- **Shift type sync**: The system replaces all shift types for a labour group with the shift types specified in the import file. If multiple rows for the same labour group have different Shift Type values, the entire import batch will be rejected. To keep existing shift types unchanged, leave the Shift Type field empty on all rows for that group.
- **Allocation merge**: The system merges allocations based on the Location + Department + Role combination. Existing allocations with the same combination are updated; new combinations are added.

### Duplicate detection

Within the same labour group, you cannot have multiple allocations that resolve to the same Location + Department + Role combination. If any labour group in your import contains duplicate allocations, the entire import batch will be rejected and no rows will be processed.

### Validation errors

**Batch-level validation errors** (entire batch rejected, no rows processed):
- Duplicate labour group identifiers within the import
- Missing labour group identifier fields required by the selected matching strategy
- Duplicate allocations within any labour group
- Inconsistent Shift Type values for the same labour group
- Export Code not unique

**Row-level validation errors** (only affected rows rejected, valid rows processed):
- Invalid allocation values
- Entity identifiers not found (Location, Department, Role, Shift Type)
- Invalid Location/Department/Role combinations
- Partial allocation fields provided
- New labour group without shift types

### Matching rules

- Entity identifiers (Location, Department, Role, Shift Type) are resolved using the lookup type configured in the field mapping screen (Name, ExportCode, or GuidKey).
- Location identifiers are matched against the LocationName field in WFM, not the Name field.
- All identifier matching is case-sensitive and whitespace-sensitive.
- Location + Department + Role combinations must exist as valid DepartmentRole relationships in WFM.
- Leave types are automatically filtered out during import and cannot be assigned to labour groups.

### File size

File must be under 15 MB. The system automatically handles batching during import (maximum 200 records per batch, grouped by labour group identifier).

## Tips

- Use Export Code or GuidKey for more reliable matching in scheduled imports.
- All rows for the same labour group must use the same labour group identifier value and the same Shift Type value (or all leave it blank).
- Ensure no duplicate Location+Department+Role combinations exist within each labour group.
- To avoid duplication, do not upload the same file more than once.
