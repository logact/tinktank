---
share: true
---
#powershell
## 步骤
1. 输出profile位置
``` powershell
echo $PROFILE
```
2. 创建1的profile文件，写入自定义i脚本命令
```powershell
function gitsync {

    param (

        $dir

    )

    if(!$dir){

        $dir = "D:/logact/tinktank"

    }

    $commitMsg = "sync " + $dir

    Set-Location $dir

    git add .

    git commit -m $commitMsg

    $remotes = git remote

    foreach($remote in $remotes){

        git push $remote master        

    }    

}
```
## 帮助
1. 编写profile可以不用重启电脑（但是需要重新开启shell窗口）
2. 可以使用vscode（不知道idea是否也可以）进行debug