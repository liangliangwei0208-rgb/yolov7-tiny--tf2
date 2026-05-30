# AGENTS.md

这个是笔记本电脑，和主机电脑一致，用来撰写或修改代码。

## 文件操作规则

禁止批量删除文件或目录。

不要使用：

- `del /s`
- `rd /s`
- `rmdir /s`
- `Remove-Item -Recurse`
- `rm -rf`

需要删除文件时，只能一次删除一个明确路径的文件。

正确示例：

```powershell
Remove-Item "C:\path\to\file.txt"
```

如果需要批量删除文件，应停止操作，并向用户请求，让用户手动删除。

## 文档同步

每次对话结束，如果需要，同步更新 README 和 AGENTS 文件。

## 代码注释

撰写代码要有一定的中文注释，方便个人理解。
