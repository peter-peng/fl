Title: js 的 templet 工具
Date: 2014-01-25 17:35
Author: chengz
Category: 技术流
Slug: js-templet-tool

underscore.js 的模板方法

     template_.template(templateString, [data], [settings])

Compiles JavaScript templates into functions that can be evaluated for
rendering. Useful for rendering complicated bits of HTML from JSON data
sources. Template functions can both interpolate variables, using <%= …
%\>, as well as execute arbitrary JavaScript code, with <% … %\>. If you
wish to interpolate a value, and have it be HTML-escaped, use <%- … %\>
When you evaluate a template function, pass in a data object that has
properties corresponding to the template's free variables. If you're
writing a one-off, you can pass the data object as the second parameter
to template in order to render immediately instead of returning a
template function. The settings argument should be a hash containing any
\_.templateSettings that should be overridden.

变量替换

    var compiled = _.template("hello: <%= name %>");
    compiled({name: 'moe'});
    => "hello: moe"

循环列表

    var list = "<% _.each(people, function(name) { %> <li><%= name %></li> <% }); %>";
    _.template(list, {people: ['moe', 'curly', 'larry']});
    => "<li>moe</li><li>curly</li><li>larry</li>"

html转义

    var template = _.template("<b><%- value %></b>");
    template({value: '<script>'});
    => "<b>&lt;script&gt;</b>"

可以在JS中使用print方法.有时候比用 <%= ... %\>.方便些

    var compiled = _.template("<% print('Hello ' + epithet); %>");
    compiled({epithet: "stooge"});
    => "Hello stooge"

Learn more about [underscore.js
template](http://underscorejs.org/#template)
