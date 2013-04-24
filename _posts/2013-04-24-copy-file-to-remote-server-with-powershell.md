---
layout: post
category : powershell
tags : [powershell]
title : "Copying a file to a remote server with PowerShell"
---

If you have a network share on a server then you can use the New-PSDrive cmdlet to copy files to a remote server. If you need to specify credentials for the remote server (who doesn't?) then you'll need [PowerShell 3][1] or it'll moan about the provider not supporting something or other.

{% highlight powershell %}
$user = "\myusername"
$pass = ConvertTo-SecureString -String "supersecretpassword" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential 
	-ArgumentList $user ,$pass

New-PSDrive -Name myshare -PSProvider FileSystem -Root \\remoteserver\some-share -Credential $cred
Copy-Item C:\Users\James\test.txt myshare:\
{% endhighlight %}

Without passing the -Persist parameter the drive mapping is only temporary. This is noteworthy for a few reasons:

* The mapping is removed when the PowerShell session ends. Persistent mappings can be removed with the Remove-PSDrive cmdlet
* The mapping name can be any valid string, not just a drive letter
* The mapping is only known to PowerShell so can't be used from Explorer or .NET

It's no scp, but it does the job.

See [MSDN][2] for more details.

[1]: http://www.microsoft.com/en-us/download/details.aspx?id=34595
[2]: http://technet.microsoft.com/en-gb/library/hh849829.aspx