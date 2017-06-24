+++
date = "2017-06-18T16:03:04-04:00"
description = ""
draft = false
tags = ["iis", "https", "tls", "ssl", "powershell"]
title = "IIS Self Signed TLS/SSL Certificate"
topics = ["development", "scripts"]

+++

{{< highlight powershell >}}
$certFriendlyName = "Localhost Self-Signed"
$hostnames = @(
    "localhost",
    [System.Net.Dns]::GetHostName(),
    ([System.Net.Dns]::GetHostName() + ".mydomain.local")
)

$crt = New-SelfSignedCertificate -DnsName $hostnames -CertStoreLocation Cert:\LocalMachine\My\ -FriendlyName $certFriendlyName -NotAfter (Get-Date).AddYears(10)
$tmpFile = New-TemporaryFile
$crtFile = Export-Certificate -Cert $crt -Type CERT -FilePath $tmpFile
$crt = Import-Certificate -FilePath $crtFile.FullName -CertStoreLocation Cert:\LocalMachine\Root
$crt.FriendlyName = $certFriendlyName
Start-Process -FilePath (Join-Path $env:windir "\system32\inetsrv\InetMgr.exe")
Write-Host "Now setup the binding in IIS with the certificate named: " $certFriendlyName
Remove-Item $tmpFile
{{< /highlight >}}
