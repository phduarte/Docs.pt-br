---
title: "Pré-compilação e a compilação de exibição do razor"
author: rick-anderson
description: "Um documento de referência que explicam como habilitar a compilação de exibição Razor do MVC e pré-compilação em aplicativos do ASP.NET Core."
ms.author: riande
manager: wpickett
ms.date: 12/13/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/view-compilation
ms.openlocfilehash: 87989455c2fb6b5a922c7fb6133aa3e8cef42c88
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="razor-view-compilation-and-precompilation-in-aspnet-core"></a>Compilação de exibição Razor e pré-compilação no núcleo do ASP.NET

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Modos de exibição Razor são compilados em tempo de execução quando o modo de exibição é invocado. ASP.NET Core 1.1.0 e superior pode opcionalmente compilar exibições Razor e implantá-las com o aplicativo&mdash;um processo conhecido como pré-compilação. Os modelos de projeto do ASP.NET Core 2. x habilitam pré-compilação por padrão.

> [!IMPORTANT]
> Pré-compilação de exibição Razor está indisponível no momento ao executar uma [autossuficiente de implantação (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) no ASP.NET 2.0 de núcleo. O recurso estará disponível para SCDs ao versões 2.1. Para obter mais informações, consulte [compilação exibição falha durante a compilação cruzada para Linux no Windows](https://github.com/aspnet/MvcPrecompilation/issues/102).

Considerações de pré-compilação:

* Exibições de pré-compilação resulta em uma menor pacote publicado e o tempo de inicialização mais rápido.
* Depois que você pré-compilar modos de exibição, você não pode editar arquivos do Razor. Os modos de exibição editados não está presentes no pacote publicado. 

Para implantar pré-compilado exibições:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Se seu projeto se destina ao .NET Framework, incluir uma referência de pacote para [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/):

```xml
<PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.ViewCompilation" Version="2.0.0" PrivateAssets="All" />
```

Se seu projeto se destina ao .NET Core, não são necessárias alterações.

Os modelos de projeto do ASP.NET Core 2. x definem implicitamente `MvcRazorCompileOnPublish` para `true` por padrão, que significa que este nó pode ser removido com segurança do *. csproj* arquivo. Se você preferir ser explícita, não há qualquer problema na configuração de `MvcRazorCompileOnPublish` propriedade `true`. O seguinte *. csproj* exemplo destaca essa configuração:

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Definir `MvcRazorCompileOnPublish` para `true`e incluir uma referência de pacote para `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`. O seguinte *. csproj* exemplo realça essas configurações:

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish.csproj?highlight=5,12)]

---

Preparar o aplicativo para um [implantação dependentes de framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd) executando um comando como o seguinte na raiz do projeto:

```console
dotnet publish -c Release
```

Um *< nome_do_projeto >. PrecompiledViews.dll* arquivo que contém os modos de exibição do Razor compilados é produzido quando pré-compilação for bem-sucedida. Por exemplo, a captura de tela abaixo mostra o conteúdo de *cshtml* dentro de *WebApplication1.PrecompiledViews.dll*:

![Modos de exibição Razor dentro de DLL](view-compilation/_static/razor-views-in-dll.png)
