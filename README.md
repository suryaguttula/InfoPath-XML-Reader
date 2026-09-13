# InfoPath XML Reader & SharePoint Integration

A comprehensive C# solution for reading InfoPath XML files and syncing data with SharePoint lists.

## Features

### InfoPath XML Reading
- ✅ Read and parse InfoPath XML files
- ✅ Extract field values
- ✅ Handle repeating groups
- ✅ Search and filter data
- ✅ Nested field queries
- ✅ Export to CSV

### SharePoint Integration
- ✅ Upload single records to SharePoint
- ✅ Batch upload multiple files
- ✅ Upload repeating groups
- ✅ Sync data (create or update)
- ✅ Query SharePoint lists
- ✅ Export list data
- ✅ Update existing items
- ✅ Get field information

## Quick Start

### 1. Install Dependencies

```powershell
Install-Package Microsoft.SharePointOnline.CSOM
```

### 2. Basic InfoPath Reading

```csharp
using InfoPathXmlReader;

// Read InfoPath XML
var reader = new InfoPathXmlReader("form.xml");

// Get all fields
var fields = reader.GetAllFields();
foreach (var field in fields)
{
    Console.WriteLine($"{field.Key}: {field.Value}");
}
```

### 3. Upload to SharePoint

```csharp
using InfoPathToSharePoint;

var uploader = new InfoPathToSharePointUploader(
    "https://company.sharepoint.com/sites/site",
    "MyList",
    "user@company.com",
    "password"
);

var mapping = new Dictionary<string, string>
{
    { "Name", "Title" },
    { "Department", "Department" },
    { "Email", "Email" }
};

int itemId = uploader.UploadSingleRecord("form.xml", mapping);
uploader.Dispose();
```

## Included Files

### Core Classes
- **InfoPathXmlReader.cs** - Basic XML reading
- **InfoPathXmlReaderAdvanced.cs** - Advanced queries and operations
- **InfoPathToSharePointUploader.cs** - SharePoint upload operations
- **InfoPathToSharePointAdvanced.cs** - Batch and sync operations

### Examples
- **Program.cs** - InfoPath XML reading examples
- **SharePointIntegrationExamples.cs** - SharePoint integration examples

### Documentation
- **README.md** - This file
- **SHAREPOINT_INTEGRATION.md** - Complete SharePoint integration guide

### Sample Data
- **SampleInfoPathXml.xml** - Example InfoPath XML file
- **InfoPathToSharePoint.csproj** - Project dependencies

## Usage Scenarios

### Scenario 1: Migrate Employee Data
Read employee InfoPath forms and create SharePoint employee list items.

```csharp
var uploader = new InfoPathToSharePointUploader(siteUrl, listName, username, password);
var mapping = new Dictionary<string, string>
{
    { "EmployeeName", "Title" },
    { "EmployeeID", "EmployeeID" },
    { "Department", "Department" },
    { "Email", "Email" }
};
uploader.UploadSingleRecord("employee.xml", mapping);
```

### Scenario 2: Process Project Tasks
Extract tasks from InfoPath project form and upload to SharePoint tasks list.

```csharp
var uploader = new InfoPathToSharePointUploader(siteUrl, "Tasks", username, password);
var mapping = new Dictionary<string, string>
{
    { "TaskName", "Title" },
    { "AssignedTo", "AssignedTo" },
    { "DueDate", "DueDate" }
};
uploader.UploadRepeatingGroup("project.xml", "Task", mapping);
```

### Scenario 3: Batch Data Import
Upload multiple InfoPath files to SharePoint in one operation.

```csharp
var advanced = new InfoPathToSharePointAdvanced(siteUrl, listName, username, password);
string[] files = { "file1.xml", "file2.xml", "file3.xml" };
var results = advanced.BatchUpload(files, fieldMapping);
```

### Scenario 4: Data Synchronization
Keep SharePoint list in sync with InfoPath data (create if new, update if exists).

```csharp
var advanced = new InfoPathToSharePointAdvanced(siteUrl, listName, username, password);
var result = advanced.SyncWithSharePoint("employee.xml", mapping, "EmployeeID");
```

### Scenario 5: Data Export
Export SharePoint list data to CSV for reporting or analysis.

