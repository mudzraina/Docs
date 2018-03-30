---
title: Filter methods for Razor Pages in ASP.NET Core
author: Rick-Anderson
description: Learn how to create filter  methods for Razor Pages in ASP.NET Core.
manager: wpickett
ms.author: riande
ms.date: 4/5/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: content
uid: mvc/razor-pages/filter
---
# Filter methods for Razor Pages in ASP.NET Core

By [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0)) allow Razor Pages to run code before and after a Razor Page handler is run. Razor Page filters are similar to ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters).

Razor Page filters can:

* Run code after a handler method has been selected, but before model binding occurs.
* Run code before the handler method executes, after model binding is complete.
* Run code after the handler method executes.
* Influence which page handler runs.
* Run initialization code before model binding occurs. 
* Be implemented on a page or globally.

[PageModel](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel?view=aspnetcore-2.0) contructors run before the handler methods, but they don't have access to the [HttpContext](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext). Filters have a [FilterContext](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to a subset of the `HttpContext`.

[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) ([how to download](xref:tutorials/index#how-to-download-a-sample))

Razor Page filters provides the following methods, which can be applied globally or at the page level:

* Synchronous methods:

    * [OnPageHandlerSelected](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.
    * [OnPageHandlerExecuting](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.
    * 
[OnPageHandlerExecuted](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.

* Asynchronous methods:

    *
 [OnPageHandlerSelectionAsync](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.
    * [OnPageHandlerExecutionAsync](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.

> [!NOTE]
> Implement **either** the synchronous or the async version of a filter interface, not both. The framework checks first to see if the filter implements the async interface, and if so, it calls that. If not, it calls the synchronous interface's method(s). If both interfaces are implemented, only the async methods are be called. The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.

## Implement Razor Page filters globally

The following code implements an `IAsyncPageFilter`:

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

In the preceding code, [ILogger](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required. It's used in the sample to provide trace information for the application.

The following code enables the `SampleAsyncPageFilter` in the `Startup` class:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2)]

The following code shows the complete `Startup` class:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

The following code implements the synchronous `IPageFilter`:

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

## Implement Razor Page filters by overriding page handlers

The following code overrides the synchronous Razor Page filters:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

The following code overrides the asynchronous Razor Page filters:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs)]