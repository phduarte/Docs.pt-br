---
title: "Hospedagem no núcleo do ASP.NET"
author: guardrex
description: "Saiba mais sobre o host da web em ASP.NET Core, que é responsável pelo gerenciamento de inicialização e o tempo de vida do aplicativo."
keywords: Host, IWebHost, WebHostBuilder, IHostingEnvironment, IApplicationLifetime da web do ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 09/10/2017
ms.topic: article
ms.assetid: 4e45311d-8d56-46e2-b99d-6f65b648a277
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/hosting
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4eb57cf80399abdb7c6d05546ea2b0d5718c56c3
ms.sourcegitcommit: 0a3f215b4f665afc6f2678642968eea698102346
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2017
---
# <a name="hosting-in-aspnet-core"></a><span data-ttu-id="459f2-104">Hospedagem no núcleo do ASP.NET</span><span class="sxs-lookup"><span data-stu-id="459f2-104">Hosting in ASP.NET Core</span></span>

<span data-ttu-id="459f2-105">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="459f2-105">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="459f2-106">Aplicativos ASP.NET Core configurar e iniciar um *host*, que é responsável pelo gerenciamento de inicialização e o tempo de vida do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="459f2-106">ASP.NET Core apps configure and launch a *host*, which is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="459f2-107">No mínimo, o host configura um servidor e um pipeline de processamento de solicitação.</span><span class="sxs-lookup"><span data-stu-id="459f2-107">At a minimum, the host configures a server and a request processing pipeline.</span></span>

## <a name="setting-up-a-host"></a><span data-ttu-id="459f2-108">Configurando um host</span><span class="sxs-lookup"><span data-stu-id="459f2-108">Setting up a host</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="459f2-109">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="459f2-109">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="459f2-110">Criar um host usando uma instância de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="459f2-110">Create a host using an instance of [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span></span> <span data-ttu-id="459f2-111">Isso normalmente é executado no ponto de entrada do aplicativo, o `Main` método.</span><span class="sxs-lookup"><span data-stu-id="459f2-111">This is typically performed in your app's entry point, the `Main` method.</span></span> <span data-ttu-id="459f2-112">Nos modelos de projeto, `Main` está localizado em *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="459f2-112">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="459f2-113">Um típico *Program.cs* chamadas [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) para começar a configurar um host:</span><span class="sxs-lookup"><span data-stu-id="459f2-113">A typical *Program.cs* calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main)]

