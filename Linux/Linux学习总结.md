## Linux学习总结

- **shell命令格式：**
```
$ command  [-options]  parameter1  parameter2 ...
```
  - 命令最开始位置肯定是`command`或可执行档案（如批次脚本，shell script）
  - `command`为指令名称，如`cd`
  - 中括号[]为可选。加入选项设定时，通常`-`号加选项缩写，如`-h`；也可以`--`号加选项全称，如`--help`
  - `parameter1`和`parameter2`是依附在选项后的参数，或是`command`的参数
  - 指令太长时可以用反斜杠`\`号直接跟回车来跳到下一行继续输入，其实就是转义符，把回车`Enter`转义
  - 区分大小写

- **`man`的一些memo:**
  - Mac的**zsh**的上下跳跃（不算翻页，但比`Up/Down`跳跃范围更大）：`D`下跳，`U`上跳
  - 翻页：`空格`键；`Page Up`/`Page Down`翻页
  - 退出：`Q`键
  - **搜索功能：**按下`/`键（向下搜索）或`?`键（向上搜索），然后输入需要搜索的文字。如：`/open`是搜索"open"。`n`键是跳到下一个match，`N`键则是跳到上一个
