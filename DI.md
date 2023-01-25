# Dependency Injection (DI)


Dependency Injection (DI) is a design pattern that allows you to separate the concerns of creating and managing objects from the objects themselves. This pattern makes your code more maintainable and testable by allowing you to replace dependencies with mock objects in unit tests.

ASPNET Core provides built-in support for DI through the Microsoft.Extensions.DependencyInjection package. This package allows you to register services with the DI container and control their lifetime.

There are three lifetime options in ASPNET Core DI: Transient, Scoped, and Singleton.

>  **Transient lifetime** services are created each time they are requested. This means that a new instance of the service will be created for each constructor or method that requests the service. This lifetime is useful for services that are lightweight and stateless.

> **Scoped lifetime** services are created once per request within the scope. This means that the same instance of the service will be used for all constructors or methods that request the service within a single request to the application. This lifetime is useful for services that have a short lifespan and need to maintain state within a single request.

>  **Singleton lifetime** services are created the first time they are requested and then reused for all subsequent requests. This means that a single instance of the service will be used for the entire lifetime of the application. This lifetime is useful for services that need to maintain state across multiple requests.

To use DI in an ASPNET Core application, you first need to install the *Microsoft.Extensions.DependencyInjection* package(when it's not there yet). Then, in the *Startup.cs* file, you can configure the DI container in the *ConfigureServices* method by calling the *AddTransient, AddScoped, or AddSingleton* method on the *IServiceCollection* interface.

You can then register a service by calling the `AddTransient<TService, TImplementation>()` method on the IServiceCollection interface. And finally, you can inject the service into a constructor of a controller or another class by adding a parameter of the service type to the constructor.

It's important to use the appropriate lifetime based on the requirements of the service and the application. In general, you should use transient lifetime for lightweight, stateless services and singleton lifetime for heavyweight, stateful services.

Here is an example of how you might use DI in a simple ASPNET Core application:

## Creating our interface and our concrete implementation

    public interface IMyService
    {
        void DoWork();
    }

    public class MyService : IMyService
    {
        public void DoWork()
        {
            // Perform some work
        }
    }

## Let's say we want to use our service inside of HomeController:

    public class HomeController : Controller
    {
        private readonly IMyService _myService;
    
        public HomeController(IMyService myService)
        {
            _myService = myService;
        }
    
        public IActionResult Index()
        {
            _myService.DoWork();
            return View();
        }
    }

## The last part is to register our abstraction and our implementations according to properly lifetime.

    public class Startup
    {
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddTransient<IMyService, MyService>();
        }
    
        public void Configure(IApplicationBuilder app)
        {
            app.UseRouting();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapDefaultControllerRoute();
            });
        }
    }


## To Sum up:

In this example, we have defined an interface `IMyService` and an implementation `MyService` of that interface. We have registered the implementation class as a service in the `ConfigureServices` method, using the `AddTransient<TService, TImplementation>()` method on the `IServiceCollection` interface. And we have Injected the service in the HomeController constructor, ASPNET Core's Dependency Injection system will automatically create an instance of the `MyService` class and pass it to the constructor when a new instance of the `HomeController` class is created.
