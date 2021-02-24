## Import multiple Hosts into Rubrik CDM via PowerShell

A while ago i have to import multiple Hosts into a <a rel="noreferrer noopener" href="http://www.rubrik.com/" target="_blank">Rubrik CDM</a> Cluster. So i took the chance, jumped into PowerShell and wrote a small and very simple Script, which reads out an CSV-File containing the Hosts and then triggers the Rubrik-API. 

To run this script you need the PowerShell Module created by Chris Wahl. You can download the module here <a rel="noreferrer noopener" href="https://www.powershellgallery.com/packages/Rubrik/5.0.1" data-type="URL" data-id="https://www.powershellgallery.com/packages/Rubrik/5.0.1" target="_blank">Rubrik PowerShell Module</a>. Meanwhile it's also released in the PowerShell Gallery, so you can install the latest module with the following command.

```powershell 
PS> Install-Module -Name Rubrik
```

Below the commented script for usage. You have to create an CSV-File with your Hosts, each line a host, change Cluster-IP and Admin-Username and give it a shot. This script is a bit rusty and will be revised soon.

```powershell
$ConfirmPreference = "None"
$ErrorActionPreference = 'SilentlyContinue'
Start-Transcript -Path "C:\RubrikImport_$(get-date -f yyyy-MM-dd-hh-mm-ss).txt"
### Kompatibilitätsprobleme mit lokalen Rechten umgehen
Get-ChildItem -Path 'C:\Program Files\WindowsPowerShell\Modules\Rubrik\' -Recurse | Unblock-File 
### Modulfunktionen für Rubrik importieren
Import-Module Rubrik
### Verbinden mit Rubrik Appliance
Connect-Rubrik -Server [Cluster-IP] -Username [Admin-Username]
### Einlesen der CSV-Datei mit den anzulegenden Hosts
$input = Import-Csv "C:\Import.csv" -Delimiter ";" 
### Für jedes Objekt / jede Zeile in der CSV Datei wird die Schleife durchlaufen
foreach ($hostdata in $input)
{ 
### Es wird auf dem Rubrik Cluster geprüft, ob der jeweilige Host existiert, tut er dies nicht, wird der Host angelegt  
    if (Get-RubrikHost -Hostname $hostdata.Title | Where-Object {$_.hostname -ne $null})
        {
            Write-Host "Host" $hostdata.Title "bereits angelegt."
        }
    else 
        {
            Write-Host "Host" $hostdata.Title "wird angelegt."
            New-RubrikHost -Name $hostdata.Title -HasAgent $false
        }
Stop-Transcript
```

You can find more Rubrik PowerShell Commands here

<a href="https://powershell-module-for-rubrik.readthedocs.io/en/latest/">https://powershell-module-for-rubrik.readthedocs.io/en/latest/</a>








