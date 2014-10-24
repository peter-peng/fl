Title: Extjs的borderlayout实战代码
Date: 2013-12-01 00:11
Author: fuqiang
Category: 技术流
Slug: extjs-borderlayout-example

![image](http://www.floatinglife.cn/wp-content/uploads/2013/12/快照1.png "Extjs")

使用如下代码，你将创建出如上图所示的漂亮界面

    Ext.onReady(function(){
    var body = new Ext.Panel({
    layout: 'border',
    items: [
    {
    xtype: 'panel',
    region: 'north',
    height: 80,
    html: '<IFRAME ID="head_frame" width="100%" height="100%" FRAMEBORDER=0 SCROLLING=NO SRC="' + basePath +'head.jsp"></IFRAME>'
    }, {
    xtype: 'panel',
    region: 'south',
    height: 30,
    html: 'south'
    }, {
    xtype: 'panel',
    region: 'center',
    layout: 'border',
    tbar: [
    userName,
    {
    xtype: 'splitbutton',

    text: 'Split Button'
    },
    '->', // same as {xtype: 'tbfill'}, // Ext.Toolbar.Fill
    {
    xtype: 'textfield',
    name: 'field1',
    emptyText: 'enter search term'
    },
    // add a vertical separator bar between toolbar items
    '-', // same as {xtype: 'tbseparator'} to create Ext.Toolbar.Separator
    'text 1', // same as {xtype: 'tbtext', text: 'text1'} to create Ext.Toolbar.TextItem
    {xtype: 'tbspacer'},// same as ' ' to create Ext.Toolbar.Spacer
    {
    text: '退出',
    handler: logout
    },
    {xtype: 'tbspacer', width: 50}, // add a 50px space 'text 3'

    ],
    items: [
    {
    xtype: 'panel',
    region: 'center',
    html: '<IFRAME ID="container_frame" width="100%" height="100%" FRAMEBORDER=0 SCROLLING=NO SRC="' + basePath +'welcome.jsp"></IFRAME>'
    }, {
    xtype: 'panel',
    region: 'west',
    width: 150,
    split: true,
    layout: 'fit',
    items:getTree('tree/initTree.action?showLink=true')
    }
    ]
    }
    ]
    });

    var viewPort = new Ext.Viewport({
    layout: 'fit',
    items:body
    });
    });
