#### vim

```bash
i:进入插入模式，光标前
I：行首
a：进入插入模式，光标后
A：行尾
```

##### 命令行模式

```
h 左移
j 下移
k 上移
l 右移
M：光标移动到中间行
L:光标移动到屏幕最后一行行首
G:移动到指定行，行号 -G
w:向后一次移动一个字
b：向前一次移动一个字
{:按段移动，上移
}:按段移动，下移
x:删除光标后一个字符
X:删除光标前一个字符
dd:删除光标所在行 n dd 删除指定行数
dw:删除光标开始位置的字，包含光标所在字符
u:撤销
ctrl  r:反撤销
.：重复上次的操作
yy:复制当前行 n yy 复制n行
p:粘贴
r:替换当前行光标后的字符
sp:分屏 (ctrl+w 切换)
vim -ON file1 file2 左右分屏 (ctrl w c 关闭一个分屏)
```