```csharp
var advanced = new InfoPathToSharePointAdvanced(siteUrl, listName, username, password);
advanced.ExportListToCsv("report.csv");
```

## Architecture

```
InfoPath XML File
        ↓
InfoPathXmlReader (Read & Parse)
        ↓
Field Mapping
        ↓
InfoPathToSharePointUploader (Transform & Upload)
        ↓
SharePoint List
```

## Key Classes

### InfoPathXmlReader
Basic operations for reading InfoPath XML files.

```csharp
new InfoPathXmlReader(filePath)
    .GetElementValue(name)
    .GetAllFields()
    .GetElementsByName(name)
    .PrintAllFields()
```

### InfoPathToSharePointUploader
Upload InfoPath data to SharePoint lists.

```csharp
new InfoPathToSharePointUploader(url, list, user, password)
    .UploadSingleRecord(xmlPath, mapping)
    .UploadRepeatingGroup(xmlPath, groupName, mapping)
    .UpdateListItem(itemId, xmlPath, mapping)
```

### InfoPathToSharePointAdvanced
Advanced operations with batch, sync, and query capabilities.

```csharp
new InfoPathToSharePointAdvanced(url, list, user, password)
    .BatchUpload(files, mapping)
    .SyncWithSharePoint(xmlPath, mapping, uniqueField)
    .QueryListItems(column, value)
    .ExportListToCsv(filePath)
```

## Requirements

- .NET Framework 4.7.2 or .NET Core 2.0+
- Microsoft.SharePointOnline.CSOM package
- Valid SharePoint Online or on-premises access
- Appropriate permissions on SharePoint lists

## Configuration

### SharePoint URL Format
```
SharePoint Online: https://company.sharepoint.com/sites/sitename
SharePoint On-Premises: http://server/sites/sitename
```

### Field Mapping
Use SharePoint internal field names, not display names:
```csharp
// Correct
{ "EmployeeName", "Title" }

// Incorrect
{ "EmployeeName", "Employee Name" }
```

## Error Handling

All classes include comprehensive error handling:

```csharp
try
{
    var uploader = new InfoPathToSharePointUploader(...);
    uploader.UploadSingleRecord(...);
}
catch (Exception ex)
{
    Console.WriteLine($"Error: {ex.Message}");
    // Handle error appropriately
}
finally
{
    uploader?.Dispose();
}
```

## Performance Considerations

- **Batch Operations**: 10-100 files per batch
- **Connection Pooling**: Reuse client context when possible
- **CAML Queries**: Use filters to reduce data transfer
- **CSV Export**: Use for large datasets (1000+ items)

## Security Best Practices

1. **Credentials**: Use secure credential storage (Azure Key Vault)
2. **Network**: Use HTTPS connections
3. **Validation**: Validate data before upload
4. **Auditing**: Log all upload operations
5. **Permissions**: Grant minimum required SharePoint permissions

## Troubleshooting

### Common Issues

**Connection Failed**
- Verify SharePoint URL
- Check username/password
- Ensure network connectivity
- Verify user permissions

**Upload Failed**
- Check XML file validity
- Verify field mapping
- Ensure required fields have values
- Review SharePoint list schema

**Data Type Mismatch**
- Verify data types match SharePoint columns
- Format dates correctly (yyyy-MM-dd)
- Use valid values for choice fields

## Limitations

- Single authentication per uploader instance
- CSOM not available for SharePoint 365 (must use PnP)
- Field names must be in internal format
- Large files may require chunked upload

## Future Enhancements

- [ ] Support for SharePoint REST API
- [ ] PnP PowerShell integration
- [ ] Workflow triggering
- [ ] Attachment upload
- [ ] Advanced CAML query builder
- [ ] Bulk update operations
- [ ] Change tracking/versioning

## Contributing

Contributions are welcome! Please feel free to submit pull requests.

## License

MIT License - see LICENSE file for details

## Support

For issues, questions, or suggestions:
1. Check the documentation
2. Review troubleshooting guide
3. Open an issue on GitHub

## Related Links

- [GitHub Repository](https://github.com/suryaguttula/InfoPath-XML-Reader)
- [SharePoint Integration Guide](SHAREPOINT_INTEGRATION.md)
- [Microsoft SharePoint Documentation](https://docs.microsoft.com/sharepoint/)
