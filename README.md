# File Import Help Articles

This repository contains end-user help documentation for Integration Central file import features.

## Structure

### humanforce-help-articles/
Help articles for Humanforce WFM Integration Central file import types.

Currently available:
- **Labour_Group_Allocation_Import.md** - Guide for uploading labour group allocations (HC-6966)

## Adding New Articles

When adding new help articles:

1. Place them in the appropriate subfolder (e.g., `humanforce-help-articles/`)
2. Use descriptive filenames (e.g., `Feature_Name_Import.md`)
3. Follow the established format:
   - Introduction and purpose
   - Part 1: Prepare the import file
   - Part 2: Upload and configure
   - Reference: Field definitions table
   - Additional information
   - Tips section

## Verification Standard

All help articles should be verified against source code before publication:
- Field specifications confirmed from SystemSettings JSON
- Validation rules verified from source code
- Behavioral patterns confirmed from work logs
- UI elements noted as standard IC features when not verifiable from backend

## Related Resources

- Original documentation and verification reports are stored in the AI Experiments folder
- Each article should reference its originating JIRA epic/ticket
