# 在WSL中，git status显示所有文件已修改的问题

在Windows10中，使用WSL(Windows Subsystem for Linux)来开发项目，这就会出现在WSL中，git status显示所有文件都已修改的问题。通过Google找到问题所在，大概有两个原因：

* 由于windows下的权限和Linux的权限不一致，导致的git检测文件状态变更，需要我们关闭WSL中git的文件权限检测：

```bash
# 全局设置
git config --global core.filemode false
```

* Windows中使用的文本换行方式是CRLF，Linux中使用的文本换行方式是LF，然后WSL是Linux环境，默认使用LF换行方式，所以git会自动将代码当中与WSL系统不同的换行方式转化成WSL换行方式，从而状态发生变动。需要我们禁止WSL自动转换文本换行模式：

```bash
# 全局设置
git config --global core.autocrlf true
```