<span data-ttu-id="459f2-114">`CreateDefaultBuilder`executa as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="459f2-114">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="459f2-115">Configura [Kestrel](servers/kestrel.md) como o servidor web.</span><span class="sxs-lookup"><span data-stu-id="459f2-115">Configures [Kestrel](servers/kestrel.md) as the web server.</span></span> <span data-ttu-id="459f2-116">Para as opções padrão Kestrel, consulte [o Kestrel opções de seção de implementação do servidor web Kestrel no ASP.NET Core](xref:fundamentals/servers/kestrel#kestrel-options).</span><span class="sxs-lookup"><span data-stu-id="459f2-116">For the Kestrel default options, see [the Kestrel options section of Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel#kestrel-options).</span></span>
* <span data-ttu-id="459f2-117">Define a raiz de conteúdo [GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="459f2-117">Sets the content root to [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="459f2-118">Configuração opcional de cargas de:</span><span class="sxs-lookup"><span data-stu-id="459f2-118">Loads optional configuration from:</span></span>
  * <span data-ttu-id="459f2-119">*appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="459f2-119">*appsettings.json*.</span></span>
  * <span data-ttu-id="459f2-120">*appSettings. . JSON de {ambiente}*.</span><span class="sxs-lookup"><span data-stu-id="459f2-120">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="459f2-121">[Segredos do usuário](xref:security/app-secrets) quando o aplicativo é executado no `Development` ambiente.</span><span class="sxs-lookup"><span data-stu-id="459f2-121">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="459f2-122">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="459f2-122">Environment variables.</span></span>
  * <span data-ttu-id="459f2-123">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="459f2-123">Command-line arguments.</span></span>
* <span data-ttu-id="459f2-124">Configura [log](xref:fundamentals/logging) para a saída do console e de depuração com [filtragem de log](xref:fundamentals/logging#log-filtering) regras especificadas em uma seção de configuração de log de um *appSettings. JSON* ou *appsettings. . JSON de {ambiente}* arquivo.</span><span class="sxs-lookup"><span data-stu-id="459f2-124">Configures [logging](xref:fundamentals/logging) for console and debug output with [log filtering](xref:fundamentals/logging#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="459f2-125">Quando em execução por trás do IIS, permite [integração IIS](xref:publishing/iis) Configurando o caminho base e a porta do servidor deve escutar ao usar o [ASP.NET Core módulo](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="459f2-125">When running behind IIS, enables [IIS integration](xref:publishing/iis) by configuring the base path and port the server should listen on when using the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span> <span data-ttu-id="459f2-126">O módulo cria um proxy reverso entre o IIS e Kestrel.</span><span class="sxs-lookup"><span data-stu-id="459f2-126">The module creates a reverse-proxy between IIS and Kestrel.</span></span> <span data-ttu-id="459f2-127">Também configura o aplicativo [capturar erros de inicialização](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="459f2-127">Also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="459f2-128">Para as opções de padrão do IIS, consulte [o IIS opções seção de Host ASP.NET Core no Windows com o IIS](xref:publishing/iis#iis-options).</span><span class="sxs-lookup"><span data-stu-id="459f2-128">For the IIS default options, see [the IIS options section of Host ASP.NET Core on Windows with IIS](xref:publishing/iis#iis-options).</span></span>

<span data-ttu-id="459f2-129">O *conteúdo raiz* determina onde o host procura por arquivos de conteúdo, como arquivos de exibição do MVC.</span><span class="sxs-lookup"><span data-stu-id="459f2-129">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="459f2-130">A raiz de conteúdo padrão é [GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="459f2-130">The default content root is [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span> <span data-ttu-id="459f2-131">Isso resulta em usando a pasta raiz do projeto da web como a raiz de conteúdo quando o aplicativo é iniciado na pasta raiz (por exemplo, chamar [dotnet executar](/dotnet/core/tools/dotnet-run) da pasta de projeto).</span><span class="sxs-lookup"><span data-stu-id="459f2-131">This results in using the web project's root folder as the content root when the app is started from the root folder (for example, calling [dotnet run](/dotnet/core/tools/dotnet-run) from the project folder).</span></span> <span data-ttu-id="459f2-132">Esse é o padrão usado em [Visual Studio](https://www.visualstudio.com/) e [dotnet novos modelos](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="459f2-132">This is the default used in [Visual Studio](https://www.visualstudio.com/) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="459f2-133">Consulte [configuração no ASP.NET Core](xref:fundamentals/configuration) para obter mais informações sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="459f2-133">See [Configuration in ASP.NET Core](xref:fundamentals/configuration) for more information on app configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="459f2-134">Como uma alternativa ao uso estático `CreateDefaultBuilder` método, criando um host de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) é uma abordagem com suporte com o ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="459f2-134">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span> <span data-ttu-id="459f2-135">Consulte a guia de 1. x do ASP.NET Core para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="459f2-135">See the ASP.NET Core 1.x tab for more information.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="459f2-136">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="459f2-136">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="459f2-137">Criar um host usando uma instância de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="459f2-137">Create a host using an instance of [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span></span> <span data-ttu-id="459f2-138">Isso normalmente é executado no ponto de entrada do aplicativo, o `Main` método.</span><span class="sxs-lookup"><span data-stu-id="459f2-138">This is typically performed in your app's entry point, the `Main` method.</span></span> <span data-ttu-id="459f2-139">Nos modelos de projeto, `Main` está localizado em *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="459f2-139">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="459f2-140">O seguinte *Program.cs* demonstra como usar `WebHostBuilder` para criar o host:</span><span class="sxs-lookup"><span data-stu-id="459f2-140">The following *Program.cs* demonstrates how to use `WebHostBuilder` to build the host:</span></span>

[!code-csharp[Main](../common/samples/WebApplication1/Program.cs)]

<span data-ttu-id="459f2-141">`WebHostBuilder`requer um [server que implementa IServer](servers/index.md).</span><span class="sxs-lookup"><span data-stu-id="459f2-141">`WebHostBuilder` requires a [server that implements IServer](servers/index.md).</span></span> <span data-ttu-id="459f2-142">Os servidores internos são [Kestrel](servers/kestrel.md) e [HTTP.sys](servers/httpsys.md) (antes da versão do ASP.NET Core 2.0, o HTTP.sys foi chamado [WebListener](xref:fundamentals/servers/weblistener)).</span><span class="sxs-lookup"><span data-stu-id="459f2-142">The built-in servers are [Kestrel](servers/kestrel.md) and [HTTP.sys](servers/httpsys.md) (prior to the release of ASP.NET Core 2.0, HTTP.sys was called [WebListener](xref:fundamentals/servers/weblistener)).</span></span> <span data-ttu-id="459f2-143">Neste exemplo, o [o método de extensão UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) Especifica o servidor Kestrel.</span><span class="sxs-lookup"><span data-stu-id="459f2-143">In this example, the [UseKestrel extension method](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) specifies the Kestrel server.</span></span>

<span data-ttu-id="459f2-144">O *conteúdo raiz* determina onde o host procura por arquivos de conteúdo, como arquivos de exibição do MVC.</span><span class="sxs-lookup"><span data-stu-id="459f2-144">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="459f2-145">A raiz de conteúdo padrão fornecido a `UseContentRoot` é [GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory?view=netcore-1.1).</span><span class="sxs-lookup"><span data-stu-id="459f2-145">The default content root supplied to `UseContentRoot` is [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory?view=netcore-1.1).</span></span> <span data-ttu-id="459f2-146">Isso resulta em usando a pasta raiz do projeto da web como a raiz de conteúdo quando o aplicativo é iniciado na pasta raiz (por exemplo, chamar [dotnet executar](/dotnet/core/tools/dotnet-run) da pasta de projeto).</span><span class="sxs-lookup"><span data-stu-id="459f2-146">This results in using the web project's root folder as the content root when the app is started from the root folder (for example, calling [dotnet run](/dotnet/core/tools/dotnet-run) from the project folder).</span></span> <span data-ttu-id="459f2-147">Esse é o padrão usado em [Visual Studio](https://www.visualstudio.com/) e [dotnet novos modelos](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="459f2-147">This is the default used in [Visual Studio](https://www.visualstudio.com/) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="459f2-148">Para usar o IIS como um proxy reverso, chame [UseIISIntegration](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions) como parte da construção do host.</span><span class="sxs-lookup"><span data-stu-id="459f2-148">To use IIS as a reverse proxy, call [UseIISIntegration](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions) as part of building the host.</span></span> <span data-ttu-id="459f2-149">`UseIISIntegration`não configure um *servidor*, como [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) does.</span><span class="sxs-lookup"><span data-stu-id="459f2-149">`UseIISIntegration` doesn't configure a *server*, like [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) does.</span></span> <span data-ttu-id="459f2-150">`UseIISIntegration`configura o caminho base e a porta que o servidor deve escutar ao usar o [ASP.NET Core módulo](xref:fundamentals/servers/aspnet-core-module) para criar um proxy reverso entre Kestrel e o IIS.</span><span class="sxs-lookup"><span data-stu-id="459f2-150">`UseIISIntegration` configures the base path and port the server should listen on when using the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) to create a reverse-proxy between Kestrel and IIS.</span></span> <span data-ttu-id="459f2-151">Para usar o IIS com o ASP.NET Core, você deve especificar `UseKestrel` e `UseIISIntegration`.</span><span class="sxs-lookup"><span data-stu-id="459f2-151">To use IIS with ASP.NET Core, you must specify both `UseKestrel` and `UseIISIntegration`.</span></span> <span data-ttu-id="459f2-152">`UseIISIntegration`ativa somente quando em execução por trás do IIS ou IIS Express.</span><span class="sxs-lookup"><span data-stu-id="459f2-152">`UseIISIntegration` only activates when running behind IIS or IIS Express.</span></span> <span data-ttu-id="459f2-153">Para obter mais informações, consulte [Introdução ao ASP.NET Core módulo](xref:fundamentals/servers/aspnet-core-module) e [referência de configuração do módulo do ASP.NET Core](xref:hosting/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="459f2-153">For more information, see [Introduction to ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) and [ASP.NET Core Module configuration reference](xref:hosting/aspnet-core-module).</span></span>

<span data-ttu-id="459f2-154">Uma implementação mínima que configura um host (e um aplicativo ASP.NET Core) inclui a especificação de um servidor e a configuração do canal de solicitação do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="459f2-154">A minimal implementation that configures a host (and an ASP.NET Core app) includes specifying a server and configuration of the app's request pipeline:</span></span>

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .Configure(app =>
    {
        app.Run(context => context.Response.WriteAsync("Hello World!"));
    })
    .Build();

host.Run();
```

---

<span data-ttu-id="459f2-155">Ao configurar um host, você pode fornecer [configurar](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) métodos.</span><span class="sxs-lookup"><span data-stu-id="459f2-155">When setting up a host, you can provide [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) methods.</span></span> <span data-ttu-id="459f2-156">Se você especificar um `Startup` classe, deve definir um `Configure` método.</span><span class="sxs-lookup"><span data-stu-id="459f2-156">If you specify a `Startup` class, it must define a `Configure` method.</span></span> <span data-ttu-id="459f2-157">Para obter mais informações, consulte [inicialização do aplicativo no ASP.NET Core](startup.md).</span><span class="sxs-lookup"><span data-stu-id="459f2-157">For more information, see [Application Startup in ASP.NET Core](startup.md).</span></span> <span data-ttu-id="459f2-158">Diversas chamadas para `ConfigureServices` acrescentar um ao outro.</span><span class="sxs-lookup"><span data-stu-id="459f2-158">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="459f2-159">Diversas chamadas para `Configure` ou `UseStartup` no `WebHostBuilder` substituir as configurações anteriores.</span><span class="sxs-lookup"><span data-stu-id="459f2-159">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="459f2-160">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="459f2-160">Host configuration values</span></span>

<span data-ttu-id="459f2-161">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) fornece métodos para definir a maioria dos valores de configuração disponíveis para o host, que também pode ser definida diretamente com [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) e a chave associada.</span><span class="sxs-lookup"><span data-stu-id="459f2-161">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) provides methods for setting most of the available configuration values for the host, which can also be set directly with [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="459f2-162">Ao definir um valor com `UseSetting`, o valor é definido como uma cadeia de caracteres (entre aspas), independentemente do tipo.</span><span class="sxs-lookup"><span data-stu-id="459f2-162">When setting a value with `UseSetting`, the value is set as a string (in quotes) regardless of the type.</span></span>

### <a name="capture-startup-errors"></a><span data-ttu-id="459f2-163">Capturar erros de inicialização</span><span class="sxs-lookup"><span data-stu-id="459f2-163">Capture Startup Errors</span></span>

<span data-ttu-id="459f2-164">Essa configuração controla a captura de erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="459f2-164">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="459f2-165">**Chave**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="459f2-165">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="459f2-166">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="459f2-166">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="459f2-167">**Padrão**: assume como padrão `false` , a menos que o aplicativo é executado com Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="459f2-167">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="459f2-168">**Definido usando**:`CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="459f2-168">**Set using**: `CaptureStartupErrors`</span></span>

<span data-ttu-id="459f2-169">Quando `false`, erros durante o resultado de inicialização no host sair.</span><span class="sxs-lookup"><span data-stu-id="459f2-169">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="459f2-170">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="459f2-170">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="459f2-171">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="459f2-171">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="459f2-172">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="459f2-172">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .CaptureStartupErrors(true)
    ...
```

---

### <a name="content-root"></a><span data-ttu-id="459f2-173">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="459f2-173">Content Root</span></span>

<span data-ttu-id="459f2-174">Essa configuração determina onde o ASP.NET Core começa a procurar por arquivos de conteúdo, como exibições do MVC.</span><span class="sxs-lookup"><span data-stu-id="459f2-174">This setting determines where ASP.NET Core begins searching for content files, such as MVC views.</span></span> 

<span data-ttu-id="459f2-175">**Chave**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="459f2-175">**Key**: contentRoot</span></span>  
<span data-ttu-id="459f2-176">**Tipo**: *cadeia de caracteres*</span><span class="sxs-lookup"><span data-stu-id="459f2-176">**Type**: *string*</span></span>  
<span data-ttu-id="459f2-177">**Padrão**: padrão é a pasta em que reside o assembly de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="459f2-177">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="459f2-178">**Definido usando**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="459f2-178">**Set using**: `UseContentRoot`</span></span>

<span data-ttu-id="459f2-179">A conteúdo raiz também é usada como o caminho base para o [configuração Web raiz](#web-root).</span><span class="sxs-lookup"><span data-stu-id="459f2-179">The content root is also used as the base path for the [Web Root setting](#web-root).</span></span> <span data-ttu-id="459f2-180">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="459f2-180">If the path doesn't exist, the host fails to start.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="459f2-181">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="459f2-181">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\mywebsite")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="459f2-182">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="459f2-182">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseContentRoot("c:\\mywebsite")
    ...
```

---

### <a name="detailed-errors"></a><span data-ttu-id="459f2-183">Erros detalhados</span><span class="sxs-lookup"><span data-stu-id="459f2-183">Detailed Errors</span></span>

<span data-ttu-id="459f2-184">Determina se detalhado erros devem ser capturados.</span><span class="sxs-lookup"><span data-stu-id="459f2-184">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="459f2-185">**Chave**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="459f2-185">**Key**: detailedErrors</span></span>  
<span data-ttu-id="459f2-186">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="459f2-186">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="459f2-187">**Padrão**: falso</span><span class="sxs-lookup"><span data-stu-id="459f2-187">**Default**: false</span></span>  
<span data-ttu-id="459f2-188">**Definido usando**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="459f2-188">**Set using**: `UseSetting`</span></span>

<span data-ttu-id="459f2-189">Quando habilitado (ou quando o <a href="#environment">ambiente</a> é definido como `Development`), o aplicativo captura exceções detalhadas.</span><span class="sxs-lookup"><span data-stu-id="459f2-189">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="459f2-190">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="459f2-190">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="459f2-191">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="459f2-191">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
    ...
```

---

### <a name="environment"></a><span data-ttu-id="459f2-192">Ambiente</span><span class="sxs-lookup"><span data-stu-id="459f2-192">Environment</span></span>

<span data-ttu-id="459f2-193">Define o ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="459f2-193">Sets the app's environment.</span></span>

<span data-ttu-id="459f2-194">**Chave**: ambiente</span><span class="sxs-lookup"><span data-stu-id="459f2-194">**Key**: environment</span></span>  
<span data-ttu-id="459f2-195">**Tipo**: *cadeia de caracteres*</span><span class="sxs-lookup"><span data-stu-id="459f2-195">**Type**: *string*</span></span>  
<span data-ttu-id="459f2-196">**Padrão**: produção</span><span class="sxs-lookup"><span data-stu-id="459f2-196">**Default**: Production</span></span>  
<span data-ttu-id="459f2-197">**Definido usando**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="459f2-197">**Set using**: `UseEnvironment`</span></span>

<span data-ttu-id="459f2-198">Você pode definir o *ambiente* para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="459f2-198">You can set the *Environment* to any value.</span></span> <span data-ttu-id="459f2-199">Incluem valores definidos pelo Framework `Development`, `Staging`, e `Production`.</span><span class="sxs-lookup"><span data-stu-id="459f2-199">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="459f2-200">Valores não diferencia maiusculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="459f2-200">Values aren't case sensitive.</span></span> <span data-ttu-id="459f2-201">Por padrão, o *ambiente* são lidos a partir de `ASPNETCORE_ENVIRONMENT` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="459f2-201">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="459f2-202">Ao usar [Visual Studio](https://www.visualstudio.com/), variáveis de ambiente podem ser definidas no *launchSettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="459f2-202">When using [Visual Studio](https://www.visualstudio.com/), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="459f2-203">Para obter mais informações, consulte [Trabalhando com vários ambientes](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="459f2-203">For more information, see [Working with Multiple Environments](xref:fundamentals/environments).</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="459f2-204">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="459f2-204">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="459f2-205">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="459f2-205">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseEnvironment("Development")
    ...
```

---

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="459f2-206">Assemblies de inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="459f2-206">Hosting Startup Assemblies</span></span>

<span data-ttu-id="459f2-207">Define a hospedagem assemblies de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="459f2-207">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="459f2-208">**Chave**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="459f2-208">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="459f2-209">**Tipo**: *cadeia de caracteres*</span><span class="sxs-lookup"><span data-stu-id="459f2-209">**Type**: *string*</span></span>  
<span data-ttu-id="459f2-210">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="459f2-210">**Default**: Empty string</span></span>  
<span data-ttu-id="459f2-211">**Definido usando**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="459f2-211">**Set using**: `UseSetting`</span></span>

<span data-ttu-id="459f2-212">Uma cadeia delimitada por ponto e vírgula de hospedagem assemblies de inicialização para carregar na inicialização.</span><span class="sxs-lookup"><span data-stu-id="459f2-212">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="459f2-213">Esse recurso é novo no ASP.NET 2.0 de núcleo.</span><span class="sxs-lookup"><span data-stu-id="459f2-213">This feature is new in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="459f2-214">Embora o valor de configuração padrão é uma cadeia de caracteres vazia, os assemblies de inicialização hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="459f2-214">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="459f2-215">Quando você fornece hospedagem assemblies de inicialização, elas são adicionadas ao assembly do aplicativo de carregamento quando o aplicativo cria seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="459f2-215">When you provide hosting startup assemblies, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="459f2-216">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="459f2-216">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="459f2-217">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="459f2-217">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="459f2-218">Este recurso está indisponível no núcleo do ASP.NET 1. x.</span><span class="sxs-lookup"><span data-stu-id="459f2-218">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="prefer-hosting-urls"></a><span data-ttu-id="459f2-219">Preferir URLs de hospedagem</span><span class="sxs-lookup"><span data-stu-id="459f2-219">Prefer Hosting URLs</span></span>

<span data-ttu-id="459f2-220">Indica se o host deve escutar nas URLs configuradas com o `WebHostBuilder` em vez das configuradas com o `IServer` implementação.</span><span class="sxs-lookup"><span data-stu-id="459f2-220">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="459f2-221">**Chave**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="459f2-221">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="459f2-222">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="459f2-222">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="459f2-223">**Padrão**: true</span><span class="sxs-lookup"><span data-stu-id="459f2-223">**Default**: true</span></span>  
<span data-ttu-id="459f2-224">**Definido usando**:`PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="459f2-224">**Set using**: `PreferHostingUrls`</span></span>

<span data-ttu-id="459f2-225">Esse recurso é novo no ASP.NET 2.0 de núcleo.</span><span class="sxs-lookup"><span data-stu-id="459f2-225">This feature is new in ASP.NET Core 2.0.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="459f2-226">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="459f2-226">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="459f2-227">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="459f2-227">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="459f2-228">Este recurso está indisponível no núcleo do ASP.NET 1. x.</span><span class="sxs-lookup"><span data-stu-id="459f2-228">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="prevent-hosting-startup"></a><span data-ttu-id="459f2-229">Impedir a inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="459f2-229">Prevent Hosting Startup</span></span>

<span data-ttu-id="459f2-230">Impede o carregamento automático de assemblies de inicialização, incluindo o assembly do aplicativo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="459f2-230">Prevents the automatic loading of hosting startup assemblies, including the app's assembly.</span></span>

<span data-ttu-id="459f2-231">**Chave**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="459f2-231">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="459f2-232">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="459f2-232">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="459f2-233">**Padrão**: falso</span><span class="sxs-lookup"><span data-stu-id="459f2-233">**Default**: false</span></span>  
<span data-ttu-id="459f2-234">**Definido usando**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="459f2-234">**Set using**: `UseSetting`</span></span>

<span data-ttu-id="459f2-235">Esse recurso é novo no ASP.NET 2.0 de núcleo.</span><span class="sxs-lookup"><span data-stu-id="459f2-235">This feature is new in ASP.NET Core 2.0.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="459f2-236">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="459f2-236">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="459f2-237">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="459f2-237">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="459f2-238">Este recurso está indisponível no núcleo do ASP.NET 1. x.</span><span class="sxs-lookup"><span data-stu-id="459f2-238">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="server-urls"></a><span data-ttu-id="459f2-239">URLs de servidor</span><span class="sxs-lookup"><span data-stu-id="459f2-239">Server URLs</span></span>

<span data-ttu-id="459f2-240">Indica os endereços IP ou endereços de host com as portas e protocolos que o servidor deve ouvir para solicitações.</span><span class="sxs-lookup"><span data-stu-id="459f2-240">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="459f2-241">**Chave**: urls</span><span class="sxs-lookup"><span data-stu-id="459f2-241">**Key**: urls</span></span>  
<span data-ttu-id="459f2-242">**Tipo**: *cadeia de caracteres*</span><span class="sxs-lookup"><span data-stu-id="459f2-242">**Type**: *string*</span></span>  
<span data-ttu-id="459f2-243">**Padrão**: http://localhost:5000/</span><span class="sxs-lookup"><span data-stu-id="459f2-243">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="459f2-244">**Definido usando**:`UseUrls`</span><span class="sxs-lookup"><span data-stu-id="459f2-244">**Set using**: `UseUrls`</span></span>

<span data-ttu-id="459f2-245">Definir um separados por ponto e vírgula (;) prefixos de lista de URL para o servidor deve responder.</span><span class="sxs-lookup"><span data-stu-id="459f2-245">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="459f2-246">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="459f2-246">For example, `http://localhost:123`.</span></span> <span data-ttu-id="459f2-247">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando o protocolo e porta especificada (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="459f2-247">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="459f2-248">O protocolo (`http://` ou `https://`) devem ser incluídas com cada URL.</span><span class="sxs-lookup"><span data-stu-id="459f2-248">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="459f2-249">Os formatos com suporte variam entre servidores.</span><span class="sxs-lookup"><span data-stu-id="459f2-249">Supported formats vary between servers.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="459f2-250">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="459f2-250">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
    ...
```

<span data-ttu-id="459f2-251">Kestrel tem sua própria API de configuração do ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="459f2-251">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="459f2-252">Para obter mais informações, consulte [Implementação do servidor Web Kestrel no ASP.NET Core](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="459f2-252">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="459f2-253">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="459f2-253">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
    ...
```

---

### <a name="shutdown-timeout"></a><span data-ttu-id="459f2-254">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="459f2-254">Shutdown Timeout</span></span>

<span data-ttu-id="459f2-255">Especifica a quantidade de tempo de espera para o host da web para o desligamento.</span><span class="sxs-lookup"><span data-stu-id="459f2-255">Specifies the amount of time to wait for the web host to shutdown.</span></span>

<span data-ttu-id="459f2-256">**Chave**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="459f2-256">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="459f2-257">**Tipo**: *int*</span><span class="sxs-lookup"><span data-stu-id="459f2-257">**Type**: *int*</span></span>  
<span data-ttu-id="459f2-258">**Padrão**: 5</span><span class="sxs-lookup"><span data-stu-id="459f2-258">**Default**: 5</span></span>  
<span data-ttu-id="459f2-259">**Definido usando**:`UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="459f2-259">**Set using**: `UseShutdownTimeout`</span></span>

<span data-ttu-id="459f2-260">Embora a chave aceita um *int* com `UseSetting` (por exemplo, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), o `UseShutdownTimeout` método de extensão usa um `TimeSpan`.</span><span class="sxs-lookup"><span data-stu-id="459f2-260">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the `UseShutdownTimeout` extension method takes a `TimeSpan`.</span></span> <span data-ttu-id="459f2-261">Esse recurso é novo no ASP.NET 2.0 de núcleo.</span><span class="sxs-lookup"><span data-stu-id="459f2-261">This feature is new in ASP.NET Core 2.0.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="459f2-262">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="459f2-262">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="459f2-263">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="459f2-263">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="459f2-264">Este recurso está indisponível no núcleo do ASP.NET 1. x.</span><span class="sxs-lookup"><span data-stu-id="459f2-264">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="startup-assembly"></a><span data-ttu-id="459f2-265">Assembly de inicialização</span><span class="sxs-lookup"><span data-stu-id="459f2-265">Startup Assembly</span></span>

<span data-ttu-id="459f2-266">Determina o assembly para pesquisar o `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="459f2-266">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="459f2-267">**Chave**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="459f2-267">**Key**: startupAssembly</span></span>  
<span data-ttu-id="459f2-268">**Tipo**: *cadeia de caracteres*</span><span class="sxs-lookup"><span data-stu-id="459f2-268">**Type**: *string*</span></span>  
<span data-ttu-id="459f2-269">**Padrão**: assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="459f2-269">**Default**: The app's assembly</span></span>  
<span data-ttu-id="459f2-270">**Definido usando**:`UseStartup`</span><span class="sxs-lookup"><span data-stu-id="459f2-270">**Set using**: `UseStartup`</span></span>

<span data-ttu-id="459f2-271">Você pode fazer referência ao assembly por nome (`string`) ou tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="459f2-271">You can reference the assembly by name (`string`) or type (`TStartup`).</span></span> <span data-ttu-id="459f2-272">Se vários `UseStartup` métodos são chamados, último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="459f2-272">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="459f2-273">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="459f2-273">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
    ...
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="459f2-274">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="459f2-274">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseStartup("StartupAssemblyName")
    ...
```

```csharp
var host = new WebHostBuilder()
    .UseStartup<TStartup>()
    ...
```

---

### <a name="web-root"></a><span data-ttu-id="459f2-275">Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="459f2-275">Web Root</span></span>

<span data-ttu-id="459f2-276">Define o caminho relativo para ativos estático do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="459f2-276">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="459f2-277">**Chave**: webroot</span><span class="sxs-lookup"><span data-stu-id="459f2-277">**Key**: webroot</span></span>  
<span data-ttu-id="459f2-278">**Tipo**: *cadeia de caracteres*</span><span class="sxs-lookup"><span data-stu-id="459f2-278">**Type**: *string*</span></span>  
<span data-ttu-id="459f2-279">**Padrão**: se não for especificado, o padrão é "(Content Root)/wwwroot", se o caminho existe.</span><span class="sxs-lookup"><span data-stu-id="459f2-279">**Default**: If not specified, the default is "(Content Root)/wwwroot", if the path exists.</span></span> <span data-ttu-id="459f2-280">Se o caminho não existir, é usado um provedor de arquivo não-operacional.</span><span class="sxs-lookup"><span data-stu-id="459f2-280">If the path doesn't exist, then a no-op file provider is used.</span></span>  
<span data-ttu-id="459f2-281">**Definido usando**:`UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="459f2-281">**Set using**: `UseWebRoot`</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="459f2-282">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="459f2-282">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="459f2-283">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="459f2-283">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseWebRoot("public")
    ...
```

---

## <a name="overriding-configuration"></a><span data-ttu-id="459f2-284">Configuração de substituição</span><span class="sxs-lookup"><span data-stu-id="459f2-284">Overriding configuration</span></span>

<span data-ttu-id="459f2-285">Use [configuração](configuration.md) para configurar o host.</span><span class="sxs-lookup"><span data-stu-id="459f2-285">Use [Configuration](configuration.md) to configure the host.</span></span> <span data-ttu-id="459f2-286">No exemplo a seguir, a configuração do host é especificada, opcionalmente, em um *hosting.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="459f2-286">In the following example, host configuration is optionally specified in a *hosting.json* file.</span></span> <span data-ttu-id="459f2-287">Qualquer configuração carregados a partir de *hosting.json* arquivo pode ser substituído pelos argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="459f2-287">Any configuration loaded from the *hosting.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="459f2-288">A configuração interna (no `config`) é usado para configurar o host com `UseConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="459f2-288">The built configuration (in `config`) is used to configure the host with `UseConfiguration`.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="459f2-289">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="459f2-289">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="459f2-290">*Hosting.JSON*:</span><span class="sxs-lookup"><span data-stu-id="459f2-290">*hosting.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

<span data-ttu-id="459f2-291">Substituindo a configuração fornecida pelo `UseUrls` com *hosting.json* config primeiro argumento de linha de comando config segundo:</span><span class="sxs-lookup"><span data-stu-id="459f2-291">Overriding the configuration provided by `UseUrls` with *hosting.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        BuildWebHost(args).Run();
    }

    public static IWebHost BuildWebHost(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hosting.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            })
            .Build();
    }
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="459f2-292">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="459f2-292">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="459f2-293">*Hosting.JSON*:</span><span class="sxs-lookup"><span data-stu-id="459f2-293">*hosting.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

<span data-ttu-id="459f2-294">Substituindo a configuração fornecida pelo `UseUrls` com *hosting.json* config primeiro argumento de linha de comando config segundo:</span><span class="sxs-lookup"><span data-stu-id="459f2-294">Overriding the configuration provided by `UseUrls` with *hosting.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hosting.json", optional: true)
            .AddCommandLine(args)
            .Build();

        var host = new WebHostBuilder()
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .UseKestrel()
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            })
            .Build();

        host.Run();
    }
}
```

---

> [!NOTE]
> <span data-ttu-id="459f2-295">O `UseConfiguration` método de extensão não está atualmente capaz de analisar uma seção de configuração retornada por `GetSection` (por exemplo, `.UseConfiguration(Configuration.GetSection("section"))`.</span><span class="sxs-lookup"><span data-stu-id="459f2-295">The `UseConfiguration` extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="459f2-296">O `GetSection` método filtra as chaves de configuração para a seção solicitada, mas deixa o nome da seção sobre as chaves (por exemplo, `section:urls`, `section:environment`).</span><span class="sxs-lookup"><span data-stu-id="459f2-296">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="459f2-297">O `UseConfiguration` método espera que as chaves para corresponder a `WebHostBuilder` chaves (por exemplo, `urls`, `environment`).</span><span class="sxs-lookup"><span data-stu-id="459f2-297">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="459f2-298">A presença do nome da seção nas chaves impede que os valores da seção Configurando o host.</span><span class="sxs-lookup"><span data-stu-id="459f2-298">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="459f2-299">Esse problema será corrigido em uma próxima versão.</span><span class="sxs-lookup"><span data-stu-id="459f2-299">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="459f2-300">Para obter mais informações e soluções alternativas, consulte [passar a seção de configuração para WebHostBuilder.UseConfiguration usa chaves completas](https://github.com/aspnet/Hosting/issues/839).</span><span class="sxs-lookup"><span data-stu-id="459f2-300">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>

<span data-ttu-id="459f2-301">Para especificar o host executado em uma URL específica, você pode transmitir o valor desejado de um prompt de comando ao executar `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="459f2-301">To specify the host run on a particular URL, you could pass in the desired value from a command prompt when executing `dotnet run`.</span></span> <span data-ttu-id="459f2-302">O argumento de linha de comando substitui o `urls` valor o *hosting.json* arquivo e o servidor escuta na porta 8080:</span><span class="sxs-lookup"><span data-stu-id="459f2-302">The command-line argument overrides the `urls` value from the *hosting.json* file, and the server listens on port 8080:</span></span>

