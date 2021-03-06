# 请求的生命周期

- [简介](#introduction)
- [生命周期概要](#lifecycle-overview)
- [聚焦于服务提供者](#focus-on-service-providers)

<a name="introduction"></a>
## 简介

当您使用「真实世界」中的任何工具时，若能了解它是如何运作的，您会更具信心。开发应用程序也是一样。当您明白您的开发工具运作的方式，使用它们时，您会感到更舒适、更有信心。

这份文档的目的在给予您一个优良且高端的概述，关于 Laravel 框架是如何「运作」的。当您越了解整个框架，这些事情便不再那么令人感到「神奇」，而您在建立应用程序时也会更具信心。

若您目前还无法了解所有的术语，不要灰心！只要试着对现在提到的东西有个基本掌握，您的知识将会随着您探索这份文档其他章节的同时跟着成长。

<a name="lifecycle-overview"></a>
## 生命周期概要

#### 首要之事

`public/index.php` 这个文件是对 Laravel 应用程序所有请求的进入点。所有的请求都通过您网页服务器（Apache / Ngix）的配置导向这个文件。 `index.php` 这个文件并没有太多的代码。更确切地说，它只是个起始点，用来加载框架其他的部分。

`index.php` 加载由 Composer 产生的自动加载器定义，并接收由 `bootstrap/app.php` 文件所产生的 Laravel 应用程序实例。Laravel 自身的第一个动作就是创建一个应用程序 / [服务容器](/docs/5.0/container)的实例。

#### HTTP / 终端核心

接下来，进入应用程序的请求的会被送往 HTTP 核心或终端核心，视该请求的种类而定。这两种核心是所有请求流向的中心位置。现在开始，我们只将焦点放在 HTTP 核心，它位于 `app/Http/Kernel.php`。

HTTP 核心扩展了 `Illuminate\Foundation\Http\Kernel` 类，它定义了一个 `bootstrappers` 数组，在请求被执行前会执行。这些启动器（bootstrappers）会进行配置错误处理，日志记录，侦测应用程序环境，以及其他在请求真正被处理之前，需要完成的工作。

HTTP 核心也定义了一份 HTTP [中间件](/docs/5.0/middleware)清单，所有的请求在被应用程序处理之前都必须经过它们。这些中间件有负责处理 HTTP session 的读写，决定应用程序是否处于维护模式，查验跨站请求伪造（CSRF）标记，以及其他更多的功能。

HTTP 核心 `handle` 方法的方法签名相当简单：它接收一个 `Request` 并返回一个 `Response`。把核心想像成一个大的黑盒子，用来代表你整个的应用程序。对它输入 HTTP 请求，它将返回 HTTP 响应。

#### 服务提供者

最重要的核心启动行为之一，是加载您的应用程序的服务提供者。所有应用程序的服务提供者，都在 `config/app.php` 配置文件的 `providers` 数组中被配置。首先，对所有的提供者调用 `register` 方法，一旦所有的提供者都被注册之后，`boot` 方法也会被调用。

#### 请求分派

当应用程序启动且所有的服务提供者都被注册之后，`Request` 将被移转给路由器进行分派。路由器会将请求分派给路由或控制器，并执行任何特定路由的中间件。

<a name="focus-on-service-providers"></a>
## 聚焦于服务提供者

服务提供者是启动 Laravel 应用程序的真正关键。创建应用程序实例，注册服务提供者，并将请求移转至已启动的应用程序。真的就是这么简单！

能确实掌握 Laravel 应用程序是如何建立，并通过服务提供者启动是很有价值的。当然，您应用程序的默认服务提供者存放在 `app/Providers` 这个目录中。

`AppServiceProviders` 默认几乎是空的。此提供者是一个很好的地方，可让您加入您应用程序自身的启动及对服务容器的绑定。当然，对大型应用程序而言，您可能希望创建若干个服务提供者，每一个都具备更精细的启动类型。 
