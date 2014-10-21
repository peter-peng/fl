Title: Mutt 一个通过终端发送邮件的Email客户端
Date: 2014-03-31 13:59
Author: neoyin
Category: 技术流
Tags: email, linux, mutt
Slug: linux-command-mutt

> As a System admin, sometimes we need to send mails to users or
someone else from the server and for that we used to go with a web based
interface to send email, is it really that handy ? Absolutely No.

作为一个系统管理员，有时我们需要发送邮件给客户或者服务器上的其他人，这时我们会使用基于web的邮件系统来发送邮件，这是不是最便捷的方法呢？绝对不是。

> Here in this tutorial, we’ll be using the mutt (a terminal email
> client) command to send email from command line interlace.

在这篇教程里，我们将使用mutt(一个终端email客户端)命令来从命令行发送邮件。

> ### What is Mutt?
>
> Mutt is a command line based Email client. It’s a very useful and
> powerful tool to send and read mails from command line in Unix based
> systems. Mutt also supports POP and IMAP protocols for receiving
> mails. It opens with a coloured interface to send Email which makes it
> user friendly to send emails from command line.

Mutt是一个基于命令行的Email客户端。它是基于Unix的系统上发送和阅读邮件的十分强大而有用命令。Mutt也支持用POP和IMAP协议接收邮件。它提供了一个彩色的界面使得从命令行发送邮件更加友好。

<!--more-->

### Mutt Features

> Some other important features of Mutt is as follows:

-   Its very Easy to install and configure.
-   Allows us to send emails with attachments from the command line.
-   It also has the features to add BCC (Blind carbon copy) and CC
    (Carbon copy) while sending mails.
-   It allows message threading.
-   It provides us the facility of mailing lists.
-   It also support so many mailbox formats like maildir, mbox, MH and
    MMDF.
-   Supports at least 20 languages.
-   It also support DSN (Delivery Status Notification).

Mutt包含以下主要特性：

-   易于安装和配置
-   可以从命令行发送带附件的邮件
-   支持在发送邮件时加入密送和抄送
-   支持消息会话
-   可以显示邮件列表
-   支持多种邮箱格式，例如maildir，mbox，MH和MMDF
-   支持至少20种语言
-   支持DSN(Delivery Status Notification，投递状态通知)

如何在Linux上安装Mutt

我们可以在Linux上非常容易的使用任意包安装器安装Mutt客户端，像下面这样。

    apt-get install mutt (For Debian / Ubuntu based system)
    yum install mutt (For RHEL / CentOS / Fedora based system)

### Configuration files

> Configuration files of Mutt Email client.

-   Main Configuration file: To make changes globally for all users For
    mutt, you can make changes in its mail configuration file
    “/etc/Muttrc“.
-   User Configuration file of Mutt : If you want to set some specific
    configuration for a particular user for Mutt, you can configure
    those settings in \~/.muttrc or \~/.mutt/muttrc files.

Mutt Email客户端的配置文件：

-   Main Configuration
    file:改变是全局的，会影响所有用户的mutt，你可以通过它的邮件配置文件”/etc/Muttrc”进行更改。
-   User Configuration file of
    Mutt：如果你想为特定用户定制Mutt，你可以通过设置\~/.muttrc或者\~/.mutt/muttrc做到这一点。

### Send an email with mutt command

In this example, following command will send a test Email to
john@tecmint.com. The “-s” option is used to specify Subject of the
mail.

在这个例子里，下面的命令将会发送一封测试邮件到john@tecmint.com。”-s”选项用于指定邮件的主题。

`[root@tecmint ~]#  mutt -s "Test Email" john@tecmint.com`

### Add Carbon copy(Cc) and Blind Carbon copy(Bcc)

> We can add Cc and Bcc with mutt command to our email with “-c” and
> “-b” option.

我们可以通过”-c”和”-b”选项添加抄送和密送。

    [root@tecmint ~]# mutt -s "Subject of mail" -c <email add for CC> -b <email-add for BCC> mail address of recipient
    [root@tecmint ~]# mutt -s “Test Email” -c tecmint.com@gmail.com  -b root@server1.tecmint.com john@server1.tecmint.com

### Send Emails with Attachments

> We can send email from command line with attachments by using “-a”
> option with mutt command.

    [root@tecmint ~]# mutt  -s "Subject of Mail" -a <path of  attachment file> -c <email address of CC>  mail address of recipient
    [root@tecmint ~]# mutt -s "Site Backup" -a /backups/backup.tar  -c tarunika@centos58server.example.com root@centos58server.example.com

### Use of muttrc file

> If we want to change the senders name and email, then we need to
> Create a file in that particular user’s home directory.

如果我们想改变发送人的名字和地址，我们需要在指定的用户home目录下创建一个文件。

    [root@tecmint ~]# cat .muttrc
    Add the following lines to it. Save and close it.
    set from = "user@domain.com"
    set realname = "Realname of the user"

### Getting Help

> To print the help menu of “mutt”, we need to specify “-h” option with
> it.

    [root@tecmint ~]# mutt -h

    Mutt 1.4.2.2i (2006-07-14)
    usage: mutt [ -nRyzZ ] [ -e  ] [ -F  ] [ -m  ] [ -f  ]
           mutt [ -nx ] [ -e  ] [ -a  ] [ -F  ] [ -H  ] 
           mutt [ -i  ] [ -s  ] [ -b  ] [ -c  ]  [ ... ]
           mutt [ -n ] [ -e  ] [ -F  ] -p -v[v]
    options:
      -a      attach a file to the message
      -b 
      specify a blind carbon-copy (BCC) address
        -c 
        specify a carbon-copy (CC) address
          -e   specify a command to be executed after initialization
          -f      specify which mailbox to read
          -F      specify an alternate muttrc file
          -H      specify a draft file to read header from
          -i      specify a file which Mutt should include in the reply
          -m      specify a default mailbox type
          -n            causes Mutt not to read the system Muttrc
          -p            recall a postponed message
          -R            mailbox in read-only mode
          -s      specify a subject (must be in quotes if it has spaces)
          -v            show version and compile-time definitions
          -x            simulate the mailx send mode
          -y            select a mailbox specified in your `mailboxes' list
          -z            exit immediately if there are no messages in the mailbox
          -Z            open the first folder with new message, exit immediately if none
          -h            this help message
        

    <p>
      这就是mutt的命令了，阅读mutt的man page来获取更多mutt命令信息。
    </p>



    <p>
      <a href="http://www.tecmint.com/send-mail-from-command-line-using-mutt-command/">http://www.tecmint.com/send-mail-from-command-line-using-mutt-command/</a>
    </p>
