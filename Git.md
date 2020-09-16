### 如何将本地文件上传至GitHub项目中？

1. 在本地文件夹变成Git可管理的仓库

   ```
   git init
   ```

2. 检查Github中，setting 下的SSH and GPG keys，SSH keys是否添加本地C盘用户目录下.ssh/id_rsa.pub的内容。

3. 将远程仓库与本地仓库进行关联

   ```
   git remote add origin git@github.com:zingffeng/Learn.git
   ```

4. 将本地仓库文件添加至仓库

   ```
   git add . //全部文件
   ```

5. 将本地仓库文件提交至仓库

   ```
   git commit -m "注释内容"
   ```

6. 将本地库的所有内容推送到远程仓库

   ```
   git push -u origin master //由于新建的远程仓库是空的，所以要加上-u这个参数
   git push origin master  //再从本地库上传内容的时候使用此命令
   ```

