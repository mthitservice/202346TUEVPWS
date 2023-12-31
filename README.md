[bild1]: https://github.com/mthitservice/202346TUEVPWS/blob/main/202346%20T%C3%9CV%20PS%20T1.png "Big Picture"
# 202346TUEVPWS
## Powershell Kurs
Trainer: Michael Lindner

## Alle Labs zum auskopieren
https://github.com/MicrosoftLearning/AZ-040T00-Automating-Administration-with-PowerShell/tree/master/Instructions/Labs

## PowerShell Bookware

Ein Ebook als PDF - https://onedrive.live.com/?authkey=%21AJdUaNzW7L9yC18&cid=5A8D2641E0963A97&id=5A8D2641E0963A97%216929&parId=5A8D2641E0963A97%21832&o=OneUp

Lernpfad von Microsoft und das Buch dazu als E-Book:
https://learn.microsoft.com/en-us/powershell/scripting/learn/ps101/00-introduction?view=powershell-7.3

https://leanpub.com/powershell101

Für die Administration Exchange (Nicht nur Powershell) :

https://www.frankysweb.de/





## Das BigPicture ;)
![alt text][bild1]
## Die wichtigsten Repositorys
### Powershell Gallery
https://www.powershellgallery.com/
### Nuget
https://www.nuget.org/
### Winget
https://winget.run/

>Zumstarten der Skriptaufzeichnung folgenden Skript benutzen:
``` powershell
Start-Transcript -Path "c:\transcripts\transcript0.txt" -NoClobber
```
Installation Winget
```
# get latest download url
$URL = "https://api.github.com/repos/microsoft/winget-cli/releases/latest"
$URL = (Invoke-WebRequest -Uri $URL).Content | ConvertFrom-Json |
        Select-Object -ExpandProperty "assets" |
        Where-Object "browser_download_url" -Match '.msixbundle' |
        Select-Object -ExpandProperty "browser_download_url"

# download
Invoke-WebRequest -Uri $URL -OutFile "Setup.msix" -UseBasicParsing

# install
Add-AppxPackage -Path "Setup.msix"

# delete file
Remove-Item "Setup.msix"

```

```
Get-ChildItem 'HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP' -Recurse | Get-ItemProperty -Name version -EA 0 | Where { $_.PSChildName -Match '^(?!S)\p{L}'} | Select PSChildName, version
```

```
 (Get-AppxPackage).where({$_.name -match 'Microsoft.UI.Xaml.2.7'}) 
 ```

 ```
 # Install NuGet
Install-Module PowerShellGet -Force
Register-PackageSource -provider NuGet -name nugetRepository -location https://www.nuget.org/api/v2

# Install Microsoft.UI.Xaml 
Install-Package Microsoft.UI.Xaml -RequiredVersion 2.7 -Force

Add-AppxPackage 'https://aka.ms/Microsoft.VCLibs.x64.14.00.Desktop.appx'

```

```
https://answers.microsoft.com/en-us/windows/forum/all/what-is-microsoftuixaml27-and-why-dont-i-have-it/9e5753be-3b5f-4975-ac00-a28344c710a6
```


>Installation Git über Winget
```
winget install -e --id Git.Git
```

>Installation Visual STudio Code
```
winget install -e --id Microsoft.VisualStudioCode
```
>Codesignaturzertifikat erstellen
```
New-SelfSignedCertificate -CertStoreLocation Cert:\LocalMachine\My -Subject "CN=YOURDOMAIN" -Signer $root -NotAfter (Get-Date).AddYears(3) -KeyLength 2048 -KeyUsage DigitalSignature -Type CodeSigningCert -KeyExportPolicy Exportable
```

>Transaktionen (Registry)
``` script
# BEISPIEL Transaktion starten und zurück rollen

C:\PS>cd hkcu:\software

PS HKCU:\software> start-transaction

PS HKCU:\software> new-item MyCompany -UseTransaction

PS HKCU:\software> new-itemproperty MyCompany -name MyKey -value 123 -UseTransaction

PS HKCU:\software> undo-transaction
```

``` script
# BEISPIEL Transaktion starten und ausführen

C:\PS>cd hkcu:\software

PS HKCU:\software> start-transaction

PS HKCU:\software> new-item MyCompany -UseTransaction

PS HKCU:\software> new-itemproperty MyCompany -name MyKey -value 123 -UseTransaction

PS HKCU:\software> complete-transaction
```

``` script
# BEISPIEL Transaktion starten, überprüfen und ausführen

C:\PS>cd hkcu:\software

PS HKCU:\software> start-transaction

PS HKCU:\software> new-item MyCompany -UseTransaction

PS HKCU:\software> new-itemproperty MyCompany -name MyKey -value 123 -UseTransaction

PS HKCU:\software> complete-transaction
```

``` script
# BEISPIEL Transaktion starten, überprüfen und ausführen

C:\PS>cd hkcu:\software

PS HKCU:\software> start-transaction

PS HKCU:\software> new-item MyCompany -UseTransaction

PS HKCU:\software> new-itemproperty MyCompany -name MyKey -value 123 -UseTransaction

PS HKCU:\software> get-transaction

PS HKCU:\software> complete-transaction
```

>Beispielprojekt
```
# Passwort generieren
#$c=Get-Credential # Passwort erfragen
#$c  |Export-Clixml -Path 'C:\tuev\p1\pwd.xml' # in Datei speichern

# Passwort generieren mit Nutzer und Computerbezug
#$c=Get-Credential # Passwort erfragen
$rootpath='C:\tuev\p1\'

$daten= $rootpath + 'Cred_' + $env:USERNAME + '_' + $env:COMPUTERNAME +'.xml'
#$c  |Export-Clixml -Path $daten # in Datei speichern

#Laden der Passwort und Nutzerdaten
$c=Import-Clixml -Path $daten

# Computernamen laden
$MyComputer=Import-csv -Path $rootpath'Computerlist.csv'

Write-Host "Contakt mit Computern aufnehmen"
# Security Logs aller Computer der Liste auslesen in einer Datei zusammenfassen
$MyComputer | ForEach-Object {


Write-Host "Computer:" $_.Computername
$werte=Invoke-Command -ComputerName $_.Computername -ScriptBlock {Get-EventLog Security 4625,4720,4722,4725 -Newest 10 }
# Exportieren in CSV
$werte | Select-Object EventId,Time  | Export-Csv $rootpath'Securitylog.csv'

}
```
