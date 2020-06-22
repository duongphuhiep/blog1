---
title: "Dependencies Injection"
date: 2020-06-21T12:04:25+02:00
categories: ["backend"]
tags: ["di", "ioc", "autofac", "unittest"]
keywords: ["unittest"]
---

This is not an easy subject. So I always wanted to write about it since a long time ago. This big subject deserves a series of Posts, but I don't wanna  make it too long.

I will just express some random thoughts about DI in all kind of layers: principle's, theories, code practices. so this posts might be a little messy and un-structured!

## Conforming Container

(From the [Mark Seemann's blog post](https://blog.ploeh.dk/2014/05/19/conforming-container))

> Once in a while, someone comes up with the idea that it would be great to introduce a common abstraction over various DI Containers in .NET ...

This Common abstraction of all the DI Containers is called "Conforming Container".
The conclusion is that **Conforming Container is an anti-pattern**. Please read the explanation in the [original blog post](https://blog.ploeh.dk/2014/05/19/conforming-container).

He also explained why peoples still try to do it from time to time:

* "Fear of vendor lock-in" (Autofac, Unity, NInject...). They want to freely switch from NInject to Autofac for example...
* "Library (or Framework) authors mistakenly thinking that Dependency Injection support involves defining a *Conforming Container*".

IMO, Dependency Injection is only a technique to follow the [Dependency inversion principle].
Your library don't "USE" DI, but they should follow the "DI" principles. As long as your your libs nuget follow the principles, then the consumer can choose any DI Container, or no Container at all to consume your library. You don't need any DI container or depend on any DI framework to follow the DI principles.

## Microsoft nearly did a *Conforming Container*

Don't get it wrong! Microsoft didn't try to unify all the .NET DI container, or build adapters for them.

They only made their own DI Container for ASP.NET in the package [Microsoft.Extensions.DependencyInjection.Abstraction].

It's just that [other library authors happen to implement the Microsoft's DI Container interface](https://github.com/aspnet/DependencyInjection). So that make this interface got the same advantage of a *Conforming Container*. However

The [Microsoft.Extensions.DependencyInjection.Abstraction] is optimized for the Microsoft implementation and only in the context of the ASP.NET applications. It might not optimized for others DI frameworks and might not make sense in others context. So some DI frameworks might not compatible with this abstraction.

## Codes Samples

I will demonstrate the [Microsoft.Extensions.DependencyInjection.Abstraction] and by using 2 popular implementations of this interface:

* Microsoft implementation
* Autofac implementation

Let's take a basic dependency graph sample: A `IWalletService` depends on a `IWalletRepository` which in turn depend on a database connectionString:

```C#
public interface IWalletRepository
{
    void WriteToDB();
}
public interface IWalletService
{
    void LockAmount();
}
public class WalletRepository: IWalletRepository
{
    public WalletRepository(string connectionString) {...}
    public void WriteToDB() {...}
}
public class WalletService: IWalletService
{
    public WalletService(IWalletRepository walletRepository) {...}
    public void LockAmount() {...}
}
```

### Microsoft implementation

In this example, I use the Microsoft implementation in the package
`Microsoft.Extensions.DependencyInjection`

```C#
using Microsoft.Extensions.DependencyInjection;

// Use the default DI Container of Microsoft
IServiceProviderFactory<IServiceCollection> serviceProviderFactory = new DefaultServiceProviderFactory();

#region Register all implementations / objects in the dependency graph

IServiceCollection serviceCollection = new ServiceCollection();

// AddScoped means that only one object will be created per scope. In this example I also injected implementation with parameter connectionString, you don't need thigs like "IConnectionString"
serviceCollection.AddScoped(typeof(IWalletRepository), svp => new WalletRepository("LocalDB_connection_string"));

// AddTransient means that A new object is created for each GetService(). I use `AddTransient` only for demonstration purpose
serviceCollection.AddTransient(typeof(IWalletService), typeof(WalletService));

#endregion

IServiceProvider serviceProvider = serviceProviderFactory.CreateServiceProvider(serviceCollection);

//just for demonstration, you don't need to open a new scope here, you can just use serviceProvider.GetService<IWalletService>() to resolve your object.
using (var controller01Scope = serviceProvider.CreateScope())
{
    IServiceProvider scopedServiceProvider = controller01Scope.ServiceProvider;
    
    //resolve the dependencies to create (or Get) the object
    IWalletService walletService = scopedServiceProvider.GetService<IWalletService>();
    
    walletService.LockAmount();
}
```

### Autofac implementation

You can register objects:

* in the Microsoft-way (with `AddScoped`, `AddTransient`..) as the above example.
* or in the Autofac-way (with `Register`, `RegisterType`)..

The initialization process is a little different but in the end you will have a `IServiceProvider` to resolve objects in your Dependency Graph as the above example.

In addition, you also have access to the Autofac specifics features such as `serviceProvider.GetAutofacRoot().BeginLifetimeScope()`..

```C#
using Autofac;
using Autofac.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;

IServiceProviderFactory<ContainerBuilder> serviceProviderFactory = new AutofacServiceProviderFactory();

#region Register all implementations / objects in the dependency graph

IServiceCollection serviceCollection = new ServiceCollection();
serviceCollection.AddScoped(typeof(IWalletRepository), svp => new WalletRepository("LocalDB_connection_string"));
serviceCollection.AddTransient(typeof(IWalletService), typeof(WalletService));

ContainerBuilder containerBuilder = serviceProviderFactory.CreateBuilder(serviceCollection);
// You can also use the Autofac specifics methods to register dependencies. For eg:
// containerBuilder.RegisterType<TransactionRepository>().As<ITransactionRepository>();

#endregion

IServiceProvider serviceProvider = serviceProviderFactory.CreateServiceProvider(containerBuilder);

//from here it is exactly the same as the above example

using (var controller01Scope = serviceProvider.CreateScope())
{
    IServiceProvider scopedServiceProvider = controller01Scope.ServiceProvider;
    IWalletService walletService = scopedServiceProvider.GetService<IWalletService>();
    walletService.LockAmount();
}

```

## Do you need to use a DI Container (or a DI Framework)?

After all the "cool" demonstrations about How to do Dependency Injection.. I want to come back to the starting point :D

Do we really need all of these thing? (`IServiceProviderFactory`, `IServiceCollection`, ..). Hell no! for most applications...

In truth, most of the time that I use the Microsoft DI Framework simply because I was in the ASP.NET application. The ASP.NET framework is big enough to justify the utilization of a DI Framework. It also needs something to manage the **objects lifetime** (Controller scopes, Request scopes, Application/Singleton scopes), and it is also the role of a DI Container.

Other than that, all of my applications is not big enough to justify the utilization of a DI Container. I usually use the [Custom or Default Pattern](https://www.rhyous.com/2017/11/08/avoiding-dependency-injections-constructor-injection-hell-by-using-the-custom-or-default-pattern/) to avoid the [Constructor Injection Hell](https://www.rhyous.com/2016/09/27/constructor-injection-hell/). It is also called the *Poor Man's Injection* or ironically the *Bastard Injection* - an **anti-pattern**. And yeah.. though well aware about all this, I still use this pattern in every applications which

* The object graph is not complicate.
* Most of the dependencies are Singletons so the Objects Lifetime Management is not necessary.

In this (common) situation, using a DI Framework give me more troubles than actual benefits. Moreover, I didn't turn every single thing to interface either, especially for idiots POCO objects. **As long as I can write unit test for my codes (which means as long as I can mock every I/O Codes) then it is good enough.** In order to mock the I/O Codes, I naturally turn most of the classes to Interface in the end. But I didn't blindly turn things into interface, if I did then it was for a good justifiable reason.

One day (which probably never come) if the application grow big enough to ask for a DI Framework, then include a DI Framework and refactor these codes shouldn't be much of a problem.

[Microsoft.Extensions.DependencyInjection.Abstraction]: https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1
[Dependency inversion principle]: https://en.wikipedia.org/wiki/Dependency_inversion_principle
