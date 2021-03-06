# :partly_sunny:依赖注入DI #

<p id="t"></p>

:one:[什么是依赖注入](#a1)

:two:[使用框架提供服务](#a2)

:three:[注册自己的服务](#a3)

<p id="a1"></p>

### :arrow_forward:什么是依赖注入 ### 

:arrow_double_up:[返回目录](#t)

ASP.NET Core的底层设计支持和使用依赖注入，ASP.NET Core应用程序可以利用内置的框架服务将他们注入到启动类的方法中，并且应用程序能够配置注入，由ASP.NET Core提供的默认服务容器提供了最小功能集，并不是取代其他容器。

依赖注入（Dependency injection，DI）是一种实现对象及其合作者或依赖项之间松散耦合的技术。将类用来执行其操作的这些对象以某种方式提供给该类，而不是直接实例化合作者或使用静态引用。通常，类会通过它们的构造函数声明其依赖关系，允许它们遵循显式依赖原则。这种方法称为“构造函数注入”

当系统被设计使用DI，很多类通过它们的构造函数（或者属性）请求其依赖关系，当一个类被用来创建这些类及其相关的依赖关系是很有帮助的，这些类被称为“容器”或者是“控制反转容器”容器实质上是一个工厂，负责提供向它请求的类型实例。除了创建对象的依赖关系之外，容器还会管理应用程序中对象的生命周期。

ASP.NET Core包含了一个默认支持构造函数注入的简单内置容器。

<p id="a2"></p>

### :arrow_forward:使用框架提供服务 ### 

:arrow_double_up:[返回目录](#t)

Startup类中的ConfigureServices方法负责定义应用程序将使用的服务，包括了平台功能，下面是一个如何使用一些扩展方法，向容器中添加额外服务的例子：

```C#
        public void ConfigureServices(IServiceCollection services)
        {
            services.Configure<CookiePolicyOptions>(options =>
            {
                options.CheckConsentNeeded = context => true;
                options.MinimumSameSitePolicy = SameSiteMode.None;
            });
            services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
            //添加你的服务
            services.AddTransient<IUser, UserInfro>();
        }
```

ASP.NET 提供的功能和中间件，例如MVC，遵守使用一个单一的AddService扩展方法来注册所有该功能所需的服务。当然除了使用各种框架功能配置应用程序外，还可以使用ConfigureServices配置自己的应用程序服务。

<p id="a3"></p>

### :arrow_forward:注册自己的服务 ### 

:arrow_double_up:[返回目录](#t)

可以按照以下方式来注册自己的应用程序服务。第一个泛型类型表示将要从容器中请求的类型（通常是一个接口）。第二个泛型类型表示将由容器实例化并且勇于完成这些请求的具体类型：

```C#
services.AddTransient<IUser, UserInfro>();
```

每个Services.Add<service> 调用添加服务。例如`services.AddMvc()`添加了MVC服务。AddTransient方法用于将抽象类型映射到每个需要它实例化的具体服务。这种称为服务的生命周期，另外的生命周期选项在下面描述。为你注册的每一个服务选择合适的生命周期是重要的。
  
在下面中示例中简单阐述了在控制器中简单地使用：

用户数据类：

```C#
    public class User
    {
        public String Name { get; set; }
        public int Id { get; set; }

        public User(String _name,int _id)
        {
            Name = _name;
            Id = _id;
        }
        
    }
```

声明接口类：

```C#
    public interface IUser
    {
        List<User> GetList();
        void Add(User u);
        void Delete(int i);
    }
```

接口实现类：

```C#
    public class UserInfro : IUser 
    {
        private List<User> list;
        public UserInfro()
        {
            list = new List<User>();
            list.Add(new User("lumnca", 451515));
        }
        public List<User> GetList()
        {
            return list;
        }
        public void Add(User user)
        {
            list.Add(user);
        }
        public void Delete(int i)
        {
            list.RemoveAt(i);
        }
    }
```

控制器实现类：

```C#
    public class HomeController : Controller
    {
        private readonly  IUser user;
        public HomeController(IUser _user)
        {
            user = _user;
        }
        public IActionResult Index()
        { 
            ViewBag.list = user.GetList();
            return View();
        }
    }
```

像这样最后在ViewBag.list中就含有了用户数据列表类型的数据。可以看到在控制器中并没有类实例化，而是通过构造函数来实现了注入。这个数由于在Startup中注册了服务实现`services.AddTransient<IUser, UserInfro>();` 所以会自动帮你实例化一个
UserInfro类实例。所有以该接口实例的对象都会被执行UserInfro类。所以接口变量实例化了服务。

对于实例化含有3中方法，每种的声明周期不一致，如下：

 `services.AddTransient<T,V>();` 瞬时，生命周期在他们每次请求时被创建。
 
 `services.AddScoped<T,V>()` 作用域生命周期服务在每次请求时被创建一次。
 
 `services.AddSingleton<T,V>()` 单例，单例生命周期服务在它们第一次被请求时创建，并且以后每次请求都使用这个相同的实例。
 
 









