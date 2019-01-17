# ---------------------第一个MVC应用程序 --------------------- #

## :beginner:创建ASP .NET MVC新项目 ##

打开文件->项目->Web下面找到ASP .NET Web 应用程序,如下

![](https://github.com/Lumnca/MVC/blob/master/Images/b1.png)

在下面给项目命名，可以更改存储地方，解决项目不需要自己命名，会自动命名。点击确定后，出现下面视图：

![](https://github.com/Lumnca/MVC/blob/master/Images/b2.png)

按照上面操作后，就可以创建一个MVC应用程序。我们先看一下解决方案中项目文件目录：

![](https://github.com/Lumnca/MVC/blob/master/Images/b3.png)

可以看到文件目录比较多，我们一步步来了解这些目录：

 * App_Data,App_Start,为MVC的初始文件是关于存放数据和一些网页设置代码，网页设置代码存于App_Start中，不要随意更改。
 
 * Content 这是一个存放Css文件的文件，网站的Css文件都存于下面
 
 * Controllers 为网站的控制器，也是处理数据的方法，也是MVC中C字母的全称，MVC中重要的一点就是控制器的编写，想要网站处理数据，就需要编写控制器代码。我们打开这个项目可以看到一个名为 HomeController 的控制器，这个是下面Views文件中的Home文件下的视图代码所对应的控制器，也就是说你每有一个视图文件夹就需要添加一个控制器，并且控制器名称必须为 视图文件名+Controller，像上面Home+Controller一样。有关这部分下面会继续说明。
 
 * fonts 是一些外部引用文件，现在用不到。
 
 * Models 即模型，MVC中M的全称，是应用程序最重要的部分，模型是应用程序的主体，所定义的现实对象，过程，及规则的表示。简单的来说，就是一些C#代码，在模型中创建类，对象，方法来处理交互数据等作用，C#代码可写在控制器中，但是过多的代码不利于维护，所以更多的处理数据都写在模型中。
 
 * Script 脚本文件，一般存放js文件，网站所用的Js脚本代码文件存放在这个文件中。
 
 * Views 视图，即网页，也是MVC中V的全称，我们一般看到的网页都是.html，打开文件Views再打开Home下的文件发现文件名是以.cshtml结尾的，这是MVC应用程序下网页的扩展名。一个cshtml文件就代表了一个网页。其中还有一个是Shared文件，这是有关默认网页和错误信息的网页，即出现程序错误所要转到的界面和主视图。
 
 * 剩余的其他都是关于网站的配置文件，不要删除和随意改动，网站的运行需要靠这些来完成操作。

## :beginner:控制器 ##

在MVC体系中，传入的请求是由控制器处理的，在ASP .NET MVC中，控制器只是一个简单的C#类，看以下代码：

```C#
using System.Web;
using System.Web.Mvc;

namespace Demo.Controllers
{
    public class HomeController : Controller
    {
        public ActionResult Index()
        {
            return View();
        }

        public ActionResult About()
        {
            ViewBag.Message = "Your application description page.";

            return View();
        }

        public ActionResult Contact()
        {
            ViewBag.Message = "Your contact page.";

            return View();
        }
    }
}
```
先看public class HomeController : Controller这句代码，意为HomeController这个类继承了Controller这个控制器类的所用方法与属性，也是HomeController这个类具有控制器的原因。看这个类中的方法含有3个 Index， About，Contact方法。返回值又是View()，代表了这三个方法其实是渲染了三个界面，打开Views中的Home文件，你也能发现它所具有的三个界面和这三个方法命名相同，也就是说，你的视图每有一个视图，就需要一个ActionResult类型的方法来渲染它。那么是不是没每一个方法都需要一个视图呢？当然不是这样的，观察上面的三个方法他们都是ActionResult类型为返回值的方法，返回值为View()就代表默认返回到以该方法命名的视图
如上面三种，方法名是什么，返回的视图就是什么，如果没有这个视图，就会出现错误，当然我们想不用这个名字返回该名字的视图可以像下面这样写：

```C# 
        public ActionResult OtherName()
        {
            return View("Index");
        }
```

如果调用这个方法就会转到Index界面，你可能又会问，我该怎样调用这个方法呢？下面介绍一下路由：

## :beginner:理解路由 ##

路由系统决定了如何把Url映射到特定的控制器上，也是网页跳转和方法处理的必须所用到条件，举例来说，我们想运行Index界面，需要点击打开这个界面，再点击运行（crtl+F5）这个界面才能看到该网页，如下：

![](https://github.com/Lumnca/MVC/blob/master/Images/b4.png)

你可以看到地址栏的Url，即是我们这个Index的路由也就是它所对应的网址，我们来分析一下这个Url路由：`http://localhost:62078/Home/Index` 

* `http://` ： http协议,一般的网址开头都是这个（可不写）。

* `localhost:62078` ： localhost代表主机，也就是你的主机其他编译器可能会显示127.0.0.1这都是对本机的标识。：62078是我的主机一个端口号，这个是随机分配的，每个人得到的端口号可能是不一样的。

* `/Home/Index` : 你会发现刚好是Home控制器下的Index方法，这就是路由的具体文件采用两级目录形式（Home文件下也可以使用一级，但建议使用二级目录），也就是说我想访问控制器方法 可以通过  /控制器名/方法名  来访问。

例如在刚才的地址栏上我们将`http://localhost:62078/Home/Index` 改成`http://localhost:62078/Home/About` 看看会发现什么，只要你的url没有书写错误就会出现About界面的类容。这就是控制器方法路由。再回到上面的那个方法

```C# 
        public ActionResult OtherName()
        {
            return View("Index");
        }
```

那么它的调用是不是会写呢，它也是同样位于Home控制器下的方法，这里不做演示，读者自行完成。

## :beginner:视图 ##

一开始创建的ASP.NET程序会自带一个Home文件和三个视图文件，你可以删除掉这三个视图文件，但这个Home的文件夹以及Home所对应的控制器就不要删除了，否者要修改需要信息才能使程序运行，因为网站很多默认的功能都是绑定到Home控制器以及Home下视图的，最简单的方法就是我们可以新建立一个和Home一样的视图文件夹，右击Views文件点击新建一个文件夹，并命名为Myweb如下

![](https://github.com/Lumnca/MVC/blob/master/Images/b5.png)

再右击这个文件夹添加视图，如果不采用布局形式页，就可以点击下面这个视图选项：

![](https://github.com/Lumnca/MVC/blob/master/Images/b6.png)

再给你的视图命名，我命名为Index，可以和Home下的文件同样的命名，但是不能在同一个文件夹下命名相同的文件，Home与Index文件夹不同所以可以命名Home下的文件名。然后出现一个cshtml界面，就表示页面创建成功了。你可以在这个页面上添加Html文档。

那么我们想使用这个视图怎么办，打开这个网页，并运行一下，会发现这是不行的，为什么，上一节我们说的路由是控制器的映射，还记的那个路由格式吗？我们再看看一下这个路由格式：`localhost:62078/Home/Index` 。/Home/Index代表的是Home控制器下的Index方法。同理我们的这个视图需要一个 MyWeb控制器下的Index方法，即要使`localhost:62078/MyWeb/Index`有对应的映射。

首先先创建一个名为MyWeb的控制器，右击控制器文件夹，点击添加一个控制器，如下：

![](https://github.com/Lumnca/MVC/blob/master/Images/b7.png)


在出现的界面中选择第一个 MVC 5 控制器-空 的那个选项，然后出现命名界面，由于我们的新建视图文件夹名为MyWeb，所以要一一映射，我们的控制器名为`MyWebController`注意Controller这个名称段必须含有，控制器名一般为 视图文件夹名 + Controller为格式的命名。点击确认后出现控制器代码：

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.Mvc;

namespace Demo.Controllers
{
    public class MyWebController : Controller
    {
        // GET: MyWeb
        public ActionResult Index()
        {
            return View();
        }
    }
}
```

由于控制器会自动创建一个Index方法，所以我们就可以不必自己创建。现在再打开MyWeb文件下的Index视图文件，点击运行，若能出现界面（Index界面含有显示的内容）就表示路由设置成功。同样的你想再MyWeb文件下添加一个其他界面如Other界面，那么首先你要做的就是现在MyWeb文件下创建视图，然后在MyWeb控制器下创建一个Other方法，如下代码：

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.Mvc;

namespace Demo.Controllers
{
    public class MyWebController : Controller
    {
        // GET: MyWeb
        public ActionResult Index()
        {
            return View();
        }
        //添加的Other界面
        public ActionResult Other()
        {
            return View();
        }
    }
}
```
然后再进入到Other界面点击运行，出现界面表示成功。你也可以在地址栏输入 `localhost:62078/控制器/控制器方法` 这种格式的地址来转到不同的界面。























