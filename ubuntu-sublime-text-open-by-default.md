Title: Ubuntu下Sublime Text 2默认打开文本
Date: 2013-04-26 11:40
Author: neoyin
Category: 技术流
Tags: sublime, ubuntu
Slug: ubuntu-sublime-text-open-by-default


Ubuntu Sublime Text 2 是个不错的编辑神器。但是在Linux Sublime
Text并不是需要安装，直接解压即可使用，所以缺少Ubuntu桌面运行的一些基本配置。Ubuntu上也没有快捷方式，选择Open
with other Application 也找不到它。所以需要进行一些配置 先 cd 到
/usr/share/applications 目录下创建文件 sublime.desktop 配置代码如下：

    #!/usr/bin/env xdg-open
        [Desktop Entry]
        Name=Sublime Text 2
        Comment=Sublime Text 2
        Exec=/../${path}/sublime_text
        Icon=/../${path}/Icon/48x48/sublime_text.png
        Terminal=false
        Type=Application
        Categories=Application;Development;
        StartupNotify=true
    退出保存

-   Name:名称描述
-   Comment：相当于注释
-   Exec：可执行文件的路径
-   Icon：图标路径,需要绝对路径 编辑
    \~/.local/share/applications/mimeapps.list 文件

<!-- -->

    text/plain=sublime.desktop;gedit.desktop;
    退出保存

---

参考：

[](http://www.mypaomian.com/%E8%AE%BE%E7%BD%AEubuntu-%E9%BB%98%E8%AE%A4%E8%BD%AF%E4%BB%B6%E6%89%93%E5%BC%80%E6%96%B9%E5%BC%8F/)
[http://www.mypaomian.com/设置ubuntu-默认软件打开方式](http://www.ibm.com/developerworks/cn/linux/l-cn-dtef/)
[http://www.ibm.com/developerworks/cn/linux/l-cn-dtef/](http://www.ibm.com/developerworks/cn/linux/l-cn-dtef/)