```console
dotnet run --urls "http://*:8080"
```

## <a name="ordering-importance"></a><span data-ttu-id="459f2-303">Ordem de importância</span><span class="sxs-lookup"><span data-stu-id="459f2-303">Ordering importance</span></span>

<span data-ttu-id="459f2-304">Alguns do `WebHostBuilder` configurações primeiro são lidos a partir de variáveis de ambiente, se definido.</span><span class="sxs-lookup"><span data-stu-id="459f2-304">Some of the `WebHostBuilder` settings are first read from environment variables, if set.</span></span> <span data-ttu-id="459f2-305">Essas variáveis de ambiente usam o formato `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="459f2-305">These environment variables use the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="459f2-306">Para definir as URLs que o servidor escuta em por padrão, você deve definir `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="459f2-306">To set the URLs that the server listens on by default, you set `ASPNETCORE_URLS`.</span></span>

<span data-ttu-id="459f2-307">Você pode substituir qualquer um desses valores de variável de ambiente com a especificação de configuração (usando `UseConfiguration`) ou definindo o valor explicitamente (usando `UseSetting` ou um dos métodos de extensão explícito, como `UseUrls`).</span><span class="sxs-lookup"><span data-stu-id="459f2-307">You can override any of these environment variable values by specifying configuration (using `UseConfiguration`) or by setting the value explicitly (using `UseSetting` or one of the explicit extension methods, such as `UseUrls`).</span></span> <span data-ttu-id="459f2-308">O host usa qualquer opção define o valor da última.</span><span class="sxs-lookup"><span data-stu-id="459f2-308">The host uses whichever option sets the value last.</span></span> <span data-ttu-id="459f2-309">Se você deseja definir a URL padrão para um valor programaticamente, mas permitir que ele seja substituído com a configuração, você pode usar a configuração de linha de comando depois de definir a URL.</span><span class="sxs-lookup"><span data-stu-id="459f2-309">If you want to programmatically set the default URL to one value but allow it to be overridden with configuration, you can use command-line configuration after setting the URL.</span></span> <span data-ttu-id="459f2-310">Consulte [configuração substituindo](#overriding-configuration).</span><span class="sxs-lookup"><span data-stu-id="459f2-310">See [Overriding configuration](#overriding-configuration).</span></span>

## <a name="starting-the-host"></a><span data-ttu-id="459f2-311">Iniciando o host</span><span class="sxs-lookup"><span data-stu-id="459f2-311">Starting the host</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="459f2-312">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="459f2-312">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="459f2-313">**Executar**</span><span class="sxs-lookup"><span data-stu-id="459f2-313">**Run**</span></span>

<span data-ttu-id="459f2-314">O `Run` método inicia o aplicativo web e bloqueia o thread de chamada até que o host é desligado:</span><span class="sxs-lookup"><span data-stu-id="459f2-314">The `Run` method starts the web app and blocks the calling thread until the host is shutdown:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="459f2-315">**Iniciar**</span><span class="sxs-lookup"><span data-stu-id="459f2-315">**Start**</span></span>

<span data-ttu-id="459f2-316">Você pode executar o host de maneira sem bloqueio, chamando seu `Start` método:</span><span class="sxs-lookup"><span data-stu-id="459f2-316">You can run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="459f2-317">Se você passar uma lista de URLs para o `Start` método, ele escuta em URLs especificadas:</span><span class="sxs-lookup"><span data-stu-id="459f2-317">If you pass a list of URLs to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="459f2-318">Você pode inicializar e iniciar um novo host usando os padrões pré-configurado de `CreateDefaultBuilder` usando um método estático de conveniência.</span><span class="sxs-lookup"><span data-stu-id="459f2-318">You can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="459f2-319">Esses métodos de iniciar o servidor sem saída de console e [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) aguardar uma quebra (Ctrl-C/SIGINT ou SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="459f2-319">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="459f2-320">**Início (RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="459f2-320">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="459f2-321">Iniciar com um `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="459f2-321">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="459f2-322">Fazer uma solicitação no navegador para `http://localhost:5000` para receber a resposta "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="459f2-322">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="459f2-323">`WaitForShutdown`blocos até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="459f2-323">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="459f2-324">O aplicativo é exibido o `Console.WriteLine` mensagem e aguarda um pressionamento de tecla sair.</span><span class="sxs-lookup"><span data-stu-id="459f2-324">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="459f2-325">**Iniciar (cadeia de caracteres de url, RequestDelegate do aplicativo)**</span><span class="sxs-lookup"><span data-stu-id="459f2-325">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="459f2-326">Iniciar com uma URL e `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="459f2-326">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="459f2-327">Produz o mesmo resultado que **inicial (aplicativo RequestDelegate)**, exceto o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="459f2-327">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="459f2-328">**Iniciar (ação<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="459f2-328">**Start(Action<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="459f2-329">Usar uma instância de `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) para usar o middleware de roteamento:</span><span class="sxs-lookup"><span data-stu-id="459f2-329">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="459f2-330">Use as seguintes solicitações de navegador com o exemplo:</span><span class="sxs-lookup"><span data-stu-id="459f2-330">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="459f2-331">Solicitação</span><span class="sxs-lookup"><span data-stu-id="459f2-331">Request</span></span>                                    | <span data-ttu-id="459f2-332">Resposta</span><span class="sxs-lookup"><span data-stu-id="459f2-332">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="459f2-333">Olá, Martin!</span><span class="sxs-lookup"><span data-stu-id="459f2-333">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="459f2-334">Dias de Buenos, Catrina!</span><span class="sxs-lookup"><span data-stu-id="459f2-334">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="459f2-335">Gera uma exceção com a cadeia de caracteres "ooops!"</span><span class="sxs-lookup"><span data-stu-id="459f2-335">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="459f2-336">Gera uma exceção com a cadeia de caracteres "Uh oh!"</span><span class="sxs-lookup"><span data-stu-id="459f2-336">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="459f2-337">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="459f2-337">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="459f2-338">Olá, mundo!</span><span class="sxs-lookup"><span data-stu-id="459f2-338">Hello World!</span></span>                             |

<span data-ttu-id="459f2-339">`WaitForShutdown`blocos até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="459f2-339">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="459f2-340">O aplicativo é exibido o `Console.WriteLine` mensagem e aguarda um pressionamento de tecla sair.</span><span class="sxs-lookup"><span data-stu-id="459f2-340">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="459f2-341">**Iniciar (url, a ação de cadeia de caracteres<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="459f2-341">**Start(string url, Action<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="459f2-342">Use uma URL e uma instância do `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="459f2-342">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="459f2-343">Produz o mesmo resultado que **iniciar (ação<IRouteBuilder> routeBuilder)**, exceto o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="459f2-343">Produces the same result as **Start(Action<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="459f2-344">**StartWith (ação<IApplicationBuilder> aplicativo)**</span><span class="sxs-lookup"><span data-stu-id="459f2-344">**StartWith(Action<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="459f2-345">Forneça um delegado para configurar um `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="459f2-345">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="459f2-346">Fazer uma solicitação no navegador para `http://localhost:5000` para receber a resposta "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="459f2-346">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="459f2-347">`WaitForShutdown`blocos até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="459f2-347">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="459f2-348">O aplicativo é exibido o `Console.WriteLine` mensagem e aguarda um pressionamento de tecla sair.</span><span class="sxs-lookup"><span data-stu-id="459f2-348">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="459f2-349">**StartWith (url, a ação de cadeia de caracteres<IApplicationBuilder> aplicativo)**</span><span class="sxs-lookup"><span data-stu-id="459f2-349">**StartWith(string url, Action<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="459f2-350">Forneça uma URL e um delegado para configurar um `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="459f2-350">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="459f2-351">Produz o mesmo resultado que **StartWith (ação<IApplicationBuilder> aplicativo)**, exceto o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="459f2-351">Produces the same result as **StartWith(Action<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="459f2-352">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="459f2-352">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="459f2-353">**Executar**</span><span class="sxs-lookup"><span data-stu-id="459f2-353">**Run**</span></span>

<span data-ttu-id="459f2-354">O `Run` método inicia o aplicativo web e bloqueia o thread de chamada até que o host é desligado:</span><span class="sxs-lookup"><span data-stu-id="459f2-354">The `Run` method starts the web app and blocks the calling thread until the host is shutdown:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="459f2-355">**Iniciar**</span><span class="sxs-lookup"><span data-stu-id="459f2-355">**Start**</span></span>

<span data-ttu-id="459f2-356">Você pode executar o host de maneira sem bloqueio, chamando seu `Start` método:</span><span class="sxs-lookup"><span data-stu-id="459f2-356">You can run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="459f2-357">Se você passar uma lista de URLs para o `Start` método, ele escuta em URLs especificadas:</span><span class="sxs-lookup"><span data-stu-id="459f2-357">If you pass a list of URLs to the `Start` method, it listens on the URLs specified:</span></span>


```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

---

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="459f2-358">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="459f2-358">IHostingEnvironment interface</span></span>

<span data-ttu-id="459f2-359">O [IHostingEnvironment interface](/aspnet/core/api/microsoft.aspnetcore.hosting.ihostingenvironment) fornece informações sobre o ambiente de hospedagem na web do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="459f2-359">The [IHostingEnvironment interface](/aspnet/core/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="459f2-360">Você pode usar [injeção de construtor](xref:fundamentals/dependency-injection) para obter o `IHostingEnvironment` para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="459f2-360">You can use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="459f2-361">Você pode usar um [abordagem baseado em convenção](xref:fundamentals/environments#startup-conventions) para configurar seu aplicativo na inicialização com base no ambiente.</span><span class="sxs-lookup"><span data-stu-id="459f2-361">You can use a [convention-based approach](xref:fundamentals/environments#startup-conventions) to configure your app at startup based on the environment.</span></span> <span data-ttu-id="459f2-362">Como alternativa, você pode injetar o `IHostingEnvironment` para o `Startup` construtor para uso em `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="459f2-362">Alternatively, you can inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="459f2-363">Além de `IsDevelopment` método de extensão, `IHostingEnvironment` oferece `IsStaging`, `IsProduction`, e `IsEnvironment(string environmentName)` métodos.</span><span class="sxs-lookup"><span data-stu-id="459f2-363">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="459f2-364">Consulte [trabalhando com vários ambientes](xref:fundamentals/environments) para obter detalhes.</span><span class="sxs-lookup"><span data-stu-id="459f2-364">See [Working with multiple environments](xref:fundamentals/environments) for details.</span></span>

<span data-ttu-id="459f2-365">O `IHostingEnvironment` serviço também pode ser inserido diretamente para o `Configure` método para configurar o pipeline de processamento:</span><span class="sxs-lookup"><span data-stu-id="459f2-365">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up your processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the developer exception page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="459f2-366">Você pode injetar `IHostingEnvironment` para o `Invoke` método ao criar personalizado [middleware](xref:fundamentals/middleware#writing-middleware):</span><span class="sxs-lookup"><span data-stu-id="459f2-366">You can inject `IHostingEnvironment` into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware#writing-middleware):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="459f2-367">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="459f2-367">IApplicationLifetime interface</span></span>

<span data-ttu-id="459f2-368">O [IApplicationLifetime interface](/aspnet/core/api/microsoft.aspnetcore.hosting.iapplicationlifetime) permite executar atividades de pós-inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="459f2-368">The [IApplicationLifetime interface](/aspnet/core/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows you to perform post-startup and shutdown activities.</span></span> <span data-ttu-id="459f2-369">Três propriedades na interface são tokens de cancelamento que você pode registrar com `Action` métodos para definir os eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="459f2-369">Three properties on the interface are cancellation tokens that you can register with `Action` methods to define startup and shutdown events.</span></span> <span data-ttu-id="459f2-370">Também há um `StopApplication` método.</span><span class="sxs-lookup"><span data-stu-id="459f2-370">There's also a `StopApplication` method.</span></span>

| <span data-ttu-id="459f2-371">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="459f2-371">Cancellation Token</span></span>    | <span data-ttu-id="459f2-372">Acionado quando &#8230;</span><span class="sxs-lookup"><span data-stu-id="459f2-372">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="459f2-373">O host foi totalmente iniciado.</span><span class="sxs-lookup"><span data-stu-id="459f2-373">The host has fully started.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="459f2-374">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="459f2-374">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="459f2-375">Ainda podem estar processando solicitações.</span><span class="sxs-lookup"><span data-stu-id="459f2-375">Requests may still be processing.</span></span> <span data-ttu-id="459f2-376">Blocos de desligamento até que esse evento seja concluída.</span><span class="sxs-lookup"><span data-stu-id="459f2-376">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="459f2-377">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="459f2-377">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="459f2-378">Todas as solicitações devem ser processadas completamente.</span><span class="sxs-lookup"><span data-stu-id="459f2-378">All requests should be completely processed.</span></span> <span data-ttu-id="459f2-379">Blocos de desligamento até que esse evento seja concluída.</span><span class="sxs-lookup"><span data-stu-id="459f2-379">Shutdown blocks until this event completes.</span></span> |

| <span data-ttu-id="459f2-380">Método</span><span class="sxs-lookup"><span data-stu-id="459f2-380">Method</span></span>            | <span data-ttu-id="459f2-381">Ação</span><span class="sxs-lookup"><span data-stu-id="459f2-381">Action</span></span>                                           |
| ----------------- | ------------------------------------------------ |
| `StopApplication` | <span data-ttu-id="459f2-382">Encerramento de solicitações do aplicativo atual.</span><span class="sxs-lookup"><span data-stu-id="459f2-382">Requests termination of the current application.</span></span> |

```csharp
public class Startup 
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime) 
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

## <a name="troubleshooting-systemargumentexception"></a><span data-ttu-id="459f2-383">Solucionando problemas de System. ArgumentException</span><span class="sxs-lookup"><span data-stu-id="459f2-383">Troubleshooting System.ArgumentException</span></span>

<span data-ttu-id="459f2-384">**Aplica-se ao ASP.NET Core 2.0 apenas**</span><span class="sxs-lookup"><span data-stu-id="459f2-384">**Applies to ASP.NET Core 2.0 Only**</span></span>

<span data-ttu-id="459f2-385">Se você compilar o host injetando `IStartup` diretamente para o contêiner de injeção de dependência em vez de chamar `UseStartup` ou `Configure`, você pode encontrar o seguinte erro: `Unhandled Exception: System.ArgumentException: A valid non-empty application name must be provided`.</span><span class="sxs-lookup"><span data-stu-id="459f2-385">If you build the host by injecting `IStartup` directly into the dependency injection container rather than calling `UseStartup` or `Configure`, you may encounter the following error: `Unhandled Exception: System.ArgumentException: A valid non-empty application name must be provided`.</span></span>

<span data-ttu-id="459f2-386">Isso ocorre porque o [applicationName(ApplicationKey)](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostdefaults#Microsoft_AspNetCore_Hosting_WebHostDefaults_ApplicationKey) (do assembly atual) é necessário para verificar se há `HostingStartupAttributes`.</span><span class="sxs-lookup"><span data-stu-id="459f2-386">This occurs because the [applicationName(ApplicationKey)](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostdefaults#Microsoft_AspNetCore_Hosting_WebHostDefaults_ApplicationKey) (the current assembly) is required to scan for `HostingStartupAttributes`.</span></span> <span data-ttu-id="459f2-387">Se você inserir manualmente `IStartup` para o contêiner de injeção de dependência, adicione a seguinte chamada a sua `WebHostBuilder` com o nome do assembly especificado:</span><span class="sxs-lookup"><span data-stu-id="459f2-387">If you manually inject `IStartup` into the dependency injection container, add the following call to your `WebHostBuilder` with the assembly name specified:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("applicationName", "<Assembly Name>")
    ...
```

<span data-ttu-id="459f2-388">Como alternativa, adicione uma cópia `Configure` para sua `WebHostBuilder`, que define o `applicationName`(`ApplicationKey`) automaticamente:</span><span class="sxs-lookup"><span data-stu-id="459f2-388">Alternatively, add a dummy `Configure` to your `WebHostBuilder`, which sets the `applicationName`(`ApplicationKey`) automatically:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .Configure(_ => { })
    ...
```

<span data-ttu-id="459f2-389">**Observação**: isso só é necessária com a versão 2.0 do ASP.NET Core e somente quando você não chama `UseStartup` ou `Configure`.</span><span class="sxs-lookup"><span data-stu-id="459f2-389">**NOTE**: This is only required with the ASP.NET Core 2.0 release and only when you don't call `UseStartup` or `Configure`.</span></span>

<span data-ttu-id="459f2-390">Para obter mais informações, consulte [anúncios: Microsoft.Extensions.PlatformAbstractions foi removido (comentário)](https://github.com/aspnet/Announcements/issues/237#issuecomment-323786938) e [StartupInjection exemplo](https://github.com/aspnet/Hosting/blob/8377d226f1e6e1a97dabdb6769a845eeccc829ed/samples/SampleStartups/StartupInjection.cs).</span><span class="sxs-lookup"><span data-stu-id="459f2-390">For more information, see [Announcements: Microsoft.Extensions.PlatformAbstractions has been removed (comment)](https://github.com/aspnet/Announcements/issues/237#issuecomment-323786938) and the [StartupInjection sample](https://github.com/aspnet/Hosting/blob/8377d226f1e6e1a97dabdb6769a845eeccc829ed/samples/SampleStartups/StartupInjection.cs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="459f2-391">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="459f2-391">Additional resources</span></span>

* [<span data-ttu-id="459f2-392">Publicar no Windows usando o IIS</span><span class="sxs-lookup"><span data-stu-id="459f2-392">Publish to Windows using IIS</span></span>](../publishing/iis.md)
* [<span data-ttu-id="459f2-393">Publicar no Linux usando Nginx</span><span class="sxs-lookup"><span data-stu-id="459f2-393">Publish to Linux using Nginx</span></span>](../publishing/linuxproduction.md)
* [<span data-ttu-id="459f2-394">Publicar no Linux usando o Apache</span><span class="sxs-lookup"><span data-stu-id="459f2-394">Publish to Linux using Apache</span></span>](../publishing/apache-proxy.md)
* [<span data-ttu-id="459f2-395">Host em um serviço do Windows</span><span class="sxs-lookup"><span data-stu-id="459f2-395">Host in a Windows Service</span></span>](xref:hosting/windows-service)