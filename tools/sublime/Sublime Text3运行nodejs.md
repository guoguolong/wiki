Tools -> Build System -> New Build System

```json
{
    "cmd": ["node", "$file"],
    "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
    "selector": "source.js",
    "shell": true,
    "encoding": "utf8",
    "windows": {
        "cmd": ["taskkill","/F", "/IM", "node.exe","&","node", "$file"]
    },
    "linux": {
        "cmd": ["killall node; node", "$file"]
    },
    "osx":{
        "cmd": ["killall node; /usr/local/bin/node $file"]
    }
}
```