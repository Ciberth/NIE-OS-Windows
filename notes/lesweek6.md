# Lesweek 6

(Op 30/10/2017)

# Powershell

```
Get-Help about_while
Get-Alias
```

## Aliassen

Set-Location -> cd 

## Vastpakken van info

```powershell
$all = Get-Alias
$all

$all.Count
$all.Length

# of zonder tussenvar

(Get-Alias).Count # staat er niet in maar werkt
(Get-Alias). # is ander lijstje dan $all.




$a = Get-Alias -Name dir

$a

$a. # van 1 alias
$a.Definition
$a.Description


Get-Alias -Definition Set-Location

```


## COM-objecten


```powershell

$excel = New-Object -ComObject Excel.Application
$fso = New-Object -ComObject Scripting.FileSystemObject


$fso.Drives

$fso.Drives.Count


$excel.Workbooks.Count

$excel.Workbooks.Add()

$workbook = $excel.Workbooks.Add()

$excel.Workbooks.Count # 2 nu al


$fso.GetFolder(".")
$folder = $fso.GetFolder(".")

$folder.Path

$fso.GetFolder(".").Path # werkt wel maar geeft geen completion



Write-Output "een" "twee"

Write-Host "een" "twee"


Get-Command # properties
Get-Alias # toont ook een koppeling
```


## Cmdlets

Verb-Noun
Parameters met -


## Datum

```
Get-Command -Name *Date*
Get-Command -Name *-Date*
Get-Command -Name *Date

Get-Command -Noun date

(Get-Date).Hour

$time = Get-Date
$time.Hour

$time.ToBinary()

```

## Twee belangrijke groepen

Get's die generen meestal uitvoer
Anderen dienen vaak voor uitvoer te bewerken via pipe |

```
Get-Command | Sort-Object
Get-Command | Sort-Object version


$lijst = Get-Command 

$lijst | sort version


Get-Help sort -parameter pro*

```


```
Get-Help Select-Object
Get-Help Select-Object -Examples


$lijst | select -first 5
$lijst | select -last 5

$lijst | sort version -D | select -last 5

$lijst | select -index 5, 40, 80

$lijst | select - first 10 | select -property name
$lijst | select - first 10 | select -property na*
$lijst | select - first 10 | select na*


$lijst | select -first 10 | select Name, Description

$lijst | select -f 10 | select Name, Module # naast mekaar
$lijst | select -f 10 | select * # onder mekaar



```


## Foreach

```
Get-Process | select -first 10 | foreach handles
Get-Process | select -first 10 | foreach Threads

Get-Process | select -first 10 | foreach Threads.Count # werkt niet

Get-Process | select -first 10 | foreach {Write-Host "name", $_.Name}

Get-Process | select -first 10 | foreach {"name: " + $_.Name}


Get-Process | select -first 10 | foreach {
	$_.Name + " "+$_.Threads.Count}


$p10 = Get-Process | Select -first 10

$p10 | foreach{$_.Name}

```


## Expressie

```
Get-Service | select Name, @{Name="Aaantal"; Expression={$_.DependentServices.Count}}

Get-Service | select Name, @{Name="Aaantal"; Expression={$_.DependentServices.Count}} | sort aantal



Get-Service | where {$_.Status -like "run*"}

```



## WMI

```
Get-WmiObject # POWERSHELL WMI object (niet zelfde van in perl)

Get-WmiObject -class Win32_Process

$lijst = Get-WmiObject -class Win32_Process
$lijst.Count # alle instanties

Get-WmiObject -Class Win32_Process -List # interessant en wildcards!

# sneller
Get-WmiObject -List Win32_Process

Get-WmiObject -List Win32_*Event

```

## The perl way

```
$location = new-object -comobject "wbemScripting.SwbemLocator"
$wmiservice = $location.ConnectServer(".","root\cimv2")
$klasse = $wmiservice.get("Win32_LogicialDisk")
$instantie = $wmiservice.get("Win32_LogicialDisk.DeviceID='C:'")


$klasse.Qualifiers_ | select Name, Value

$klasse.Qualifiers_.Item("abstract") # gaat fout
$klasse.Qualifiers_.Item("dynamic").Value


## GEBRUIK WHERE om geen fouten te zien


$klasse.Qualifiers_| Where{$_.Name -eq "abstract"}
$klasse.Qualifiers_| Where{$_.Name -eq "dynamic"}

# Description staat er niet bij!

$klasse = $wmiservice.get("Win32_logicalDisk", 131072)

# da nummer haal je uit typelibrary !

$instantie.SystemProperties_ | select Name, Value

$klasse.SystemProperties | select Name, Value

$instantie.SystemProperties_+$instantie.Properties

$instantie.Properties_.Item("size").Value
$instantie.Properties_ | where{$_.Name -eq "size"} | select Value

# voor geen fout als het niet bestaat!



$method = $klasse.Methods_ | select -f 1

$method.Name

$method.InParameters.Properties_.Count 

```








