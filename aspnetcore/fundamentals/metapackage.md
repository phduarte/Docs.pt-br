---
title: Metapackage Microsoft.AspNetCore.All para ASP.NET Core 2. x e posterior
author: Rick-Anderson
description: "O metapackage Microsoft.AspNetCore.All inclui todos os pacotes do ASP.NET Core e o Entity Framework Core, juntamente com suas dependências."
ms.author: riande
manager: wpickett
ms.date: 09/20/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/metapackage
ms.openlocfilehash: 8a44ee7ebb7e6b0112000429f1f080bceb7dc895
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
#<a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-2x"></a>Metapackage Microsoft.AspNetCore.All para ASP.NET Core 2. x

Este recurso requer .NET direcionamento do ASP.NET Core 2. x 2. x de núcleo.

O [Metapacote do Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) para ASP.NET Core inclui:

* Todos os pacotes com suporte da equipe do ASP.NET Core.
* Todos os pacotes com suporte pelo Entity Framework Core. 
* Dependências internas e de terceiros usadas por ASP.NET Core e pelo Entity Framework Core. 

Todos os recursos do ASP.NET Core 2. x e o Entity Framework Core 2. x são incluídos no `Microsoft.AspNetCore.All` pacote. Os modelos de projeto padrão usam este pacote.

O número de versão de `Microsoft.AspNetCore.All` metapackage representa a versão do ASP.NET Core e a versão do Entity Framework Core (alinhado com a versão do .NET Core).

Aplicativos que usam o `Microsoft.AspNetCore.All` metapackage automaticamente aproveitar o [repositório de tempo de execução do .NET Core](https://docs.microsoft.com/dotnet/core/deploying/runtime-store). O repositório de tempo de execução contém todos os ativos de tempo de execução necessários para executar o ASP.NET Core aplicativos 2. x. Quando você usa o `Microsoft.AspNetCore.All` metapackage, **sem** ativos dos pacotes do NuGet do ASP.NET Core referenciados são implantados com o aplicativo &mdash; o repositório de tempo de execução do .NET Core contém esses ativos. Os ativos no repositório de tempo de execução são pré-compilados para melhorar o tempo de inicialização do aplicativo.

Você pode usar o processo de remoção de pacote para remover os pacotes que não são usadas. Pacotes cortados são excluídos na saída do aplicativo publicado.

O seguinte *. csproj* referências de arquivo a `Microsoft.AspNetCore.All` metapackage para ASP.NET Core:

[!code-xml[Main](..\mvc\views\view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=9)]
