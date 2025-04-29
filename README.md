# powershell deploy winsvc


## Connection string

### 1. For app.config or web.config (XML format):
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


### 2. For appsettings.json (JSON format in ASP.NET Core):
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
