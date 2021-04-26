---
title: Visual Studio Code nasıl kullanılır Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Visual Studio Code ve Azure Dev Spaces Kubernetes uygulamalarınızı hata ayıklamanıza ve hızla yinelemenize nasıl yardımcı olduğunu öğrenin
keywords: Azure Dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar
ms.openlocfilehash: edfcb8107280bb86144b798da2d5b1c16371528e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960754"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Visual Studio Code nasıl kullanılır Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Visual Studio Code ve [Azure dev Spaces uzantısını][azds-extension] kullanarak hizmetlerinizi Azure dev Spaces hazırlayın, çalıştırabilir ve hatalarını ayıklayabilirsiniz. Visual Studio Code ve Azure Dev Spaces uzantısıyla şunları yapabilirsiniz:

* AKS 'de çalışan ve hata ayıklama Hizmetleri için varlıklar oluşturma
* Java, Node.js ve .NET Core hizmetlerinizi bir geliştirme alanında çalıştırın
* Geliştirme alanında çalışan Java, Node.js ve .NET Core hizmetlerinde doğrudan hata ayıklayın

## <a name="generate-assets"></a>Varlık oluşturma

Visual Studio Code ve Azure Dev Spaces uzantısı projeniz için aşağıdaki varlıkları oluşturur:

* Maven, Node.js uygulamaları ve .NET Core uygulamalarını kullanarak Java uygulamaları için dockerfiles
* Dockerfile ile neredeyse tüm diller için helb grafikleri
* `azds.yaml`Projeniz için [Azure dev Spaces yapılandırma dosyası][azds-yaml] olan bir dosya
* `.vscode`Maven, Node.js uygulamaları ve .NET Core uygulamalarını kullanarak Java uygulamaları için projenizin Visual Studio Code başlatma yapılandırmasına sahip bir klasör

Dockerfile, HELI grafiği ve `azds.yaml` dosyaları çalışırken oluşturulan varlıklardır `azds prep` . Bu dosyalar, Visual Studio Code dışında, projenizi çalıştırmak gibi AKS 'de çalıştırmak için de kullanılabilir `azds up` . `.vscode`Klasör yalnızca Visual Studio Code tarafından Visual Studio Code ' den AKS 'de Projenizi çalıştırmak için kullanılır.

## <a name="run-your-service-in-aks"></a>Hizmetinizi AKS 'de çalıştırın

Projeniz için varlıkları oluşturduktan sonra, Java, Node.js ve .NET Core hizmetlerinizi Visual Studio Code mevcut bir geliştirme alanında çalıştırabilirsiniz. Visual Studio Code *hata ayıklama* sayfasında, `.vscode` Projenizi çalıştırmak için dizinden başlatma yapılandırmasını çağırabilirsiniz.

AKS kümenizi oluşturmanız ve kümenizdeki Azure Dev Spaces Visual Studio Code dışında etkinleştirmeniz gerekir. Mevcut Dockerfiles, helk grafiklerini ve `azds.yaml` Visual Studio Code dışında oluşturulan dosyaları, örneğin çalıştırılarak oluşturulan varlıklar gibi yeniden kullanabilirsiniz `azds prep` . Visual Studio Code dışında oluşturulan varlıkları yeniden kullanıyorsanız, yine de bir dizininiz olması gerekir `.vscode` . Bu `.vscode` Dizin, Visual Studio Code ve Azure dev Spaces uzantısı tarafından yeniden üretilebilir ve mevcut varlıklarınızın üzerine yazılmayacak.

.NET Core projelerinde, .NET hizmetinizi Visual Studio Code çalıştırmak için [C# uzantısının][csharp-extension] yüklü olması gerekir. Ayrıca Maven kullanan Java projeleri için, [Azure dev Spaces uzantısı Için Java Debugger][java-extension] 'ın yanı sıra, Visual Studio Code ' dan Java hizmetinizi çalıştıracak şekilde [yüklenip yapılandırılmış][maven] olması gerekir.

## <a name="debug-your-service-in-aks"></a>AKS 'de hizmetinizde hata ayıklama

Projenizi başlattıktan sonra doğrudan Visual Studio Code bir geliştirme alanında çalışan Java, Node.js ve .NET Core hizmetlerinde hata ayıklaması yapabilirsiniz. Dizindeki başlatma yapılandırması, `.vscode` bir geliştirme alanında hata ayıklama etkinken bir hizmeti çalıştırmaya yönelik ek hata ayıklama bilgileri sağlar. Visual Studio Code Ayrıca, geliştirme boşluklarında çalışan kapsayıcıda hata ayıklama işlemine ekler. böylece kesme noktaları ayarlayabilir, değişkenleri inceleyebilir ve diğer hata ayıklama işlemlerini gerçekleştirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure Dev Spaces nasıl çalıştığı hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Azure Dev Spaces nasıl çalışır?](how-dev-spaces-works.md)

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
