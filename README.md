# PowerShell Deploy Windows Service

# Deploy Windows Service
```
C:\Windows\System32\sc.exe create "ThirdSight Data Export Service 1" binPath= "D:\ThirdSight\ThirdSight.DataMigration1\TSDataExport.exe" DisplayName= "ThirdSight Data Export Service 1" start= auto obj= "MANH\ADMIN" password= "Abc@123$"
```

## 1. Connection string

### 1.1. For app.config or web.config (XML format):
```
# Path to your config file
$configPath = "C:\Path\To\YourApp.exe.config"

# Load the XML
[xml]$config = Get-Content $configPath

# Update the connection string
$config.configuration.connectionStrings.add |
    Where-Object { $_.name -eq "DefaultConnection" } |
    ForEach-Object { $_.connectionString = "Data Source=(local);Initial Catalog=NewDb;Integrated Security=True;" }

# Save the modified XML
$config.Save($configPath)
```

### 1.2. For appsettings.json (JSON format in ASP.NET Core):
```
# Path to JSON file
$jsonPath = "C:\Path\To\appsettings.json"

# Load JSON
$json = Get-Content $jsonPath | ConvertFrom-Json

# Update the connection string
$json.ConnectionStrings.DefaultConnection = "Server=(local);Database=NewDb;Trusted_Connection=True;"

# Save updated JSON
$json | ConvertTo-Json -Depth 10 | Set-Content $jsonPath -Encoding UTF8
```

### 1.3. Put the connection string in a PowerShell parameter so you can easily reuse it or pass it as an argument

**For XML config (e.g., web.config, app.config):**

```
param (
    [string]$ConfigPath = "C:\Path\To\YourApp.exe.config",
    [string]$NewConnectionString = "Data Source=(local);Initial Catalog=NewDb;Integrated Security=True;"
)

[xml]$config = Get-Content $ConfigPath

$config.configuration.connectionStrings.add |
    Where-Object { $_.name -eq "DefaultConnection" } |
    ForEach-Object { $_.connectionString = $NewConnectionString }

$config.Save($ConfigPath)

Write-Host "Connection string updated successfully in $ConfigPath"
```

**For appsettings.json:**

```
param (
    [string]$JsonPath = "C:\Path\To\appsettings.json",
    [string]$NewConnectionString = "Server=(local);Database=NewDb;Trusted_Connection=True;"
)

$json = Get-Content $JsonPath | ConvertFrom-Json

$json.ConnectionStrings.DefaultConnection = $NewConnectionString

$json | ConvertTo-Json -Depth 10 | Set-Content $JsonPath -Encoding UTF8

Write-Host "Connection string updated successfully in $JsonPath"
```

**And then**
```
.\Update-ConnectionString.ps1 -NewConnectionString "Server=.;Database=ProdDb;Trusted_Connection=True;"
```
