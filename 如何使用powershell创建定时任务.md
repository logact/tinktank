#powershell
# 步骤
-   Action： 执行一个powershell脚本，使用命令[New-ScheduledTaskAction]
-   Principal (Account)：指定任务以系统账户，最高权限执行，使用命令[New-ScheduledTaskPrincipal]
-   Trigger：任务每天早上3点执行，并创建后的下一天开始，使用命令[New-ScheduledTaskTrigger]
-   Settings：如果任务执行时间超过30分钟，将会被终止，使用命令[New-ScheduledTaskSettingSet]
# 示例
定时使用gitsync命令同步笔记数据
1. 创建Action
```powershell
$Params = @{
 "Execute"  = "powershell.exe"
 "Argument" = "gitsync"
}
 
$Action = New-ScheduledTaskAction @Params
```

2. 创建Principla
使用管理员权限运行
```PowerShell
$Params = @{
 "UserID"    = "NT AUTHORITY\\SYSTEM"
 "LogonType" = 'ServiceAccount'
 "RunLevel"  = 'Highest'
}
 
$Principal = New-ScheduledTaskPrincipal @Params
```
3. 任务设置
-   `ExecutionTimeLimit` - 任务运行最大时长，超过该值及时任务未完成也将被终止，本例设置为30分钟
-   `AllowStartIfOnBatteries` - 笔记本在使用电池为电源时，是否运行
-   `DontStopIfGoingOnBatteries` - 切换电源为电池，是否停止任务
-   `RestartCount` - 任务运行失败后重试次数，本例设置为2次
-   `RestartInterval` - 任务运行失败后再次重试间隔，本例设置间隔5分钟
```powershell
$Params = @{
 "ExecutionTimeLimit"         = (New-TimeSpan -Minutes 30)
 "AllowStartIfOnBatteries"    = $True
 "DontStopIfGoingOnBatteries" = $True
 "RestartCount"               = 2
 "RestartInterval"            = (New-TimeSpan -Minutes 5)
}
 
$Settings = New-ScheduledTaskSettingsSet @Params
```
4. 创建触发条件
```powershell
$Params = @{
 "Daily" = $True
 "At"    = (Get-Date '3 AM').AddDays(1)
}
$Trigger = New-ScheduledTaskTrigger @Params
```
5. 创建任务
```powershell
$Params = @{
 "Action"    = $Action
 "Principal" = $Principal
 "Trigger"   = $Trigger
 "Setting"   = $Settings
}
 
$Task = New-ScheduledTask @Params
$Task | Register-ScheduledTask -TaskName 'gitsyncDaily'
```

6.  启用和禁用Task
```powershell
Get-ScheduledTask -TaskName 'gitsyncDaily' | Enable-ScheduledTask
Get-ScheduledTask -TaskName 'gitsyncDaily' | Start-ScheduledTask
Get-ScheduledTask -TaskName 'gitsyncDaily' | Stop-ScheduledTask
```

整体文件
```powershell
$Params = @{
 "Execute"  = "powershell.exe"
 "Argument" = "gitsync"
}
  
$Action = New-ScheduledTaskAction @Params  
  
#$Params = @{  
#    "UserID"    = "NT AUTHORITY\\SYSTEM"  
#    "LogonType" = 'ServiceAccount'  
#    "RunLevel"  = 'Highest'  
#}  
  
#$Principal = New-ScheduledTaskPrincipal @Params  
  
$Params = @{  
    "ExecutionTimeLimit"         = (New-TimeSpan -Minutes 30)  
    "AllowStartIfOnBatteries"    = $True  
    "DontStopIfGoingOnBatteries" = $True  
    "RestartCount"               = 2  
    "RestartInterval"            = (New-TimeSpan -Minutes 5)  
}  
  
$Settings = New-ScheduledTaskSettingsSet @Params  
  
$Params = @{  
    "Daily" = $True  
    "At"    = (Get-Date '3 AM').AddDays(1)  
}  
$Trigger = New-ScheduledTaskTrigger @Params  
  
  
$Params = @{  
    "Action"    = $Action  
    "Trigger"   = $Trigger  
    "Setting"   = $Settings  
}  
  
$Task = New-ScheduledTask @Params  
$Task | Register-ScheduledTask -TaskName 'gitsyncDaily'


```

# ref
[New-ScheduledTaskPrincipal (ScheduledTasks) | Microsoft Learn](https://learn.microsoft.com/en-us/powershell/module/scheduledtasks/new-scheduledtaskprincipal?view=windowsserver2022-ps)

# 直接使用windows 界面创建
控制面板-》搜索任务控制
![[Pasted image 20230416223453.png]]