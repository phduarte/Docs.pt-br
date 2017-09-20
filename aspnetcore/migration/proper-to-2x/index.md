---
title: Migrando do ASP.NET para o ASP.NET Core 2.0
author: isaac2004
description: "Este documento de referência fornece diretrizes para migrar aplicativos de API Web ou ASP.NET MVC existentes para o ASP.NET Core 2.0."
keywords: ASP.NET Core, MVC, migrando
ms.author: scaddie
manager: wpickett
ms.date: 08/27/2017
ms.topic: article
ms.assetid: 3155cc9e-d0c9-424b-886c-35c0ec6f9f4e
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/proper-to-2x/index
ms.openlocfilehash: b9170878e4797c729a94caa47c045c3ca3a3d9b8
ms.sourcegitcommit: 4693cb02d845adf2efa00e07ad432c81867bfa12
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/08/2017
---
# <a name="migrating-from-aspnet-to-aspnet-core-20"></a><span data-ttu-id="97d46-104">Migrando do ASP.NET para o ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="97d46-104">Migrating from ASP.NET to ASP.NET Core 2.0</span></span>

<span data-ttu-id="97d46-105">Por [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="97d46-105">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="97d46-106">Este artigo serve como um guia de referência para migração de aplicativos ASP.NET para o ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="97d46-106">This article serves as a reference guide for migrating ASP.NET applications to ASP.NET Core 2.0.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="97d46-107">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="97d46-107">Prerequisites</span></span>

* <span data-ttu-id="97d46-108">[SDK do .NET Core 2.0.0](https://dot.net/core) ou posterior.</span><span class="sxs-lookup"><span data-stu-id="97d46-108">[.NET Core 2.0.0 SDK](https://dot.net/core) or later.</span></span>

## <a name="target-frameworks"></a><span data-ttu-id="97d46-109">Frameworks de destino</span><span class="sxs-lookup"><span data-stu-id="97d46-109">Target Frameworks</span></span>
<span data-ttu-id="97d46-110">Projetos do ASP.NET Core 2.0 oferecem aos desenvolvedores a flexibilidade de direcionamento para o .NET Core, .NET Framework ou ambos.</span><span class="sxs-lookup"><span data-stu-id="97d46-110">ASP.NET Core 2.0 projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="97d46-111">Consulte [Escolhendo entre o .NET Core e .NET Framework para aplicativos de servidor](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server) para determinar qual estrutura de destino é mais apropriada.</span><span class="sxs-lookup"><span data-stu-id="97d46-111">See [Choosing between .NET Core and .NET Framework for server apps](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="97d46-112">Ao usar o .NET Framework como destino, projetos precisam fazer referência a pacotes NuGet individuais.</span><span class="sxs-lookup"><span data-stu-id="97d46-112">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="97d46-113">Usar o .NET Core como destino permite que você elimine várias referências de pacote explícitas, graças ao [metapacote](xref:fundamentals/metapackage) do ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="97d46-113">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core 2.0 [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="97d46-114">Instale o metapacote `Microsoft.AspNetCore.All` em seu projeto:</span><span class="sxs-lookup"><span data-stu-id="97d46-114">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
</ItemGroup>
```

<span data-ttu-id="97d46-115">Quando o metapacote é usado, nenhum pacote referenciado no metapacote é implantado com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97d46-115">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="97d46-116">O repositório de tempo de execução do .NET Core inclui esses ativos e eles são pré-compilados para melhorar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="97d46-116">The .NET Core Runtime Store includes these assets, and they are precompiled to improve performance.</span></span> <span data-ttu-id="97d46-117">Consulte [Metapacote do Microsoft.AspNetCore.All para ASP.NET Core 2.x](xref:fundamentals/metapackage) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="97d46-117">See [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.x](xref:fundamentals/metapackage) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="97d46-118">Diferenças de estrutura do projeto</span><span class="sxs-lookup"><span data-stu-id="97d46-118">Project structure differences</span></span>
<span data-ttu-id="97d46-119">O formato de arquivo *.csproj* foi simplificado no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="97d46-119">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="97d46-120">Algumas alterações importantes incluem:</span><span class="sxs-lookup"><span data-stu-id="97d46-120">Some notable changes include:</span></span>
- <span data-ttu-id="97d46-121">A inclusão explícita de arquivos não é necessária para que eles possam ser considerados como parte do projeto.</span><span class="sxs-lookup"><span data-stu-id="97d46-121">Explicit inclusion of files is not necessary for them to be considered part of the project.</span></span> <span data-ttu-id="97d46-122">Isso reduz o risco de conflitos de mesclagem XML ao trabalhar em grandes equipes.</span><span class="sxs-lookup"><span data-stu-id="97d46-122">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="97d46-123">Não há referências baseadas em GUID a outros projetos, o que melhora a legibilidade do arquivo.</span><span class="sxs-lookup"><span data-stu-id="97d46-123">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="97d46-124">O arquivo pode ser editado sem descarregá-lo no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="97d46-124">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Opção de menu de contexto Editar CSPROJ no Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="97d46-126">Substituição do arquivo Global.asax</span><span class="sxs-lookup"><span data-stu-id="97d46-126">Global.asax file replacement</span></span>
<span data-ttu-id="97d46-127">O ASP.NET Core introduziu um novo mecanismo para inicializar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97d46-127">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="97d46-128">O ponto de entrada para aplicativos ASP.NET é o arquivo *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="97d46-128">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="97d46-129">Tarefas como configuração de roteamento e registros de filtro e de área são tratadas no arquivo *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="97d46-129">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

<span data-ttu-id="97d46-130">[!code-csharp[Main](samples/globalasax-sample.cs)]</span><span class="sxs-lookup"><span data-stu-id="97d46-130">[!code-csharp[Main](samples/globalasax-sample.cs)]</span></span>

<span data-ttu-id="97d46-131">Essa abordagem associa o aplicativo e o servidor no qual ele é implantado de uma forma que interfere na implementação.</span><span class="sxs-lookup"><span data-stu-id="97d46-131">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="97d46-132">Em um esforço para desassociar, a [OWIN](http://owin.org/) foi introduzida para fornecer uma maneira mais limpa de usar várias estruturas juntas.</span><span class="sxs-lookup"><span data-stu-id="97d46-132">In an effort to decouple, [OWIN](http://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="97d46-133">A OWIN fornece um pipeline para adicionar somente os módulos necessários.</span><span class="sxs-lookup"><span data-stu-id="97d46-133">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="97d46-134">O ambiente de hospedagem leva uma função [Startup](xref:fundamentals/startup) para configurar serviços e pipeline de solicitação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97d46-134">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="97d46-135">`Startup` registra um conjunto de middleware com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97d46-135">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="97d46-136">Para cada solicitação, o aplicativo chama cada um dos componentes de middleware com o ponteiro de cabeçalho de uma lista vinculada para um conjunto existente de manipuladores.</span><span class="sxs-lookup"><span data-stu-id="97d46-136">For each request, the application calls each of the the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="97d46-137">Cada componente de middleware pode adicionar um ou mais manipuladores para a pipeline de tratamento de solicitação.</span><span class="sxs-lookup"><span data-stu-id="97d46-137">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="97d46-138">Isso é feito retornando uma referência para o manipulador que é o novo cabeçalho da lista.</span><span class="sxs-lookup"><span data-stu-id="97d46-138">This is accomplished by returning a reference to the handler that is the new head of the list.</span></span> <span data-ttu-id="97d46-139">Cada manipulador é responsável por se lembrar do próximo manipulador na lista e por invocá-lo.</span><span class="sxs-lookup"><span data-stu-id="97d46-139">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="97d46-140">Com o ASP.NET Core, o ponto de entrada para um aplicativo é `Startup` e você não tem mais uma dependência de *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="97d46-140">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="97d46-141">Ao usar a OWIN com o .NET Framework, use algo parecido com o seguinte como um pipeline:</span><span class="sxs-lookup"><span data-stu-id="97d46-141">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

<span data-ttu-id="97d46-142">[!code-csharp[Main](samples/webapi-owin.cs)]</span><span class="sxs-lookup"><span data-stu-id="97d46-142">[!code-csharp[Main](samples/webapi-owin.cs)]</span></span>

<span data-ttu-id="97d46-143">Isso configura as rotas padrão e usa XmlSerialization em Json por padrão.</span><span class="sxs-lookup"><span data-stu-id="97d46-143">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="97d46-144">Adicione outro Middleware para este pipeline conforme necessário (carregamento de serviços, definições de configuração, arquivos estáticos, etc.).</span><span class="sxs-lookup"><span data-stu-id="97d46-144">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="97d46-145">O ASP.NET Core usa uma abordagem semelhante, mas não depende de OWIN para manipular a entrada.</span><span class="sxs-lookup"><span data-stu-id="97d46-145">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="97d46-146">Em vez disso, isso é feito por meio do método `Main` de *Program.cs* (semelhante a aplicativos de console) e `Startup` é carregado por lá.</span><span class="sxs-lookup"><span data-stu-id="97d46-146">Instead, that is done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

<span data-ttu-id="97d46-147">[!code-csharp[Main](samples/program.cs)]</span><span class="sxs-lookup"><span data-stu-id="97d46-147">[!code-csharp[Main](samples/program.cs)]</span></span>

<span data-ttu-id="97d46-148">`Startup` deve incluir um método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="97d46-148">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="97d46-149">Em `Configure`, adicione o middleware necessário ao pipeline.</span><span class="sxs-lookup"><span data-stu-id="97d46-149">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="97d46-150">No exemplo a seguir (com base no modelo de site da Web padrão), vários métodos de extensão são usados para configurar o pipeline com suporte para:</span><span class="sxs-lookup"><span data-stu-id="97d46-150">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="97d46-151">BrowserLink</span><span class="sxs-lookup"><span data-stu-id="97d46-151">BrowserLink</span></span>](http://vswebessentials.com/features/browserlink)
* <span data-ttu-id="97d46-152">Páginas de erro</span><span class="sxs-lookup"><span data-stu-id="97d46-152">Error pages</span></span>
* <span data-ttu-id="97d46-153">Arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="97d46-153">Static files</span></span>
* <span data-ttu-id="97d46-154">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="97d46-154">ASP.NET Core MVC</span></span>
* <span data-ttu-id="97d46-155">Identidade</span><span class="sxs-lookup"><span data-stu-id="97d46-155">Identity</span></span>

<span data-ttu-id="97d46-156">[!code-csharp[Main](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]</span><span class="sxs-lookup"><span data-stu-id="97d46-156">[!code-csharp[Main](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]</span></span>

<span data-ttu-id="97d46-157">O host e o aplicativo foram separados, o que fornece a flexibilidade de mover para uma plataforma diferente no futuro.</span><span class="sxs-lookup"><span data-stu-id="97d46-157">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

<span data-ttu-id="97d46-158">**Observação:** para obter uma referência mais aprofundada sobre o Startup do ASP.NET Core e middleware, consulte [Startup no ASP.NET Core](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="97d46-158">**Note:** For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="storing-configurations"></a><span data-ttu-id="97d46-159">Armazenando configurações</span><span class="sxs-lookup"><span data-stu-id="97d46-159">Storing Configurations</span></span>
<span data-ttu-id="97d46-160">O ASP.NET dá suporte ao armazenamento de configurações.</span><span class="sxs-lookup"><span data-stu-id="97d46-160">ASP.NET supports storing settings.</span></span> <span data-ttu-id="97d46-161">Essas configurações são usadas, por exemplo, para dar suporte ao ambiente no qual os aplicativos foram implantados.</span><span class="sxs-lookup"><span data-stu-id="97d46-161">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="97d46-162">Uma prática comum era armazenar todos os pares chave-valor personalizados na seção `<appSettings>` do arquivo *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="97d46-162">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

<span data-ttu-id="97d46-163">[!code-xml[Main](samples/webconfig-sample.xml)]</span><span class="sxs-lookup"><span data-stu-id="97d46-163">[!code-xml[Main](samples/webconfig-sample.xml)]</span></span>

<span data-ttu-id="97d46-164">Aplicativos leem essas configurações usando a coleção `ConfigurationManager.AppSettings` no namespace `System.Configuration`:</span><span class="sxs-lookup"><span data-stu-id="97d46-164">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

<span data-ttu-id="97d46-165">[!code-csharp[Main](samples/read-webconfig.cs)]</span><span class="sxs-lookup"><span data-stu-id="97d46-165">[!code-csharp[Main](samples/read-webconfig.cs)]</span></span>

<span data-ttu-id="97d46-166">O ASP.NET Core pode armazenar dados de configuração para o aplicativo em qualquer arquivo e carregá-los como parte da inicialização de middleware.</span><span class="sxs-lookup"><span data-stu-id="97d46-166">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="97d46-167">O arquivo padrão usado em modelos de projeto é *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="97d46-167">The default file used in the project templates is *appsettings.json*:</span></span>

<span data-ttu-id="97d46-168">[!code-json[Main](samples/appsettings-sample.json)]</span><span class="sxs-lookup"><span data-stu-id="97d46-168">[!code-json[Main](samples/appsettings-sample.json)]</span></span>

<span data-ttu-id="97d46-169">Carregar esse arquivo em uma instância de `IConfiguration` dentro de seu aplicativo é feito em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="97d46-169">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

<span data-ttu-id="97d46-170">[!code-csharp[Main](samples/startup-builder.cs)]</span><span class="sxs-lookup"><span data-stu-id="97d46-170">[!code-csharp[Main](samples/startup-builder.cs)]</span></span>

<span data-ttu-id="97d46-171">O aplicativo lê de `Configuration` para obter as configurações:</span><span class="sxs-lookup"><span data-stu-id="97d46-171">The app reads from `Configuration` to get the settings:</span></span>

<span data-ttu-id="97d46-172">[!code-csharp[Main](samples/read-appsettings.cs)]</span><span class="sxs-lookup"><span data-stu-id="97d46-172">[!code-csharp[Main](samples/read-appsettings.cs)]</span></span>

<span data-ttu-id="97d46-173">Existem extensões para essa abordagem para tornar o processo mais robusto, tais como o uso de DI ([injeção de dependência](xref:fundamentals/dependency-injection)) para carregar um serviço com esses valores.</span><span class="sxs-lookup"><span data-stu-id="97d46-173">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="97d46-174">A abordagem de DI fornece um conjunto fortemente tipado de objetos de configuração.</span><span class="sxs-lookup"><span data-stu-id="97d46-174">The DI approach provides a strongly-typed set of configuration objects.</span></span>

````csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
````

<span data-ttu-id="97d46-175">**Observação:** para uma referência mais detalhada sobre configuração do ASP.NET Core, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration).</span><span class="sxs-lookup"><span data-stu-id="97d46-175">**Note:** For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="97d46-176">Injeção de dependência nativa</span><span class="sxs-lookup"><span data-stu-id="97d46-176">Native Dependency Injection</span></span>
<span data-ttu-id="97d46-177">Uma meta importante ao criar aplicativos escalonáveis e grandes é o acoplamento flexível de componentes e serviços.</span><span class="sxs-lookup"><span data-stu-id="97d46-177">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="97d46-178">A [injeção de dependência](xref:fundamentals/dependency-injection) é uma técnica popular para conseguir isso e é também um componente nativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="97d46-178">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it is a native component of ASP.NET Core.</span></span>

<span data-ttu-id="97d46-179">Em aplicativos ASP.NET, os desenvolvedores contam com uma biblioteca de terceiros para implementar injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="97d46-179">In ASP.NET applications, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="97d46-180">Um biblioteca desse tipo é a [Unity](https://github.com/unitycontainer/unity), fornecida pelas Diretrizes da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="97d46-180">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span> 

<span data-ttu-id="97d46-181">Um exemplo de configuração da injeção de dependência com Unity é a implementação de `IDependencyResolver`, que encapsula uma `UnityContainer`:</span><span class="sxs-lookup"><span data-stu-id="97d46-181">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

<span data-ttu-id="97d46-182">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample8.cs)]</span><span class="sxs-lookup"><span data-stu-id="97d46-182">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample8.cs)]</span></span>

<span data-ttu-id="97d46-183">Crie uma instância de sua `UnityContainer`, registre seu serviço e defina o resolvedor de dependência de `HttpConfiguration` para a nova instância de `UnityResolver` para o contêiner:</span><span class="sxs-lookup"><span data-stu-id="97d46-183">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

<span data-ttu-id="97d46-184">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample9.cs)]</span><span class="sxs-lookup"><span data-stu-id="97d46-184">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample9.cs)]</span></span>

<span data-ttu-id="97d46-185">Injete `IProductRepository` quando necessário:</span><span class="sxs-lookup"><span data-stu-id="97d46-185">Inject `IProductRepository` where needed:</span></span>

<span data-ttu-id="97d46-186">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample5.cs)]</span><span class="sxs-lookup"><span data-stu-id="97d46-186">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample5.cs)]</span></span>

<span data-ttu-id="97d46-187">Já que a injeção de dependência é parte do ASP.NET Core, você pode adicionar o serviço no método `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="97d46-187">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

<span data-ttu-id="97d46-188">[!code-csharp[Main](samples/configure-services.cs)]</span><span class="sxs-lookup"><span data-stu-id="97d46-188">[!code-csharp[Main](samples/configure-services.cs)]</span></span>

<span data-ttu-id="97d46-189">O repositório pode ser injetado em qualquer lugar, como ocorria com a Unity.</span><span class="sxs-lookup"><span data-stu-id="97d46-189">The repository can be injected anywhere, as was true with Unity.</span></span>

<span data-ttu-id="97d46-190">**Observação:** para uma referência detalhada sobre a injeção de dependência no ASP.NET Core, consulte [Injeção de dependência no ASP.NET Core](xref:fundamentals/dependency-injection#replacing-the-default-services-container)</span><span class="sxs-lookup"><span data-stu-id="97d46-190">**Note:** For an in-depth reference to dependency injection in ASP.NET Core, see [Dependency Injection in ASP.NET Core](xref:fundamentals/dependency-injection#replacing-the-default-services-container)</span></span>

## <a name="serving-static-files"></a><span data-ttu-id="97d46-191">Servir arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="97d46-191">Serving Static Files</span></span>
<span data-ttu-id="97d46-192">Uma parte importante do desenvolvimento da Web é a capacidade de servir ativos estáticos, do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="97d46-192">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="97d46-193">Os exemplos mais comuns de arquivos estáticos são HTML, CSS, Javascript e imagens.</span><span class="sxs-lookup"><span data-stu-id="97d46-193">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="97d46-194">Esses arquivos precisam ser salvos no local de publicação do aplicativo (ou CDN) e referenciados para que eles possam ser carregados por uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="97d46-194">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="97d46-195">Esse processo foi alterado no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="97d46-195">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="97d46-196">No ASP.NET, arquivos estáticos são armazenados em vários diretórios e referenciados nas exibições.</span><span class="sxs-lookup"><span data-stu-id="97d46-196">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="97d46-197">No ASP.NET Core, arquivos estáticos são armazenados na "raiz da Web" (*&lt;raiz do conteúdo&gt;/wwwroot*), a menos que configurado de outra forma.</span><span class="sxs-lookup"><span data-stu-id="97d46-197">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="97d46-198">Os arquivos são carregados no pipeline de solicitação invocando o método de extensão `UseStaticFiles` de `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="97d46-198">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

<span data-ttu-id="97d46-199">[!code-csharp[Main](../../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="97d46-199">[!code-csharp[Main](../../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]</span></span>

<span data-ttu-id="97d46-200">**Observação**: se você usar o .NET Framework como destino, instale o pacote NuGet `Microsoft.AspNetCore.StaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="97d46-200">**Note:** If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="97d46-201">Por exemplo, um ativo de imagem na pasta *wwwroot/imagens* está acessível para o navegador em um local como `http://<app>/images/<imageFileName>`.</span><span class="sxs-lookup"><span data-stu-id="97d46-201">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

<span data-ttu-id="97d46-202">**Observação:** para obter uma referência mais aprofundada sobre como servir arquivos estáticos no ASP.NET Core, consulte [Introdução ao trabalho com arquivos estáticos no ASP.NET Core](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="97d46-202">**Note:** For a more in-depth reference to serving static files in ASP.NET Core, see [Introduction to working with static files in ASP.NET Core](xref:fundamentals/static-files).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="97d46-203">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="97d46-203">Additional Resources</span></span>
* [<span data-ttu-id="97d46-204">Fazendo a portabilidade de Bibliotecas para o .NET Core</span><span class="sxs-lookup"><span data-stu-id="97d46-204">Porting Libraries to .NET Core</span></span>](https://docs.microsoft.com/dotnet/core/porting/libraries)