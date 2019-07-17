# :partly_sunny:ASP .NET Core创建与结构 #

<p id="t"></p>

:one:[ASP .NET Core](#a1)

:two:[ASP .NET Core创建](#a2)

:three:[ASP .NET Core结构](#a3)

<p id="a1"></p>

### :arrow_forward:ASP .NET Core简介 ### 

:arrow_double_up:[返回目录](#t)

`ASP.NET Core 是一个跨平台的高性能开源框架，用于生成基于云且连接 Internet 的新式应用程序。 使用 ASP.NET Core，可以：`

 `1.创建 Web 应用程序和服务、IoT 应用和移动后端。`
 
`2.在 Windows、macOS 和 Linux 上使用喜爱的开发工具。`

`3.部署到云或本地。`

`4.在 .NET Core 或 .NET Framework 上运行。`

`数百万开发人员使用过（并将继续使用）ASP.NET 4.x 创建 Web 应用。 ASP.NET Core 是重新设计的 ASP.NET 4.x，更改了体系结构，形成了更精简的模块化框架。
ASP.NET Core 具有如下优点：`


* 生成 Web UI 和 Web API 的统一场景。
* 针对可测试性进行构建。
* Razor Pages 可以使基于页面的编码方式更简单高效。
* 能够在 Windows、macOS 和 Linux 上进行开发和运行。
* 开放源代码和以社区为中心。
* 集成新式客户端框架和开发工作流。
* 基于环境的云就绪配置系统。
* 内置依赖项注入。
* 轻型的高性能模块化 HTTP 请求管道。
* 能够在 IIS、Nginx、Apache、Docker 上进行托管或在自己的进程中进行自托管。
* 定目标到 .NET Core 时，可以使用并行应用版本控制。
* 简化新式 Web 开发的工具。

<p id="a2"></p>

### :arrow_forward:ASP .NET Core创建 ### 

:arrow_double_up:[返回目录](#t)

使用ASP .NET Core2.x首先需要下载运行环境，使用VS2017版本可以去微软官网下载[SDK下载地址](https://dotnet.microsoft.com/download)下载如下：

![](https://github.com/Lumnca/ASP-.NET-Core/blob/master/Images/a1.png)

点击下载这两个执行文件配置全部默认点击下一步即可，安装完毕后，打开Vs2017进行如下操作：

`1.创建项目`

![](https://github.com/Lumnca/ASP-.NET-Core/blob/master/Images/a2.png)

`选择Web类型`

![](https://github.com/Lumnca/ASP-.NET-Core/blob/master/Images/a3.png)

点击确定完成创。按Ctrl+F5运行出现如下界面就表示成功！

![](https://github.com/Lumnca/ASP-.NET-Core/blob/master/Images/a4.png)

创建成功后我们可以看下文件目录如下：

![](https://github.com/Lumnca/ASP-.NET-Core/blob/master/Images/a5.png)

其中现在需要知道的是

wwwroot : `wwwroot为静态文件夹，其中放入了js，css等文件，当然你也可以加入其它静态文件`

controllers: `控制器，其默认有一个Home的控制器。这也是后台实现操作的地方`

Model: `数据模型，即数据类的存放地方`

Views: `视图模型，即cshtml界面可以使用代码插入的HTML界面`

其余的就是项目运行配置项类，后面使用到再做介绍。

<p id="a1"></p>

### :arrow_forward:ASP .NET Core结构 ### 

:arrow_double_up:[返回目录](#t)

**1.ASP.NET Core应用**

在在Program.cs文件中，代码如下：

```C#
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;

namespace WebTest
{
    public class Program
    {
        public static void Main(string[] args)
        {
            CreateWebHostBuilder(args).Build().Run();
        }
        
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>();
    }
}
```

Mian方法调用WebHost.CreateWebHostBuilder来生成了默认的Web主程序，生成服务器的启动类UseStartup，自动分配了Kestrel服务器，ASP .NET Core尝试在IIS上运行。对于其他Web服务器（Http.sys）可以通过调用相应的扩展方法来使用。

**2.Startup**

WebHostBuilder的UseStartup方法为应用程序指定了Startup类:

```C#
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            services.Configure<CookiePolicyOptions>(options =>
            {
                // This lambda determines whether user consent for non-essential cookies is needed for a given request.
                options.CheckConsentNeeded = context => true;
                options.MinimumSameSitePolicy = SameSiteMode.None;
            });


            services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            app.UseHttpsRedirection();
            app.UseStaticFiles();
            app.UseCookiePolicy();

            app.UseMvc(routes =>
            {
                routes.MapRoute(
                    name: "default",
                    template: "{controller=Home}/{action=Index}/{id?}");
            });
        }
    }
```

这个类中定义了请求处理管道和配置应用需要的服务。Startup类必须是公开的（public）并且必须包含如下方法：

```C#
public class Startup
    {
        public void ConfigureServices(IServiceCollection services)
        {

        }

        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {

        }
    }
```

ConfigureServices方法用于定义你的应用程序所使用的服务，Configure方法用于定义你的请求管道中的中间件。

**3.服务**

服务是应用中用于通用调用组件。服务通过依赖注入获取并使用。ASP .NET Core内置了一个简单的控制反转Ioc容器，它默认支持构造器注入，并且可以方便的替换成你自己选用的Ioc容器。由于它的松耦合特性，依赖注入DI使服务在整个应用程序中都可以使用。

**4.中间件**

在ASP.NET Core中，你可以使用中间件构建你的请求处理管道。 ASP.NET Core 中间件为一个HTTPContext执行异步逻辑，然后按照顺序调用下一个中间件或者直接终止请求。一般来说，要想使用一个中间件，只需要在Configure方法里调用IApplicationBuilder上一个对应的UserXXX扩展方法即可。ASP.NET Core内置中间组件：

* 静态组件

* 路由

* 身份验证

也可以创建自己的定义中间件，后面介绍这种方法。

**5.配置**

ASP .NET Core使用了一个新的配置模型，用来处理简单的键值对。新的配置模型不是基于System.Configuration或者web.config，相反它是一个有序拉取数据的配置providers。内置的构造提供多种不同的文件格式，如XML，JSON，INI和用于支持基于环境配置也可以实现自定义的providers







