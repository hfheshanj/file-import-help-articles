# Upload labour group allocations to WFM using Integration Central

Follow this guide to upload labour group allocation records in bulk to Humanforce WFM using the Integration Central file import tool. This allows you to create, update, and delete labour groups, assign shift types to labour groups, and configure location/department/role allocations.

This task is designed for organisations managing labour group configurations, shift type assignments, and workforce allocation percentages across multiple locations, departments, and roles in WFM.

If you're not familiar with the Integration Central file import feature, read **Configure and run file import tasks in Integration Central** before continuing.

For new users: All uploads are validated for formatting and field matching. The system supports three types of row configurations: group-only rows (for deleting labour groups), group + shift type rows (for assigning shift types), and full allocation rows (for configuring allocation percentages). All rows for the same labour group must use identical Shift Type values or the import will be rejected.

## Part 1: Prepare the labour group allocation import file

1. Go to Management > Advanced > Integration Central > Add New > File Import.
2. Set the Data type to **Labour Group Allocation Import**.
3. Click **Download template** to get the latest version.
4. Open the newly downloaded template.
5. Review the field requirements in the reference section below to ensure your data is valid and complete.
6. Enter labour group allocation data into the file using the required headers.
7. Save the file locally in .CSV format.

When your file is correctly structured and saved, it's ready to upload in Integration Central.

## Part 2: Upload and configure the labour group allocation import

1. Go to Management > Advanced > Integration Central > Add New > File Import.
2. Set the Data type to **Labour Group Allocation Import**.
3. In the File section:
   - For an ad-hoc upload, drag and drop your file into the upload box, or click **Browse** to select the file from your computer.
   - For a scheduled upload, click **Humanforce SFTP import**.
   - Note: You must have SFTP configured. To learn how, see **How to setup the Humanforce SFTP folder**.

4. Configure import options:
   - Choose a labour group matching identifier (Labour Group Name, Labour Group Export Code, or Labour Group GuidKey). This step is mandatory.
   - The matching strategy you select determines which labour group identifier field must be populated in your CSV file. All rows for the same labour group must use the same identifier value.
   - For example: If you select Name matching, only the Labour Group Name field is required. Export Code and GuidKey fields are optional.

5. (Optional) In Task completion notification, enter an email address to be notified when the import finishes.
6. Click **Next**.
7. Review the field mapping screen and update if required:
   - Field mapping rules (checkboxes at the top)
   - Source fields > target fields mapping
   - Lookup types for entity fields (Location, Department, Role, Shift Type)
   - Default values
8. Click **Next**.
9. Choose one of the following:
   - **Save mapping & import** – if you plan to reuse this setup in future.
   - **Import only** – if this is a one-time upload.

Once complete, you'll see a confirmation summary including:
- Number of rows successfully imported
- Any failed rows with error details

For saved mappings:
- Click **Got it** to return to Integration Central > File Import.
- Click the three dot menu next to your file and choose **View logs** or **History** to check results.

When successful, the uploaded labour groups and allocations will appear in the labour group configuration screens in WFM.

## Reference: Labour Group Allocation Import file field definitions

