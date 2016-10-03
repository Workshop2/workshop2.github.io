---
layout: post
title: Using dotNet Core configuration with Structuremap
subtitle: I was stupid, learn from my mistake...
tags: [dotnet, core, structuremap, configuration]
bigimg: /img/dotnet-core-structuremap.jpg
---

So myself and [@anotherchris](http://www.anotherchris.net/) are in the process of doing our first .Net Core port and we chose [Syringe](https://github.com/totaljobsgroup/syringe) to play with.

I have been working on porting over our service/API layer, and things had been going really well until it got to loading up configuration from file. Up until this point we had 3/4 different config files that lived on disk, and they could either be in the local directory or live in  *ProgramData/Syringe*.

The people over at Microsoft have supplied us with a really cool way of loading config from many places in one easy to use pattern: [Configuration](https://docs.asp.net/en/latest/fundamentals/configuration.html).

Everything seemed to be working fine, until I noticed that all the values in my configuration were the default ones, and it took me a very long time to understand why.

Here is a copy of my start up where the configuration was setup:

```csharp
    public Startup(IHostingEnvironment env)
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(env.ContentRootPath)
            .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
            .AddJsonFile($"appsettings.{env.EnvironmentName}.json".ToLower(), optional: true)
            .AddEnvironmentVariables();

        Configuration = builder.Build();
    }

    // This method gets called by the runtime. Use this method to add services to the container.
    public IServiceProvider ConfigureServices(IServiceCollection services)
    {
        // Add framework services.
        services.AddOptions();
        services.AddMvc();
        services.AddSwaggerGen();
        services.AddMemoryCache();

        var container = new Container();
        container.Configure(config =>
        {
            config.AddRegistry<DefaultRegistry>();
            config.Populate(services);
        });

        services.Configure<JsonConfiguration>(Configuration.GetSection("settings"));
        services.Configure<SharedVariables>(Configuration.GetSection("sharedVariables"));
        services.Configure<Environments>(Configuration.GetSection("environments"));

        return container.GetInstance<IServiceProvider>();
    }
```



I couldn’t see anything wrong with it, and I looked all over the internet to see what I was doing wrong. At first I thought the Configuration Builder didn’t support Lists/IEnumerable, however I found posts from over a year ago claiming it did, so I was stumped.

After a while of commenting out code, I found my StructureMap configuration was breaking the IOptions from picking up the correct config (which is worrying because I wasn’t doing anything crazy).

In the end I found the ordering of my ``ConfigureServices`` was very important – **you need to ensure you have done all of your service setup before setting up your StructureMap instance**.

After figuring that out, I simply moved the configuration lines above any StructureMap setup and tadah – everything is now fine. My method now looks like this:

```csharp
    public IServiceProvider ConfigureServices(IServiceCollection services)
    {
        // Add framework services.
        services.AddOptions();
        services.AddMvc();
        services.AddSwaggerGen();
        services.AddMemoryCache();

        services.Configure<JsonConfiguration>(Configuration.GetSection("settings"));
        services.Configure<SharedVariables>(Configuration.GetSection("sharedVariables"));
        services.Configure<Environments>(Configuration.GetSection("environments"));

        var container = new Container();
        container.Configure(config =>
        {
            config.AddRegistry<DefaultRegistry>();
            config.Populate(services);
        });

        return container.GetInstance<IServiceProvider>();
    }
```

I hope this can help someone :smile:



