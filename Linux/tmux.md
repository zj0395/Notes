# tmux - 终端复用软件
- 关机后保存的会话会消失

## 基础
- `tmux attach-session -t session_name` 选择会话
- `tmux new-session -s session_name` 新建会话
- `tmux kill-session -t session_name` 删除会话
- `tmux ls` 显示已有会话

## 按键
- 详见 http://blog.csdn.net/hcx25909/article/details/7602935
- % 当前面板左右分
- " 当前面板上下分
- x 关闭当前面板
- 数字键 切换到指定窗口
- d 脱离会话, 回到 shell
- ? 列出所有快捷键, 按 q 返回