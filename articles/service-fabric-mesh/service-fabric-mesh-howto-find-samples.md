---
title: Azure Service Fabric kafes örneklerini bulma
description: Kullanılabilir Service Fabric kafes örnek uygulamalarının bir dizini aşağıda verilmiştir. Bu örneklerdeki kaynak kodu, Service Fabric kaynak modeli kullanılarak belirli bir senaryonun nasıl elde alınacağını gösterir.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: 2b38cd6a6c0f3aaab4ff5b77be82aee242afef23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627037"
---
# <a name="find-service-fabric-mesh-samples"></a>Service Fabric ağ örneklerini bulma

> [!IMPORTANT]
> Azure Service Fabric ağı önizlemesi devre dışı bırakıldı. Yeni dağıtımlar Service Fabric kafes API 'SI aracılığıyla artık izin verilmeyecektir. Mevcut dağıtımlar için destek 28 Nisan 2021 ' den devam edecektir.
> 
> Ayrıntılar için bkz. [Azure Service Fabric kafes önizleme kullanımdan](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)kaldırma.

Bu tabloda, kullanılabilir Service Fabric kafes örnek uygulamaları özetlenmektedir. Bu örneklerdeki kaynak kodu, Service Fabric kaynak modeli kullanılarak belirli bir senaryonun nasıl elde alınacağını gösterir.

Şablonları doğrudan Azure 'a dağıtma hakkında daha fazla bilgi için bkz [. örnek şablon GitHub sayfası.](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)

|Örnek şablon|Senaryo Açıklaması|Kaynak Kodu|Geliştirici Araçları|
|------------|--------------------|----------|----------------------|
| [Merhaba Dünya uygulaması](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Kapsayıcıda barındırılan statik Web sayfası. Linux için Windows IIS için NGINX kullanır | [Kaynak Kodu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Gereksinim yok |
| [Azure dosya birimleri için sayaç uygulaması](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Kapsayıcının içindeki Azure dosya tabanlı birimi bağlayarak durumu saklayın. <br><br> **Note:** Bu şablon, zaten sağlanmış [yönergeler](../storage/files/storage-how-to-create-file-share.md) Için bir Azure dosyaları dosya paylaşımında gereklidir | [Kaynak Kodu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio kafes araçları |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | DNS tabanlı çözüm kullanan ön uç ve arka uç hizmetiyle bir uygulama oluşturun. [Burada](./service-fabric-mesh-tutorial-create-dotnetcore.md) öğretici olarak kullanılır | [Kaynak Kodu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Visual Studio kafes araçları |
| [Görsel nesneler uygulaması](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Bir uygulama içindeki mikro hizmetleri ölçeklendirin ve yükseltin. | [Kaynak Kodu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Visual Studio kafes araçları |
| [Oylama uygulaması](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | DNS tabanlı çözüm kullanan ön uç ve arka uç hizmetiyle uygulama oluşturma | [Kaynak Kodu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Windows sürümü için Visual Studio kafes araçları, VS Code/DotNet CLı, Linux sürümü için kullanılabilir |
| [Service Fabric güvenilir birimler için sayaç uygulaması](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Kapsayıcının içinde güvenilir disk tabanlı birim Service Fabric bağlayarak durumu saklayın.| [Kaynak Kodu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio kafes araçları |
