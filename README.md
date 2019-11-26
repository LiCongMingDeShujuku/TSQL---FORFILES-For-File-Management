![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# TSQL和FORFILES用于文件管理
#### TSQL & FORFILES For File Management
**发布-日期: 2018年07月19日 (评论)**

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
这是根据持续时间（天数）在多个驱动器中移动或删除备份文件（或任何文件）的一种方法。
你可以使用Powershell或Robocopy，但是这里使用的是TSQL。这些全部放入代理作业并进行测试。
有些人可能会认为这是使用多个FORFILES或堆叠FORFILES语句，但它是从TSQL打包并执行的基本命令shell操作。有助于平滑文件自动化过程。

有一种方法可以通过跨命令shell语句的切换来完成，但是使用基于TSQL的解决方案并从XP_CMDSHELL运行该解决方案意味着你可以从任何表上的任何位置提取路径列表，并从中创建自己的forfiles脚本

在下面的示例中，你可以看到我只是创建了一个表变量，并在不同的驱动器中插入了几个FORFILES语句。根据你在BACKUP MEDIA FAMILY表下找到的内容并仅使用物理设备位置将该路径合并到FORFILES脚本中来创建此表变量。

如果要更改保留文件的天数，则需要将脚本的最后2个字符从-0更改为-30，或者将文件保留在磁盘上。下面示例如何立即删除文件。

## English
Here’s yet another way to get your backup files (or any files) moved or deleted across multiple drives based on a duration (in number of days).
You can use Powershell, or Robocopy sure; but here is the same thing using TSQL. This was all placed into an Agent Job and tested.
Some might consider this as using multiple FORFILES or stacking FORFILES statements but it’s a basic command shell operation packaged and executed from TSQL. Really helps smooth out your file automation processes.

There’s a way this can be done with switches across command shell statements sure; but using a TSQL based solution and running that from XP_CMDSHELL means you can pull a list of paths from just about anywhere on any table, and create your own forfiles script from them.

In the example below you can see I simply created a table variable and inserted several FORFILES statements across different drives. There’s no reason at all why this table variable can’t be created based on what you might find under the BACKUP MEDIA FAMILY table and just using the physical device location and incorporating that path into the FORFILES script.


If you want to change the number of days to keep the file all you need to do is change the last 2 characters of the script from -0 to -30 or however long you intend to save keep the files on disk. The example below deletes the files immediately.
---

![#](images/tsql-and-forfiles-for-file-management-in-sql-servers.png?raw=true "#")


## Logic
```SQL
use master;
 
set nocount on
 
declare @forfiles table ([statements] varchar(255))
 
insert into @forfiles select '''FORFILES /p "K:BACKUPS" /s /m *.* /c "cmd /c Del @path" /d -0'''
 
insert into @forfiles select '''FORFILES /p "R:BACKUPS" /s /m *.* /c "cmd /c Del @path" /d -0'''
 
insert into @forfiles select '''FORFILES /p "T:BACKUPS" /s /m *.* /c "cmd /c Del @path" /d -0'''
 
insert into @forfiles select '''FORFILES /p "V:BACKUPS" /s /m *.* /c "cmd /c Del @path" /d -0'''
 
declare @command varchar(max)
 
set @command = ''
 
select @command = @command + 'exec master..xp_cmdshell ' + [statements] + char(10)
 
from @forfiles
 
exec (@command)


```

使用相同的框架，你可以使用任何其他命令shell实用程序。在下面的示例中，我使用相同的逻辑运行一些XCOPY命令将文件移动到其他备用备份文件夹。

Using the same framework you can use just about any other command shell utility. In the example below I used the same logic to run some XCOPY commands to move the files around to other alternate backup folders.

```SQL
use master;
 
set nocount on
 
declare @xcopy table ([statement] varchar(255))
 
insert into @xcopy select '''XCOPY "Y:BACKUPS*.bak" "K:BACKUPS"'''
 
insert into @xcopy select '''XCOPY "Y:BACKUPS*.bak" "R:BACKUPS"'''
 
insert into @xcopy select '''XCOPY "Y:BACKUPS*.bak" "T:BACKUPS"'''
 
insert into @xcopy select '''XCOPY "Y:BACKUPS*.bak" "V:BACKUPS"'''
 
declare @command varchar(max)
 
set @command = ''
 
select @command = @command + 'exec master..xp_cmdshell ' + [statement] + char(10)
 
from @xcopy
 
exec (@command)


```



[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

