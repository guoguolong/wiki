macOS下，我用brew安装python3

Sublime菜单选择：Tools -> Build System -> New Build System

```json
{
    "env": {"PYTHONIOENCODING": "utf8"},
    "cmd": ["/usr/local/bin/python3", "-u", "$file"],
    "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
    "selector": "source.python"
}
```

存储为 python3.sublime-build

编写一个 .py 代码文件，按 COMMAND + B