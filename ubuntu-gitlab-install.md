Title: Ubuntu搭建gitLab
Date: 2013-11-22 00:57
Author: neoyin
Category: 技术流
Tags: git, ubuntu
Slug: ubuntu-gitlab-install


最近项目更新频繁,而且多个团队同时开发同一个项目,开发阶段svn管控没有做过多限制,发布时需要非常小心,需要保证其它团队提交的代码是可发布状态.看了关于github等相关的资料,在代码管理上做一些推进.所以决定自己搭建一个私服.
**GitLab([http://gitlab.org/gitlab-ce/](http://gitlab.org/gitlab-ce/))**
GitLab是一个利用 Ruby on Rails
开发的开源应用程序，实现一个自托管的Git项目仓库，可通过Web界面进行访问公开的或者私人项目。
它拥有与Github类似的功能，能够浏览源代码，管理缺陷和注释。可以管理团队对仓库的访问，它非常易于浏览提交过的版本并提供一个文件历史库。团队成员可以利用内置的简单聊天程序(Wall)进行交流。它还提供一个代码片段收集功能可以轻松实现代码复用，便于日后有需要的时候进行查找。  
\* 仓库管理
在创建仓库的时候可以选择创建到群组空间下（类似于Github的Orgnization）；也可以到全局空间下，没有前缀；或者个人空间下，默认是私仓，可以选择公开。

-   公仓和私仓 (权限分配)
    每个仓库的权限可以单独分配，只需要设置为Master就可以对仓库有完全的操控权，如果需要过库到别人名下也是支持的。配置允许默认创建公仓还是私仓，自己创建的私仓只能自己和管理员看见。

-   Github工作流 （不清楚Github
    Flow的可以[点此](http://scottchacon.com/2011/08/31/github-flow.html)了解）
    Github工作流主要是使用Pull
    Requets的功能完成，没个人去提交自己的仓库，然后再去pull
    request来完成协作，gitlab也支持pull request。

-   团队和群组管理
    可以建立一个团队（比如公司的某个部门），把用户加进来，这些用户默认就有了访问团队下仓库的给定权限。群组和Github的Orgnizition类似，但又不完全一样。我一把把群组当作项目来用。注意：团队是没有仓库空间的，群组是有的。

<!--more-->

**安装准备**
[https://github.com/gitlabhq/gitlabhq/blob/master/doc/install/installation.md](https://github.com/gitlabhq/gitlabhq/blob/master/doc/install/installation.md)
1.先安装各种依赖包:  
sudo apt-get install -y build-essential zlib1g-dev libyaml-dev
libssl-dev libgdbm-dev libreadline-dev libncurses5-dev libffi-dev curl
openssh-server redis-server checkinstall libxml2-dev libxslt-dev
libcurl4-openssl-dev libicu-dev logrotate 2.确保系统中安装python
且版本最好为2.5+

    # Make sure that Python is 2.5+ (3.x is not supported at the moment)
    python --version

    # If it's Python 3 you might need to install Python 2 separately
    sudo apt-get install -y python2.7

    # Make sure you can access Python via python2
    python2 --version

    # If you get a "command not found" error create a link to the python binary
    sudo ln -s /usr/bin/python /usr/bin/python2

    # For reStructuredText markup language support install required package:
    sudo apt-get install -y python-docutils 3.安装ruby 

    可删除低版本
    sudo apt-get remove ruby1.8
    mkdir /tmp/ruby && cd /tmp/ruby
    curl --progress ftp://ftp.ruby-lang.org/pub/ruby/2.0/ruby-2.0.0-p247.tar.gz | tar xz
    cd ruby-2.0.0-p247
    ./configure --disable-install-rdoc
    make
    sudo make install
    #Install the Bundler Gem:
    sudo gem install bundler --no-ri --no-rdoc
     4.安装git-core 

    # Install Git
    sudo apt-get install -y git-core

    # Make sure Git is version 1.7.10 or higher, for example 1.7.12 or 1.8.4
    git --version 5.创建git用户 

    sudo adduser --disabled-login --gecos 'GitLab' git 6.安装gitLab shell 

    # Go to home directory
    cd /home/git

    # Clone gitlab shell
    sudo -u git -H git clone https://github.com/gitlabhq/gitlab-shell.git

    cd gitlab-shell

    # switch to right version
    sudo -u git -H git checkout v1.7.9

    sudo -u git -H cp config.yml.example config.yml

    # Edit config and replace gitlab_url
    # with something like 'http://domain.com/'
    sudo -u git -H editor config.yml

    # Do setup
    sudo -u git -H ./bin/install

7.数据库(mysql)

    CREATE DATABASE IF NOT EXISTS `gitlabhq_production` DEFAULT CHARACTER SET `utf8` COLLATE `utf8_unicode_ci`;
    GRANT SELECT, LOCK TABLES, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER ON `gitlabhq_production`.* TO 'gitlab'@'172.16.%' identified by '***';

8.安装gitlab并配置

    cd /data/git
    sudo -u git -H git clone https://github.com/gitlabhq/gitlabhq.git gitlab
    cd /data/git/gitlab
    # Checkout to stable release
    sudo -u git -H git checkout 6-2-stable
    # Copy the example GitLab config
    sudo -u git -H cp config/gitlab.yml.example config/gitlab.yml

    # Make sure to change "localhost" to the fully-qualified domain name of your
    # host serving GitLab where necessary
    #
    # If you installed Git from source, change the git bin_path to /usr/local/bin/git
    sudo -u git -H editor config/gitlab.yml

    # Make sure GitLab can write to the log/ and tmp/ directories
    sudo chown -R git log/
    sudo chown -R git tmp/
    sudo chmod -R u+rwX  log/
    sudo chmod -R u+rwX  tmp/

    # Create directory for satellites
    sudo -u git -H mkdir /home/git/gitlab-satellites

    # Create directories for sockets/pids and make sure GitLab can write to them
    sudo -u git -H mkdir tmp/pids/
    sudo -u git -H mkdir tmp/sockets/
    sudo chmod -R u+rwX  tmp/pids/
    sudo chmod -R u+rwX  tmp/sockets/

    # Create public/uploads directory otherwise backup will fail
    sudo -u git -H mkdir public/uploads
    sudo chmod -R u+rwX  public/uploads

    # Copy the example Unicorn config
    sudo -u git -H cp config/unicorn.rb.example config/unicorn.rb

    # Enable cluster mode if you expect to have a high load instance
    # Ex. change amount of workers to 3 for 2GB RAM server
    sudo -u git -H editor config/unicorn.rb

    # Copy the example Rack attack config
    sudo -u git -H cp config/initializers/rack_attack.rb.example config/initializers/rack_attack.rb

    # Configure Git global settings for git user, useful when editing via web
    # Edit user.email according to what is set in gitlab.yml
    sudo -u git -H git config --global user.name "GitLab"
    sudo -u git -H git config --global user.email "gitlab@localhost"
    sudo -u git -H git config --global core.autocrlf input

    #db config
    sudo -u git cp config/database.yml.mysql config/database.yml
    sudo -u git -H chmod o-rwx config/database.yml

1.  gem安装

<!-- -->

    cd /data/git/gitlab
    sudo -u git -H bundle install --deployment --without development test postgres aws

    #init db
    sudo -u git -H bundle exec rake gitlab:setup RAILS_ENV=production

    # Type 'yes' to create the database.

    # When done you see 'Administrator account created:'

    #Install Init Script
    sudo cp lib/support/init.d/gitlab /etc/init.d/gitlab
    sudo chmod +x /etc/init.d/gitlab

    #Make GitLab start on boot:
    sudo update-rc.d gitlab defaults 21

10.状态检查

    sudo -u git -H bundle exec rake gitlab:env:info RAILS_ENV=production

    System information
    System:     Ubuntu 13.10
    Current User:   git
    Using RVM:  no
    Ruby Version:   2.0.0p247
    Gem Version:    2.0.3
    Bundler Version:1.3.5
    Rake Version:   10.1.0

    GitLab information
    Version:    6.2.4
    Revision:   ab6b1f2
    Directory:  /data/git/gitlab
    DB Adapter: mysql2
    URL:        http://http://gitlab.floatinglife.cn
    HTTP Clone URL: http://http://gitlab.floatinglife.cn/some-project.git
    SSH Clone URL:  git@http://gitlab.floatinglife.cn:some-project.git
    Using LDAP: no
    Using Omniauth: no

    GitLab Shell
    Version:    1.7.9
    Repositories:   /home/git/repositories/
    Hooks:      /home/git/gitlab-shell/hooks/
    Git:        /usr/bin/git

https://github.com/gitlabhq/gitlabhq/blob/master/doc/install/installation.md

[http://blog.webnotes.cn/install-gitlab/](http://blog.webnotes.cn/install-gitlab/)
[https://gist.github.com/ETiV/5703650](https://gist.github.com/ETiV/5703650)
