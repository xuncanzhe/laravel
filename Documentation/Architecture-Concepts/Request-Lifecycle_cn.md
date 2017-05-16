# 请求周期

[参考网址](https://docs.golaravel.com/docs/5.4/lifecycle/)
-----

* [介绍](#Introduction)
* [生命周期概述](#lifecycle-overview)
* [服务提供集](#focus-on-service-providers)

## 介绍

在“现实世界”中使用任何工具时，如果了解该工具的工作原理，会更有信心。 应用开发也没有什么不同。 当了解开发工具的运作方式时，使用起来更加舒适和自信。

本文档的目的是为您提供一个关于Laravel框架如何工作的更宽泛的概述。 通过更好地了解整体框架，一切都不那么“神奇”，您将更加自信地构建应用程序。 如果你不明白所有的术语，不要着急！ 只需尝试掌握你正在用的，随着您浏览文档的其他部分，了解的越来越时，这些问题也会随之解决。


## Lifecycle Overview

### 首先
在`laravel应`中，应用程序的所有请求的入口是`public/index.php`文件。 所有请求都由Web服务器（Apache或者Nginx）配置引导到此文件。 `index.php`文件并没有包含太多的代码。 相反它只是是加载框架以及其余部分的起点。

在`index.php`文件中，首先加载由Composer生成的自动加载器，然后再加载`bootstrap/app.php`脚本并且得到一个Laravel应用程序的实例。其实`bootstrap/app.php`里只是将接口和实体类绑定，分别是HTTP请求、控制台请求、异常处理。然后在`index.php`里`make`得到一个HTTP实例对象=》HTTP对象处理请求得到响应对象=》响应对象发送数据=》打扫战场。

### HTTP/Console Kernels
接下来，所有请求会被发送给 `HTTP` 内核或者 `console` 内核，这是根据应用的请求的类型而定的（通俗点就是客户端浏览器和服务器命令行）。这两个内核将作为处理中心，所有请求都会经过对应的核心。 但是现在让我们先只关注`HTTP`核心，它位于`app/Http/Kernel.php`。

这个 `HTTP kernel`继承自 `Illuminate\Foundation\Http\Kernel` 类, 基类定义了一个名为`bootstrappers`数组，这些数组在响应请求之前就会被执行。 可以看做是一个引导程序，做一些在实际请求之前的准备工作，比如 配置错误处理、配置日志记录、检测应用程序环境以及执行请求之前需要完成的其他任务等。

这个`HTTP kernel`还定义了一组HTTP中间件的列表，所有的请求在被应用程序处理之前都需要先经过这些中间价。这些中间件处理可以进行很多操作比如 `HTTP session`的读写、确定应用程序是否处于维护模式、表单验证、验证CSRF等等。 

`HTTP kernel`核心的方法`handle`对外接口非常简单：接收一个请求`request`返回一个响应`response`。可以认为`HTTP kernel`是一个代表整个应用程序的黑盒子。 给它HTTP请求，它将返回HTTP响应。

### Service Providers

内核引导程序最重要的一个步骤就是为你的应用程序加载`service providers`。 应用程序所有的`service providers`都可以配置在`config/app.php`配置文件中的`providers`数组中。 首先`register`方法将会调用所有的`providers`，然后一旦所有`providers`都已经注册，将会调用`boot`方法。

`Service providers`负责引导所有框架的各种组件，例如数据库，队列，验证和路由组件等。由于它引导并配置了框架提供的每个功能，是整个Laravel引导过程中最重要的方面。

### Dispatch Request

一旦应用程序被引导启动并且所有`service providers`都已经注册，请求将被转交给路由器进行调度。 路由器将请求发送到路由或控制器，以及运行任何特定于路由的中间件。

## Focus On Service Providers
`Service providers`是启动Laravel应用程序的真正关键所在。 创建应用程序实例、注册`Service providers`、并将请求交给应用程序。 它真的很简单！

牢牢掌握通过`service providers`如何构建和引导一个Laravel应用程序是非常有价值的。 当然，应用程序的默认`service providers`存储在`app/Providers`目录中。

默认情况下，`AppServiceProvider`是空的。 应用程序自己的引导和服务容器绑定一般都是放在这个`provider`。 当然，对于大型应用程序，可能希望创建几个`service providers`，每个`service providers`都负责更细化的任务。
