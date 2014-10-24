Title: 安装Nodejs 和 npm
Date: 2013-12-27 17:41
Author: chengz
Category: 技术流
Tags: nodejs
Slug: nodejs-npm-install


这里 http://www.joyent.com/blog/installing-node-and-npm/ 和这里
https://gist.github.com/isaacs/579814 主要就是这里

    `echo 'export PATH=$HOME/local/bin:$PATH' >> ~/.bashrc
    . ~/.bashrc
    mkdir ~/local
    mkdir ~/node-latest-install
    cd ~/node-latest-install
    curl http://nodejs.org/dist/node-latest.tar.gz | tar xz --strip-components=1
    ./configure --prefix=~/local
    make install # ok, fine, this step probably takes more than 30 seconds...
    curl https://npmjs.org/install.sh | sh`

