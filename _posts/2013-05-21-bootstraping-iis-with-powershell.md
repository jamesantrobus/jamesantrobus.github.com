---
layout: post
category : powershell
tags : [iss, powershell]
title : "Bootstrapping IIS with PowerShell"
---

One thing I dislike about using IIS during development is checking out a .NET solution with a few web projects in and having them all fail to load because the sites arn't setup on my local instance.

You can do the XML export/import but it isn't great and won't setup app pools.

Personally I have a little project convention where I have a PowerShell script named bootstrap.ps1 partly inspired by the scripts described in this [Unsucking your team's development environment][1] talk. I add one to the root of every repository that I (and hopefully others) know to look for.

{% highlight powershell %}
Set-ExecutionPolicy RemoteSigned
Import-Module WebAdministration

Function bootstrap($siteName, $hostHeader, $sitePath, $pipelineMode)
{
    $site = Get-WebSite | where { $_.Name -eq $siteName }
    if($site -eq $null)
    {
        echo "Creating site: $siteName"

        $appPool = New-WebAppPool -Name $siteName -Force
        $appPool | Set-ItemProperty -Name "managedPipelineMode" -Value $pipelineMode
       
        $absoluteSitePath = Resolve-Path $sitePath
        New-Website -Name $siteName -Port 80 -PhysicalPath $absoluteSitePath 
            -ApplicationPool $siteName -HostHeader $hostHeader
    
        "127.0.0.1" + "`t`t" + $hostHeader | Out-File -encoding ASCII
            -append "C:\Windows\System32\drivers\etc\hosts"
    }
}

bootstrap "Site1" "site1.dev" "Solution\Site1" Integrated
bootstrap "Site2" "site2.dev" "Solution\Site2" Classic
{% endhighlight %}

This lets me add a site and app pool for each site in the solution and set the name, default binding and app pool pipeline mode. I'm also also adding my bindings (site1.dev/site2.dev) to my hosts file. Obviously you can omit a few bits depending on your needs.

[1]: http://zachholman.com/talk/unsucking-your-teams-development-environment/