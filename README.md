# Fabric Permissions Manager

A PowerShell-based tool for managing workspace permissions in Microsoft Fabric and Power BI. This tool allows administrators to easily add or remove workspace permissions for users, service principals, and security groups across multiple workspaces.

## Overview

The Fabric Permissions Manager provides an interactive GUI interface to manage workspace-level permissions in Microsoft Fabric/Power BI environments. It streamlines the process of granting or revoking access to workspaces, making it easier to manage permissions at scale.

## Features

- **Multi-Workspace Management**: Add or remove permissions across multiple workspaces in a single operation
- **Multiple Principal Types**: Support for Users, Service Principals (Apps), and Security Groups
- **Multi-Cloud Support**: Works with various Power BI cloud environments (Commercial, Government, China, Germany)
- **Interactive GUI**: User-friendly Windows Forms dialogs for easy workspace selection
- **Bulk Operations**: Process multiple workspaces efficiently with progress tracking
- **Current User Mode**: Automatically detect and manage permissions for the currently logged-in user
- **Manual Entry Mode**: Specify custom user emails, service principal IDs, or security group IDs

## Prerequisites

### Required Permissions

**This tool requires Tenant Administrator permissions** to function properly. The script uses Power BI Admin APIs to:
- List all workspaces in the tenant (`/v1.0/myorg/admin/groups`)
- Add users to workspaces (`/v1.0/myorg/admin/groups/{workspaceId}/users`)
- Remove users from workspaces (`/v1.0/myorg/admin/groups/{workspaceId}/users/{userId}`)

If you do not have Tenant Admin permissions, the script will fail when attempting to fetch the workspace list.

### Required Software

- **PowerShell**: Version 5.1 or higher
- **MicrosoftPowerBIMgmt Module**: The script will automatically install this module if not present

### System Requirements

- Windows operating system (required for Windows Forms dialogs)
- Internet connection to connect to Power BI services
- Execution policy allowing script execution (the script temporarily sets this to Bypass for the session)

## Installation

1. Download the `Manage Workspace Permissions PS Script.txt` file
2. No additional installation is required - the script will automatically install required modules on first run

## Usage

### Basic Steps

1. **Run the Script**:
   ```powershell
   .\Manage Workspace Permissions PS Script.txt
   ```

2. **Select Environment**: Choose your Power BI cloud environment (defaults to Commercial/Public after 60 seconds)
   - Public (Commercial)
   - Germany
   - USGov
   - China
   - USGovHigh
   - USGovMil

3. **Authenticate**: Sign in with your Tenant Administrator account when prompted

4. **Select Action**: Choose whether to Add or Remove permissions

5. **Select Principal Type**: Choose the type of principal to manage:
   - **User**: Individual user accounts (requires email address)
   - **App**: Service Principal/Application (requires Object ID)
   - **Group**: Security Group (requires Object ID)

6. **Specify Principal**:
   - **Current User**: Automatically uses the currently logged-in user
   - **Manual Entry**: Enter a specific user email, app Object ID, or group Object ID

7. **Select Workspaces**: A GUI dialog will show available workspaces. Select the ones you want to modify.

8. **Review Results**: The script will display a summary of successful and failed operations

### Example Scenarios

#### Add Current User as Admin to Multiple Workspaces

1. Run the script and authenticate
2. Select "Add" action
3. Select "User" principal type
4. Select "Current User" mode
5. Select desired workspaces from the list
6. Review the summary of added permissions

#### Remove a Service Principal from Workspaces

1. Run the script and authenticate
2. Select "Remove" action
3. Select "App" principal type
4. Select "Manual Entry" mode
5. Enter the Service Principal Object ID
6. Select workspaces to remove the app from
7. Review the summary of removed permissions

#### Grant Security Group Access to Workspaces

1. Run the script and authenticate
2. Select "Add" action
3. Select "Group" principal type
4. Select "Manual Entry" mode
5. Enter the Security Group Object ID
6. Select target workspaces
7. Review the summary of added permissions

