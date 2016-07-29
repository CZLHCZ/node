Node.js 究竟是什么？
一个 “编码就绪” 服务器
Node 是一个服务器端 JavaScript 解释器，它将改变服务器应该如何工作的概念。它的目标是帮助程序员构建高度可伸缩的应用程序，
编写能够处理数万条同时连接到一个（只有一个）物理机的连接代码。

简介
如果您听说过 Node，或者阅读过一些文章，宣称 Node 是多么多么的棒，那么您可能会想：“Node 究竟是什么东西？” 即便是在参阅 Node 的主页之后，您甚至可能还是 不明白 Node 为何物？Node 肯定不适合每个程序员，但它可能是某些程序员一直苦苦追寻的东西。
为试图解释什么是 Node.js，本文将简要介绍一些背景信息：它要解决的问题，它如何工作，如何运行一个简单应用程序，最后，Node 在什么情况下是一个好的解决方案。本文不涉及如何编写一个复杂的 Node 应用程序，也不是一份全面的 Node 教程。阅读本文应该有助于您决定是否应该继续学习 Node，以便将其用于您的业务。

Node 旨在解决什么问题？
Node 公开宣称的目标是 “旨在提供一种简单的构建可伸缩网络程序的方法”。当前的服务器程序有什么问题？我们来做个数学题。在 Java™ 和 PHP 这类语言中，每个连接都会生成一个新线程，每个新线程可能需要 2 MB 的配套内存。在一个拥有 8 GB RAM 的系统上，理论上最大的并发连接数量是 4,000 个用户。随着您的客户群的增长，如果希望您的 Web 应用程序支持更多用户，那么，您必须添加更多服务器。当然，这会增加服务器成本、流量成本和人工成本等成本。除这些成本上升外，还有一个潜在技术问题，即用户可能针对每个请求使用不同的服务器，因此，任何共享资源都必须在所有服务器之间共享。鉴于上述所有原因，整个 Web 应用程序架构（包括流量、处理器速度和内存速度）中的瓶颈是：服务器能够处理的并发连接的最大数量。
Node 解决这个问题的方法是：更改连接到服务器的方式。每个连接发射一个在 Node 引擎的进程中运行的事件，而不是为每个连接生成一个新的 OS 线程（并为其分配一些配套内存）。Node 声称它绝不会死锁，因为它根本不允许使用锁，它不会直接阻塞 I/O 调用。Node 还宣称，运行它的服务器能支持数万个并发连接。
现在您有了一个能处理数万个并发连接的程序，那么您能通过 Node 实际构建什么呢？如果您有一个 Web 应用程序需要处理这么多连接，那将是一件很 “恐怖” 的事！那是一种 “如果您有这个问题，那么它根本不是问题” 的问题。在回答上面的问题之前，我们先看看 Node 的工作原理以及它的设计运行方式。



Node 肯定不是什么？
没错，Node 是一个服务器程序。但是，基础 Node 产品肯定不 像 Apache 或 Tomcat。本质上，那些服务器 “安装就绪型” 服 务器产品，支持立即部署应用程序。通过这些产品，您可以在一分钟内启动并运行一个服务器。Node 肯定不是这种产品。Apache 能通过添加一个 PHP 模块来允许开发人员创建动态 Web 页，添加一个 SSL 模块来实现安全连接，与此类似，Node 也有模块概念，允许向 Node 内核添加模块。实际上，可供选择的用于 Node 的模块有数百个之多，社区在创建、发布和更新模块方面非常活跃，一天甚至可以处理数十个模块。本文后面将讨论 Node 的整个模块部分。

Node 如何工作？
Node 本身运行 V8 JavaScript。等等，服务器上的 JavaScript？没错，您没有看错。对于只在客户机上使用 JavaScript 的程序员而言，服务器端 JavaScript 可能是一个新概念，但这个概念本身并非遥不可及，因此为何不能在服务器上使用客户机上使用的编程语言？
什么是 V8？V8 JavaScript 引擎是 Google 用于其 Chrome 浏览器的底层 JavaScript 引擎。很少有人考虑 JavaScript 在客户机上实际做了些什么？实际上，JavaScript 引擎负责解释并执行代码。Google 使用 V8 创建了一个用 C++ 编写的超快解释器，该解释器拥有另一个独特特征；您可以下载该引擎并将其嵌入任何 应用程序。V8 JavaScript 引擎并不仅限于在一个浏览器中运行。因此，Node 实际上会使用 Google 编写的 V8 JavaScript 引擎，并将其重建为可在服务器上使用。太完美了！既然已经有一个不错的解决方案可用，为何还要创建一种新语言呢？
事件驱动编程
许多程序员接受的教育使他们认为，面向对象编程是完美的编程设计，这使得他们对其他编程方法不屑一顾。Node 使用了一个所谓的事件驱动编程模型。
清单 1. 客户端上使用 jQuery 的事件驱动编程
// jQuery code on the client-side showing how Event-Driven programming works