| Field Name | Description | Format / Rules |
|------------|-------------|----------------|
| **Labour Group Name** | Name of the labour group. Used to match existing labour groups when matching strategy is Name. | Optional (required only with Name matching strategy). Max 100 characters. Min 1 character. Matching is case-sensitive and whitespace-sensitive (e.g. 'Group A ' will not match 'Group A'). |
| **Labour Group Export Code** | Export code of the labour group. Used to match existing labour groups when matching strategy is Export Code. | Optional (required only with Export Code matching strategy). Max 50 characters. Matching is case-sensitive and whitespace-sensitive. Export Code uniqueness is validated during import (not enforced at database level). |
| **Labour Group GuidKey** | GuidKey of the labour group. Used to match existing labour groups when matching strategy is GuidKey. | Optional (required only with GuidKey matching strategy). Max 36 characters. Must be a valid GUID format (e.g. 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'). |
| **Labour Group Deleted** | Set to Y or TRUE to delete the matching labour group record. | Optional. Boolean field. TRUE values: Y, T, 1, True, TRUE, yes, YES. FALSE values: N, F, 0, False, FALSE, no, NO. Case-insensitive. When set to Y, the matching labour group is marked as deleted. Deletion only sets the Deleted flag and does not cascade to child scope or allocation records. |
| **Shift Type** | Shift types to assign to the labour group. Multiple shift types can be separated by semicolon (;). | Optional. Supported lookups: Name, ShortName, ExportCode, GuidKey. Leave types are automatically filtered out and cannot be assigned to labour groups. If omitted or empty for an existing labour group, existing shift types remain unchanged. All rows for the same labour group must have identical Shift Type values or the import will fail. For new labour groups, at least one shift type is required unless the group is being deleted. |
| **Location** | Location for the labour group allocation. | Optional (required only for allocation rows). **Important**: Location names are matched against the LocationName field in WFM, not the Name field. Supported lookups: Name (LocationName), ExportCode, GuidKey. Matching is case-sensitive and whitespace-sensitive. If provided, Department, Role, and Allocation Value must also be provided. |
| **Department** | Department for the labour group allocation. | Optional (required only for allocation rows). Supported lookups: Name, ExportCode, GuidKey. Matching is case-sensitive and whitespace-sensitive. If provided, Location, Role, and Allocation Value must also be provided. |
| **Role** | Role for the labour group allocation. | Optional (required only for allocation rows). Supported lookups: Name, ExportCode, GuidKey. Matching is case-sensitive and whitespace-sensitive. If provided, Location, Department, and Allocation Value must also be provided. |
| **Allocation Value** | Percentage value for the labour group allocation. | Optional (required only for allocation rows). Must be a decimal number between 0 and 100 (inclusive). Maximum 2 decimal places (e.g. 50, 33.33, 100.00). If provided, Location, Department, and Role must also be provided. |
| **Allocation Deleted** | Set to Y or TRUE to mark this specific allocation as deleted. | Optional. Boolean field. TRUE values: Y, T, 1, True, TRUE, yes, YES. FALSE values: N, F, 0, False, FALSE, no, NO. Case-insensitive. When set to Y, the matching allocation (by Location + Department + Role) is marked as deleted. The labour group and its shift types remain unchanged. |

## Additional information

### Row types and combinations

The import supports three distinct row types:

1. **Group-only rows** – Rows that specify a labour group identifier and optionally set Labour Group Deleted = Y. No shift types or allocations. Used to delete labour groups.
2. **Group + Shift Type rows** – Rows that specify a labour group identifier and Shift Type values. No allocation fields. Used to create or update labour groups and assign shift types.
3. **Full allocation rows** – Rows that specify a labour group identifier, optionally Shift Type values, and all four allocation fields (Location, Department, Role, Allocation Value). Used to create or update labour groups with allocations.

**Important**: All allocation fields (Location, Department, Role, Allocation Value) must be provided together. Providing only some of these fields will cause validation errors.

### Update behavior and duplicate detection

- **Creating new labour groups**: If no labour group matches the identifier, a new labour group is created. For new labour groups, at least one shift type is required unless the group is being deleted.
- **Updating existing labour groups**: If a labour group matches the identifier, the system updates shift types (if provided) and merges allocations.
- **Shift type sync**: The system replaces all shift types for a labour group with the shift types specified in the import file. If multiple rows for the same labour group have different Shift Type values, the entire import batch will be rejected. To keep existing shift types unchanged, leave the Shift Type field empty on all rows for that group.
- **Allocation merge**: The system merges allocations based on the Location + Department + Role combination. Existing allocations with the same combination are updated; new combinations are added.

#### ⚠️ Critical: Duplicate Detection Behavior

Within the same labour group, you cannot have multiple allocations that resolve to the same Location + Department + Role combination (even if specified using different identifier types like Name vs ExportCode). The system validates this after resolving entity identifiers.

**If any labour group in your import contains duplicate allocations, the ENTIRE import batch will be rejected and no rows will be processed.** Ensure each allocation is unique within each labour group.

### File size and batching

File must be under 15 MB. If your file exceeds this, split it into multiple smaller files. The system automatically handles batching during import (maximum 200 records per batch, grouped by labour group identifier).

### Validation and errors

**Batch-level validation errors** (entire batch rejected, no rows processed):
- Duplicate labour group identifiers within the import
- Missing labour group identifier fields required by the selected matching strategy
- Duplicate allocations within any labour group
- Inconsistent Shift Type values for the same labour group
- Export Code not unique (two or more labour groups have the same Export Code value)

**Row-level validation errors** (only affected rows rejected, valid rows processed):
- Invalid allocation values (not between 0 and 100, or more than 2 decimal places)
- Entity identifiers not found (Location, Department, Role, Shift Type)
- Invalid Location/Department/Role combinations (not valid DepartmentRole relationships in WFM)
- Partial allocation fields provided (some but not all of Location, Department, Role, or Allocation Value)
- New labour group without shift types (at least one shift type required unless being deleted)

### Matching and resolution

- The matching strategy selected during upload determines which labour group identifier field is used to find existing labour groups.
- Entity identifiers (Location, Department, Role, Shift Type) are resolved using the lookup type configured in the field mapping screen (Name, ExportCode, or GuidKey).
- **Location identifiers are matched against the LocationName field in WFM, not the Name field.**
- All identifier matching is case-sensitive and whitespace-sensitive.
- Location + Department + Role combinations must exist as valid DepartmentRole relationships in WFM. Invalid combinations will be rejected.
- **Leave types are automatically filtered out during import** and cannot be assigned to labour groups, even if included in the Shift Type field.

## Tips

- Always test with a small sample file in a non-production environment before bulk importing.
- Use Export Code or GuidKey for more reliable matching in scheduled imports.
- Shift types must already exist in the system. Leave types are automatically excluded.
- To update only shift types for an existing labour group, provide rows with the labour group identifier and Shift Type field populated, leaving all allocation fields blank.
- To update only allocations for an existing labour group, provide rows with the labour group identifier and all allocation fields populated. Leave Shift Type blank to preserve existing shift types.
- To delete a labour group, provide a row with the labour group identifier and set Labour Group Deleted = Y.
- To delete a specific allocation, provide a row with the labour group identifier, all allocation fields, and set Allocation Deleted = Y.
- All rows for the same labour group must use the same labour group identifier value and the same Shift Type value (or all leave it blank).
- **Critical**: Ensure no duplicate Location+Department+Role combinations exist within each labour group, as this will reject the entire batch.
- To avoid duplication, do not upload the same file more than once.
- If you need to automate uploads, speak to your administrator about scheduled import options.
