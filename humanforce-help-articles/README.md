# Humanforce WFM Integration Central Help Articles

This folder contains end-user help documentation for Humanforce WFM Integration Central file import features.

## Available Articles

### Labour Group Allocation Import
**File**: `Labour_Group_Allocation_Import.md`
**Epic**: HC-6966
**Date**: 2026-05-21
**Status**: ✅ Verified against source code

Comprehensive guide for uploading labour group allocation records including:
- Creating and updating labour groups
- Assigning shift types to labour groups
- Configuring location/department/role allocations
- Deletion handling
- Validation rules and error handling

**Key Features**:
- 3 row types supported (group-only, group+shift types, full allocations)
- 3 matching strategies (Name, Export Code, GuidKey)
- Batch-level and row-level validation
- Maximum 200 records per batch

---

## Article Format

All articles follow this structure:

1. **Introduction** - Feature overview and purpose
2. **Part 1: Prepare the import file** - Template download and preparation steps
3. **Part 2: Upload and configure** - Upload process and configuration options
4. **Reference: Field definitions** - Complete field documentation in table format
5. **Additional information** - Row types, update behavior, error handling, validation
6. **Tips** - Best practices and common scenarios

## Verification Process

Each article is verified against:
- SystemSettings JSON field configurations
- Validator source code
- Service layer implementation
- Work logs and implementation documentation
- GraphQL mutations and schema

Unverified statements (UI elements, standard IC features) are clearly marked.
