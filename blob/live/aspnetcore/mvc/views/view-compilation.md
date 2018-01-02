---
title: "Pré-compilação e a compilação de exibição do razor"
author: rick-anderson
description: "Um documento de referência que explicam como habilitar a compilação de exibição Razor do MVC e pré-compilação em aplicativos do ASP.NET Core."
keywords: "ASP.NET Core, compilação de exibição Razor, Razor pré-compilação, pré-compilação do Razor"
ms.author: riande
manager: wpickett
ms.date: 12/13/2017
ms.topic: article
ms.assetid: ab4705b7-1638-1638-bc97-ea7f292fe92a
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/view-compilation
ms.openlocfilehash: 6839892c104673af0fd0fd074d368f3f42259d76
ms.sourcegitcommit: 198fb0488e961048bfa376cf58cb853ef1d1cb91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="razor-view-compilation-and-precompilation-in-aspnet-core"></a><span data-ttu-id="632ec-104">Compilação de exibição Razor e pré-compilação no núcleo do ASP.NET</span><span class="sxs-lookup"><span data-stu-id="632ec-104">Razor view compilation and precompilation in ASP.NET Core</span></span>

<span data-ttu-id="632ec-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="632ec-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="632ec-106">Modos de exibição Razor são compilados em tempo de execução quando o modo de exibição é invocado.</span><span class="sxs-lookup"><span data-stu-id="632ec-106">Razor views are compiled at runtime when the view is invoked.</span></span> <span data-ttu-id="632ec-107">ASP.NET Core 1.1.0 e superior pode opcionalmente compilar exibições Razor e implantá-las com o aplicativo&mdash;um processo conhecido como pré-compilação.</span><span class="sxs-lookup"><span data-stu-id="632ec-107">ASP.NET Core 1.1.0 and higher can optionally compile Razor views and deploy them with the app&mdash;a process known as precompilation.</span></span> <span data-ttu-id="632ec-108">Os modelos de projeto do ASP.NET Core 2. x habilitam pré-compilação por padrão.</span><span class="sxs-lookup"><span data-stu-id="632ec-108">The ASP.NET Core 2.x project templates enable precompilation by default.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="632ec-109">Pré-compilação de exibição Razor está indisponível no momento ao executar uma [autossuficiente de implantação (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) no ASP.NET 2.0 de núcleo.</span><span class="sxs-lookup"><span data-stu-id="632ec-109">Razor view precompilation is currently unavailable when performing a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0.</span></span> <span data-ttu-id="632ec-110">O recurso estará disponível para SCDs ao versões 2.1.</span><span class="sxs-lookup"><span data-stu-id="632ec-110">The feature will be available for SCDs when 2.1 releases.</span></span> <span data-ttu-id="632ec-111">Para obter mais informações, consulte [compilação exibição falha durante a compilação cruzada para Linux no Windows](https://github.com/aspnet/MvcPrecompilation/issues/102).</span><span class="sxs-lookup"><span data-stu-id="632ec-111">For more information, see [View compilation fails when cross-compiling for Linux on Windows](https://github.com/aspnet/MvcPrecompilation/issues/102).</span></span>

<span data-ttu-id="632ec-112">Considerações de pré-compilação:</span><span class="sxs-lookup"><span data-stu-id="632ec-112">Precompilation considerations:</span></span>

* <span data-ttu-id="632ec-113">Exibições de pré-compilação resulta em uma menor pacote publicado e o tempo de inicialização mais rápido.</span><span class="sxs-lookup"><span data-stu-id="632ec-113">Precompiling views results in a smaller published bundle and faster startup time.</span></span>
* <span data-ttu-id="632ec-114">Depois que você pré-compilar modos de exibição, você não pode editar arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="632ec-114">You can't edit Razor files after you precompile views.</span></span> <span data-ttu-id="632ec-115">Os modos de exibição editados não está presentes no pacote publicado.</span><span class="sxs-lookup"><span data-stu-id="632ec-115">The edited views won't be present in the published bundle.</span></span> 

<span data-ttu-id="632ec-116">Para implantar pré-compilado exibições:</span><span class="sxs-lookup"><span data-stu-id="632ec-116">To deploy precompiled views:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="632ec-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="632ec-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="632ec-118">Se seu projeto se destina ao .NET Framework, incluir uma referência de pacote para [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/):</span><span class="sxs-lookup"><span data-stu-id="632ec-118">If your project targets .NET Framework, include a package reference to [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/):</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.ViewCompilation" Version="2.0.0" PrivateAssets="All" />
```

<span data-ttu-id="632ec-119">Se seu projeto se destina ao .NET Core, não são necessárias alterações.</span><span class="sxs-lookup"><span data-stu-id="632ec-119">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="632ec-120">Os modelos de projeto do ASP.NET Core 2. x definem implicitamente `MvcRazorCompileOnPublish` para `true` por padrão, que significa que este nó pode ser removido com segurança do *. csproj* arquivo.</span><span class="sxs-lookup"><span data-stu-id="632ec-120">The ASP.NET Core 2.x project templates implicitly set `MvcRazorCompileOnPublish` to `true` by default, which means this node can be safely removed from the *.csproj* file.</span></span> <span data-ttu-id="632ec-121">Se você preferir ser explícita, não há qualquer problema na configuração de `MvcRazorCompileOnPublish` propriedade `true`.</span><span class="sxs-lookup"><span data-stu-id="632ec-121">If you prefer to be explicit, there's no harm in setting the `MvcRazorCompileOnPublish` property to `true`.</span></span> <span data-ttu-id="632ec-122">O seguinte *. csproj* exemplo destaca essa configuração:</span><span class="sxs-lookup"><span data-stu-id="632ec-122">The following *.csproj* sample highlights this setting:</span></span>

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="632ec-123">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="632ec-123">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="632ec-124">Definir `MvcRazorCompileOnPublish` para `true`e incluir uma referência de pacote para `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`.</span><span class="sxs-lookup"><span data-stu-id="632ec-124">Set `MvcRazorCompileOnPublish` to `true`, and include a package reference to `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`.</span></span> <span data-ttu-id="632ec-125">O seguinte *. csproj* exemplo realça essas configurações:</span><span class="sxs-lookup"><span data-stu-id="632ec-125">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish.csproj?highlight=5,12)]

---

<span data-ttu-id="632ec-126">Preparar o aplicativo para um [implantação dependentes de framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd) executando um comando como o seguinte na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="632ec-126">Prepare the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd) by executing a command such as the following at the project root:</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="632ec-127">Um *< nome_do_projeto >. PrecompiledViews.dll* arquivo que contém os modos de exibição do Razor compilados é produzido quando pré-compilação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="632ec-127">A *<project_name>.PrecompiledViews.dll* file, containing the compiled Razor views, is produced when precompilation succeeds.</span></span> <span data-ttu-id="632ec-128">Por exemplo, a captura de tela abaixo mostra o conteúdo de *cshtml* dentro de *WebApplication1.PrecompiledViews.dll*:</span><span class="sxs-lookup"><span data-stu-id="632ec-128">For example, the screenshot below depicts the contents of *Index.cshtml* inside of *WebApplication1.PrecompiledViews.dll*:</span></span>

![Modos de exibição Razor dentro de DLL](view-compilation/_static/razor-views-in-dll.png)