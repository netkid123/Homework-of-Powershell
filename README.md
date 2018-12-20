# Homework-of-Powershell
powershell codes of my blog.

### Invoke-Enumeratefile.ps1

Enumerate all the files under `c:\Windows` that the permission of NT AUTHORITY\SYSTEM is full control.

We can use the task scheduler to write an arbitrary DACL to the file and then we can modify the files with normal user permissions.

This script will enumerate all the files you can take control over.

### Invoke-LibraryFilesPersistence.ps1

First modify the registry and then create a file named Documents.library-ms in %appdata%\Microsoft\Windows\Start Menu\Programs\Accessories.

It'll load c:\test\calc.dll on start-up.

### Delete-Amcache.ps1

This script loads the AMCache hive from the default Windows location and delete the seleted data.
You can use Get-Amcache.ps1 to list the data of AMCache hive,then use this to delete the seleted data.
The new AMCache hive will be saved as new.hve

Get-Amcache.ps1:https://github.com/yoda66/GetAmCache/blob/master/Get-Amcache.ps1

### New-GPOImmediateTask.ps1

Builds an 'Immediate' schtask to push out through a specified GPO.

Because I haven't found out how to register the 'Immediate' schtask yet.
        
So I have to backup the gpo,then modify the Backup.xml and gpreport.xml,and finally import the gpo.
                
        (1)Create a gpo
            new-gpo -name TestGPO | new-gplink -Target "dc=test,dc=com"
        (2)Backup the gpo and get the BackupId and GPOId
            $Backup = Backup-Gpo -Name TestGPO -Path C:\test1
            $BackupId = $Backup.Id
            $GPOId = $Backup.GpoId
        (3)Use New-GPOImmediateTask.ps1 to modify the Backup.xml and gpreport.xml,and finally import the gpo.   
            New-GPOImmediateTask -TaskName Debugging -BackupId 2dd64ebc-2b8b-403b-8b2d-8fd4c5711ba7 -BackupPath c:\test1 -GPOId 5fdfab7c-0bfe-4aec-983e-4d3db3003ce0 -SysPath '\\dc1.test.com\sysvol\test.com' -GPODisplayName TestGPO -CommandArguments '-c "123 | Out-File C:\test\debug.txt"'            
        (4)You can force the client to refresh the gpo:
            Invoke-GPUpdate -Computer "TEST\COMPUTER-01"
           Or you can wait 90 minutes,the client's gpo will refresh automatically. 
