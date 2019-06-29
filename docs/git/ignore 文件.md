### 关于`.gitignore`

1. .gitignore文件是必须的，应该在工程初始化的时候添加

2. 对于java对module管理的工程，.gitignore是按照路径忽略文件的，注意此时要忽略的文件相对于根目录还有一层module。例如忽略target文件，不是添加target/ 而是*/taget/

3. 如果文件已经add 进入缓存区，此时再增加对应的.gitignore是无效的，当.gitignore文件修改时，可以删除缓存重新add

   ```shell
   git rm -rf --cached . #删除缓存
   git add . # 重新add
   ```

   