// When a button is pressed, an Event occurs - deal with it
// directly right here in an anonymous function, where all the
// necessary variables are present and can be referenced directly
$("#myButton").click(function(){
     if ($("#myTextField").val() != $(this).val())
         alert("Field must match button text");
});
实际上，服务器端和客户端没有任何区别。没错，这没有按钮点击操作，也没有向文本字段键入的操作，但在一个更高的层面上，事件正在 发生。一个连接被建立，这是一个事件！数据通过连接进行接收，这也是一个事件！数据通过连接停止，这还是一个事件！
为什么这种设置类型对 Node 很理想？JavaScript 是一种很棒的事件驱动编程语言，因为它允许使用匿名函数和闭包，更重要的是，任何写过代码的人都熟悉它的语法。事件发生时调用的回调函数可以在捕获事件处进行编写。这样可以使代码容易编写和维护，没有复杂的面向对象框架，没有接口，没有过度设计的可能性。只需监听事件，编写一个回调函数，其他事情都可以交给系统处理！

示例 Node 应用程序
最后，我们来看一些代码！让我们将讨论过的所有内容汇总起来，从而创建我们的第一个 Node 应用程序。我们已经知道，Node 对于处理高流量应用程序很理想，所以我们将创建一个非常简单的 Web 应用程序，一个为实现最快速度而构建的应用程序。下面是 “老板” 交代的关于我们的样例应用程序的具体要求：创建一个随机数字生成器 RESTful API。这个应用程序应该接受一个输入：一个名为 “number” 的参数。然后，应用程序返回一个介于 0 和该参数之间的随机数字，并将生成的数字返回给调用者。由于 “老板” 希望该应用程序成为一个广泛流行的应用程序，因此它应该能处理 50,000 个并发用户。我们来看看以下代码：
清单 2. Node 随机数字生成器
// these modules need to be imported in order to use them.
// Node has several modules.  They are like any #include
// or import statement in other languages
var http = require("http");
var url = require("url");

// The most important line in any Node file.  This function
// does the actual process of creating the server.  Technically,
// Node tells the underlying operating system that whenever a
// connection is made, this particular callback function should be
// executed.  Since we're creating a web service with REST API,
// we want an HTTP server, which requires the http variable
// we created in the lines above.
// Finally, you can see that the callback method receives a 'request'
// and 'response' object automatically.  This should be familiar
// to any PHP or Java programmer.
http.createServer(function(request, response) {

     // The response needs to handle all the headers, and the return codes
     // These types of things are handled automatically in server programs
     // like Apache and Tomcat, but Node requires everything to be done yourself
     response.writeHead(200, {"Content-Type": "text/plain"});

     // Here is some unique-looking code.  This is how Node retrives
     // parameters passed in from client requests.  The url module
     // handles all these functions.  The parse function
     // deconstructs the URL, and places the query key-values in the
     // query object.  We can find the value for the "number" key
     // by referencing it directly - the beauty of JavaScript.
     var params = url.parse(request.url, true).query;
     var input = params.number;

     // These are the generic JavaScript methods that will create
     // our random number that gets passed back to the caller
     var numInput = new Number(input);
     var numOutput = new Number(Math.random() * numInput).toFixed(0);
     
     // Write the random number to response
     response.write(numOutput);
     
     // Node requires us to explicitly end this connection.  This is because
     // Node allows you to keep a connection open and pass data back and forth,
     // though that advanced topic isn't discussed in this article.
     response.end();

   // When we create the server, we have to explicitly connect the HTTP server to
   // a port.  Standard HTTP port is 80, so we'll connect it to that one.
}).listen(80);

// Output a String to the console once the server starts up, letting us know everything
// starts up correctly
console.log("Random Number Generator Running...");
启动应用程序
将上面的代码放入一个名为 “random.js” 的文件中。现在，要启动这个应用程序并运行它（以便创建 HTTP 服务器并监听端口 80 上的连接），只需在您的命令提示中输入以下命令：% node random.js。下面是服务器已经启动并运行时看起来的样子：
root@ubuntu:/home/moila/ws/mike# node random.js
Random Number Generator Running...
访问应用程序
应用程序已经启动并运行。Node 正在监听所有连接，我们来测试一下。由于我们创建了一个简单的 RESTful API，所以可以使用 Web 浏览器来访问这个应用程序。键入以下地址（确保您已完成了上面的步骤）：http://localhost/?number=27。
您的浏览器窗口将更改到一个介于 0 到 27 之间的随机数字。单击浏览器上的 “重新载入” 按钮，您会得到另一个随机数字。就是这样，这就是您的第一个 Node 应用程序！


