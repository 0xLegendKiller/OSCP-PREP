# Notes
Powershell is the Windows Scripting Language and shell environment that is built using the .NET framework.

This also allows Powershell to execute .NET functions directly from its shell. Most Powershell commands, called cmdlets, are written in .NET. Unlike other scripting languages and shell environments, the output of these cmdlets are objects - making Powershell somewhat object oriented. This also means that running cmdlets allows you to perform actions on the output object(which makes it convenient to pass output from one cmdlet to another). The normal format of a cmdlet is represented using Verb-Noun; for example the cmdlet to list commands is called Get-Command.

# the command to get help about a particular cmdlet.
---> Get-Help

# list all examples
Get-Help Get-Command -examples

# locating a file
Get-ChildItem -Path C:/ -Name *interesting-file.txt* -Recurse -File

# some commands
Get-Location
Get-FileHash
Invoke-WebRequest
certutil -decode file out.txt
