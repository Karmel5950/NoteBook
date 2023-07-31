对于编译出的程序，在 cmd 和 power shell 运行时都不能正确显示汉字。
网上查，可以再命令窗口修改：
1、打开CMD.exe命令行窗口
2、通过 chcp命令改变代码页，UTF-8的代码页为65001
chcp 65001
执行该操作后，代码页就被变成UTF-8了。

在当前窗口的确可以解决问题，但是重新打开的cmd窗口或者power shell 窗口仍然不能正确显示汉字。


最终发现，cmd的属性设置可以在注册表中修改，实现一次修改永远有效：
1. win+R 输入regedit 进入注册表
2. 找到 `HKEY_CURRENT_USER\Console\%SystemRoot%_system32_cmd.exe` 如果 该项下已存在CodePage项，则把值改为十进制”65001”；如果不存在，在该项下新建一个 DWORD（32位值），命名为“CodePage”，值设为“65001”
3. 重启cmd后生效
4. 对于Power shell修改同样，只需在第2步修改
`%SystemRoot%_system32_WindowsPowerShell_v1.0_powershell.exe `下的项。