## Supported Workspace Types

The script can manage permissions for standard workspaces but **does not support**:
- Personal workspaces
- PersonalGroup workspaces

These workspace types will be automatically skipped during operations with a warning message.

## Access Rights

When adding permissions, users are granted **Admin** access by default. The Admin role provides full control over the workspace, including:
- Managing workspace content
- Publishing and updating content
- Managing workspace permissions
- Deleting the workspace

## Limitations

1. **Tenant Admin Required**: You must be a Tenant Administrator to use this tool
2. **Personal Workspaces Not Supported**: Cannot modify permissions on Personal or PersonalGroup workspaces
3. **Windows Only**: The GUI dialogs require Windows operating system
4. **No Granular Role Assignment**: Currently only supports Admin role assignment (not Contributor, Member, or Viewer)

## API Information

### Admin APIs Used

This tool uses **Power BI Admin APIs**, which require Tenant Administrator permissions:

- **GET** `/v1.0/myorg/admin/groups` - List all workspaces in the tenant
- **POST** `/v1.0/myorg/admin/groups/{workspaceId}/users` - Add a user/app/group to a workspace
- **DELETE** `/v1.0/myorg/admin/groups/{workspaceId}/users/{userId}` - Remove a user/app/group from a workspace

### Non-Admin Alternative

Currently, this script **requires** admin APIs. If you only need to manage workspaces where you already have admin access (without being a Tenant Admin), you would need to use the non-admin APIs (`/v1.0/myorg/groups`) instead, which are not implemented in this version.

## Troubleshooting

### "Failed to fetch workspaces using Admin API"

**Cause**: You don't have Tenant Administrator permissions.

**Solution**: 
- Ensure you're logged in with a Tenant Admin account
- Contact your organization's administrator to grant you Tenant Admin role

### Script Execution Policy Error

**Cause**: PowerShell execution policy prevents running scripts.

**Solution**: The script automatically sets execution policy to Bypass for the session. If issues persist, run PowerShell as Administrator.

### Module Installation Fails

**Cause**: Insufficient permissions to install PowerShell modules.

**Solution**: Run PowerShell as Administrator or ensure you have permissions to install modules in the CurrentUser scope.

### Workspace Not Listed

**Cause**: The workspace might be a Personal or PersonalGroup type, or there might be API pagination issues.

**Solution**: 
- Check workspace type - Personal workspaces are not supported
- The script uses pagination to fetch all workspaces (1000 at a time)

### Permission Changes Not Applied

**Cause**: Various reasons including workspace type restrictions or API errors.

**Solution**: 
- Check the error message in the console output
- Verify the workspace is not a Personal or PersonalGroup type
- Ensure the principal identifier (email/Object ID) is correct
- Check that you have the necessary admin permissions

## Security Considerations

- **Credential Security**: The script uses the MicrosoftPowerBIMgmt module for secure authentication
- **Audit Trail**: All permission changes are logged in Power BI's audit logs
- **Least Privilege**: Only grant Tenant Admin access to users who need to manage permissions across all workspaces
- **Review Changes**: Always review the summary after operations to ensure changes were applied as expected

## Contributing

If you encounter issues or have suggestions for improvements, please contact the repository maintainer.

## License

Please refer to the repository license file for licensing information.

## Version History

- **Initial Release**: Basic functionality for adding and removing workspace permissions using Admin APIs

## Support

For questions or issues:
1. Check the Troubleshooting section above
2. Review the console output for specific error messages
3. Ensure you meet all prerequisites, especially Tenant Admin permissions
4. Verify your PowerShell and module versions are up to date

## Additional Resources

- [Power BI REST API Reference](https://learn.microsoft.com/en-us/rest/api/power-bi/)
- [Power BI Admin APIs](https://learn.microsoft.com/en-us/rest/api/power-bi/admin)
- [MicrosoftPowerBIMgmt PowerShell Module](https://learn.microsoft.com/en-us/powershell/power-bi/overview)
