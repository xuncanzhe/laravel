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
Next, the incoming request is sent to either the HTTP kernel or the console kernel, depending on the type of request that is entering the application. These two kernels serve as the central location that all requests flow through. For now, let's just focus on the HTTP kernel, which is located in  app/Http/Kernel.php.

The HTTP kernel extends the Illuminate\Foundation\Http\Kernel class, which defines an array of  bootstrappers that will be run before the request is executed. These bootstrappers configure error handling, configure logging, detect the application environment, and perform other tasks that need to be done before the request is actually handled.

The HTTP kernel also defines a list of HTTP middleware that all requests must pass through before being handled by the application. These middleware handle reading and writing the HTTP session, determining if the application is in maintenance mode, verifying the CSRF token, and more.

The method signature for the HTTP kernel's handle method is quite simple: receive a Request and return a Response. Think of the Kernel as being a big black box that represents your entire application. Feed it HTTP requests and it will return HTTP responses.

### Service Providers

One of the most important Kernel bootstrapping actions is loading the service providers for your application. All of the service providers for the application are configured in the config/app.php configuration file's providers array. First, the register method will be called on all providers, then, once all providers have been registered, the boot method will be called.

Service providers are responsible for bootstrapping all of the framework's various components, such as the database, queue, validation, and routing components. Since they bootstrap and configure every feature offered by the framework, service providers are the most important aspect of the entire Laravel bootstrap process.

### Dispatch Request

Once the application has been bootstrapped and all service providers have been registered, the  Request will be handed off to the router for dispatching. The router will dispatch the request to a route or controller, as well as run any route specific middleware.


## Focus On Service Providers
Service providers are truly the key to bootstrapping a Laravel application. The application instance is created, the service providers are registered, and the request is handed to the bootstrapped application. It's really that simple!

Having a firm grasp of how a Laravel application is built and bootstrapped via service providers is very valuable. Of course, your application's default service providers are stored in the  app/Providers directory.

By default, the AppServiceProvider is fairly empty. This provider is a great place to add your application's own bootstrapping and service container bindings. Of course, for large applications, you may wish to create several service providers, each with a more granular type of bootstrapping.
