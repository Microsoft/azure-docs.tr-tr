---
title: Azure işlevlerini yerel olarak geliştirin ve çalıştırın | Microsoft Docs
description: Azure işlevleri 'nde çalıştırmadan önce yerel bilgisayarınızdaki Azure işlevlerini nasıl kodleyeceğinizi ve test leyeceğinizi öğrenin.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 7c2e727ecb080d1db212e8b45a2c48bac81a3949
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709320"
---
# <a name="code-and-test-azure-functions-locally"></a>Azure Işlevlerini yerel olarak kod ve test etme

[Azure Portal]Azure işlevleri geliştirip test edebilirken birçok geliştirici, yerel bir geliştirme deneyimi tercih eder. İşlevler, yerel bilgisayarınızda işlevleri oluşturmak ve test etmek için en sevdiğiniz kod düzenleyicinizi ve geliştirme araçlarını kullanmayı kolaylaştırır. Yerel işlevleriniz canlı Azure hizmetlerine bağlanabilir ve tüm Işlevler çalışma zamanını kullanarak yerel bilgisayarınızda hata ayıklaması yapabilirsiniz.

## <a name="local-development-environments"></a>Yerel geliştirme ortamları

Yerel bilgisayarınızda işlevleri geliştirme yönteminiz, [dilinize](supported-languages.md) ve araç tercihlerinize göre değişir. Aşağıdaki tablodaki ortamlar yerel geliştirmeyi destekler:

|Ortam                              |Diller         |Açıklama|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [](functions-create-first-function-powershell.md) [](functions-reference-python.md) [](functions-reference-node.md) [ C# ](functions-reference-csharp.md) [ C# (sınıf kitaplığı)](functions-dotnet-class-library.md), betik (. CSX), JavaScript, PowerShell, Python | [Vs Code Için Azure işlevleri uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) , vs Code işlev desteği ekler. Temel araçları gerektirir. , Çekirdek araçların 2. x 'i kullanılırken Linux, MacOS ve Windows için geliştirmeyi destekler. Daha fazla bilgi için bkz. [Visual Studio Code kullanarak ilk işlevinizi oluşturma](functions-create-first-function-vs-code.md). |
| [Komut istemi veya Terminal](functions-run-local.md) | [](functions-reference-powershell.md) [](functions-reference-python.md) [](functions-reference-node.md) [ C# ](functions-reference-csharp.md) [ C# (sınıf kitaplığı)](functions-dotnet-class-library.md), betik (. CSX), JavaScript, PowerShell, Python | [Azure Functions Core Tools] , yerel geliştirmeyi etkinleştiren işlevler oluşturmak için çekirdek çalışma zamanı ve şablonları sağlar. Sürüm 2. x, Linux, MacOS ve Windows üzerinde geliştirmeyi destekler. Tüm ortamlar yerel Işlevler çalışma zamanına yönelik temel araçlara güvenir. |
| [Visual Studio 2019](functions-develop-vs.md) | [C#(sınıf kitaplığı)](functions-dotnet-class-library.md) | Azure Işlevleri araçları, [Visual Studio 2019](https://www.visualstudio.com/vs/) ve sonraki sürümlerin **Azure geliştirme** iş yüküne dahildir. Bir sınıf kitaplığındaki işlevleri derlemenize ve. dll dosyasını Azure 'a yayımlamanıza olanak sağlar. Yerel test için temel araçları içerir. Daha fazla bilgi için bkz. [Visual Studio kullanarak Azure Işlevleri geliştirme](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (çeşitli) | [Java](functions-reference-java.md) | , Java işlevlerinin geliştirilmesini sağlamak için çekirdek araçlarla tümleştirilir. Sürüm 2. x, Linux, MacOS ve Windows üzerinde geliştirmeyi destekler. Daha fazla bilgi için bkz. [Java ve Maven ile ilk işlevinizi oluşturma](functions-create-first-java-maven.md). Ayrıca, [Çakışan Küreler](functions-create-maven-eclipse.md) ve [IntelliJ fikrini](functions-create-maven-intellij.md) kullanarak geliştirmeyi destekler |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Bu yerel geliştirme ortamlarının her biri, işlev uygulaması projeleri oluşturmanıza ve yeni işlevler oluşturmak için önceden tanımlanmış Işlevler şablonlarını kullanmanıza imkan sağlar. , İşlevlerinizi diğer uygulamalarda olduğu gibi kendi makinenizde gerçek Işlevler çalışma zamanına karşı sınayabilmeniz ve hatalarını ayıklayabilmeniz için temel araçları kullanır. Ayrıca, bu ortamların herhangi birinden işlev uygulama projenizi Azure 'a yayımlayabilirsiniz.  

## <a name="next-steps"></a>Sonraki adımlar

+ Visual Studio 2019 kullanarak derlenmiş C# işlevlerin yerel geliştirmesi hakkında daha fazla bilgi edinmek için bkz. [Visual Studio kullanarak Azure işlevleri geliştirme](functions-develop-vs.md).
+ Bir Mac, Linux veya Windows bilgisayarında VS Code kullanarak işlevlerin yerel geliştirmesi hakkında daha fazla bilgi edinmek için, bkz. [vs Code Azure Işlevlerini dağıtma](/azure/javascript/tutorial-vscode-serverless-node-01).
+ Komut isteminden veya terminalden işlev geliştirme hakkında daha fazla bilgi için bkz. [Azure Functions Core Tools Ile çalışma](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
