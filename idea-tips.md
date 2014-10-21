Title: IDEA tips 
Date: 2013-11-22 08:53
Author: chengz
Category: 每日一译
Slug: idea-tips

20131122  
To quickly select the currently edited element (class, file, method or
field) in any view (Project view, Structure view or other), press Alt+F1
(View | Select In).  
在任意视图(Project视图, Structure视图 或其它)中, 按Alt+F1(View | Select
In)快速选中当前正在编辑的元素. oh y  
20131123  
The SmartType code completion may be used after the new keyword, to
instantiate an object of the expected type. For example, type  
StringBuffer buffer = new |  
and press Ctrl+Shift+Space:  

![image](http://e.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=15e617045b82b2b7a39f39c10196bad2/ac345982b2b7d0a2b8568b9dc9ef76094b369a17.jpg?referer=204c4a2f38292df5ced49925e60d&x=.jpg)

智能类型补全可以用在 new 关键词后面, 来实例化类型的一个对象. 例如: 输入  
StringBuffer buffer = new |  
然后按下 Ctrl + Shift + Space:  

![image](http://e.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=15e617045b82b2b7a39f39c10196bad2/ac345982b2b7d0a2b8568b9dc9ef76094b369a17.jpg?referer=204c4a2f38292df5ced49925e60d&x=.jpg)

To navigate to the implementation(s) of an abstract method, position the
caret at its usage or its name in the declaration and press Ctrl+Alt+B.  
要导航到一个抽象方法的实现, 把光标移到使用它的位置或声明的位置, 然后按
Ctrl+Alt+B.  
20131126  
If the cursor is between the parentheses of a method call, pressing
Ctrl+P brings up a list of valid parameters.

如果光标在方法调用的括号的中间时, 按 Ctrl+P 可以显示有效的参数名列表  
20131127  
Ctrl+Shift+Backspace (Navigate | Last Edit Location) brings you back to
the last place where you made changes in the code.  
Pressing Ctrl+Shift+Backspace a few times moves you deeper into your
changes history.

Ctrl+Shift+Backspace (Navigate | Last Edit Location)
回到你最后修改的位置.  
多按几次回到更深的修改历史.  
20131128  
Use<span style="color: #ff0000;"> Ctrl+Shift+F7</span> (**Edit | Find |
Highlight Usages in File**) to quickly highlight usages of some variable
in the current file.  
Use<span style="color: #ff0000;"> F3</span> and
<span style="color: #ff0000;">Shift+F3</span> keys to navigate through
highlighted usages.  
Press <span style="color: #ff0000;">Escape</span> to remove the
highlighting.

用 Ctrl+Shift+F7 (Edit | Find | Highlight Usages in File)
可以快速高亮显示当前文件中使用的变量.  
用 F3 和 Shift+F3 可以在高亮的位置之间切换. 按ESC清除高亮显示.  
20131129  
Use **Code | Reformat Code** to reformat code according to your code
style preferences (**File | Settings | Code Style**).  
You can also use**Code | Optimize Imports** to automatically optimize
imports (remove unused imports, etc.). To access the corresponding
settings, use **File | Settings | Code Style | Imports**.

Code | Reformat 可以根据你的代码样式参数设置(File | Settings | Code
Style)重新格式化代码.  
你也可以用 Code | Optimize Imports 来自动调整 imports ,(例如 移除不用的
imports). 相关设置在 File | Settings | Code Style | Imports 中.  
20131205  
<span style="color: #ff0000;">Ctrl+Shift+J</span> shortcut joins two
lines into one and removes unnecessary space to match your code style.

<span style="color: #ff0000;">Ctrl+Shift+J</span>
把两行合并为一行并且移除不需要的空格使之符合你的代码风格.  
20131209  
Use the <span style="color: #ff0000;">Ctrl+Shift+V</span> shortcut to
choose and insert recent clipboard contents into the text.

用 Ctrl＋Shilt＋V 可以选择剪贴板的内容并插入当前文本中  
20131211  
To easily evaluate the value of any expression while debugging the
program, select its text in the editor (you may press a
<span style="color: #ff0000;">Ctrl+W</span> a few times to efficiently
perform this operation) and press
<span style="color: #ff0000;">Alt+F8</span>.

调试时计算表达式的值，在编辑器中选中文本（你可以按几次Ctrl＋W来选择），然后按
Alt＋F8.  
20131216  
When using Code Completion, you can accept the currently highlighted
selection in the popup list with the period character (.), comma (,),
semicolon (;), space and other characters.  
The selected name is automatically entered in the editor followed by
the entered character.  

用代码补全的时候，你可以用句号(.)，逗号，分号，空格和其他字符来选中当前高亮的条目。被选中的内容自动插入编辑器中，你用来选择的字符紧随其后。  
20131217  
To help you learn the purpose of each item in the main menu, its short
description is shown in the status bar at the bottom of the application
frame when you position the mouse pointer over it.  

为了帮你学习菜单的用处，当用鼠标指向菜单时，它的简短描述会显示在程序窗口的下方。  
20131227  
If you notice that IntelliJ IDEA works slowly, consider the possibility
to reduce the number of folders under antivirus protection.  
Each antivirus check in your project consumes resources. You can
significantly improve the performance, if you exclude certain folders
from the antivirus protection.  
如果你发现IDEA运行得慢了, 试一下减少在反病毒保护下的目录数.
反病毒检查会占用资源. 指定一些目录不进行反病毒保护, 可以明显提高性能.
