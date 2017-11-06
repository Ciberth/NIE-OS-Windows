# Oefening lesweek 6 

(Op 30/10/2017)

Inhaallabo Powershell

## Opmerkingen

## Oplossingen

### 1

```
Set-Culture en-US
$excel = New-Object -ComObject Excel.Application
$excel.Visible = $True
$book = $excel.Workbooks.add()
$book.Worksheets.count

```


### 3

```
Get-Command *service* -commandtype cmdlet

Get-Command | where {$_.Name -like "*service*"}

Get-Service | where {$_.Status -like "stopped"}

```


### 4

```
$vds = Get-Service | where {$_.Name -eq "vds"}
$vds.RequiredServices
```


### 5 

```
# directories
$mappen = ls -d 
$mappen.Count

# -f voor files


ls | where {$_.LastWriteTime -gt "01/06/2017"} | sort LastWriteTime -Descending | select Name, LastWriteTime

```

### 9

```

$location = New-Object -ComObject "wbemScripting.SwbemLocator"
$service = $location.ConnectServer(".","root\cimv2")

$klasse = $service.get("Win32_LogicalDisk")


$instanties = $service.ExecQuery("select * from Win32_LogicalDisk")

$instanties | % {$_.Properties_.Item("DeviceID")}
| select value
```


### 11

```

$klassen = $service.execQuery("select * from meta_class")

$klassen | where {$_.SystemProperties_.item("__CLASS").value -like "*event*"} | select @{name="class";expression={$_.SystemProperties_.item("__CLASS").value}}

```


### 13

```

$instanties.count

$instanties | % {$_.SystemProperties_.item("__path").value}

```

### 14

```
$klasse.Properties_+$klasse.SystemProperties_ | foreach {  if($_.isarray){  ""+$_.cimtype + "array= "} else { "geen array= " + $_.cimtype } }

```

