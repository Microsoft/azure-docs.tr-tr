---
title: Öğretici-Azure Service Fabric kafes kaynaklarını Temizleme
description: Artık kullanmadığınız kaynaklara ücret ödememek için Azure Service Fabric Mesh kaynaklarını kaldırmayı öğrenin.
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 4d78e1e27d70b147bb52dff13675e63b79335d62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625473"
---
# <a name="tutorial-remove-azure-resources"></a>Öğretici: Azure kaynaklarını kaldırma

> [!IMPORTANT]
> Azure Service Fabric ağı önizlemesi devre dışı bırakıldı. Yeni dağıtımlar Service Fabric kafes API 'SI aracılığıyla artık izin verilmeyecektir. Mevcut dağıtımlar için destek 28 Nisan 2021 ' den devam edecektir.
> 
> Ayrıntılar için bkz. [Azure Service Fabric kafes önizleme kullanımdan](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)kaldırma.

Bu öğretici bir serinin beşinci bölümüdür ve uygulamayla uygulamanın kaynaklarına ücret ödememeniz için bunları nasıl sileceğinizi gösterir.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * Uygulama tarafından kullanılan kaynakları temizlerseniz bunlar için ücretlendirilmezsiniz.

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * [Visual Studio’da Service Fabric Mesh uygulaması oluşturma](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Yerel geliştirme kümenizde çalışan bir Service Fabric Mesh uygulamasının hatalarını ayıklama](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Service Fabric Mesh uygulaması dağıtma](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Service Fabric Mesh uygulamasını yükseltme](service-fabric-mesh-tutorial-upgrade.md)
> * Service Fabric Mesh kaynaklarını temizleme

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* To-Do uygulamasını dağıtmadıysanız, [Service Fabric Mesh web uygulamasını yayımlama](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md) başlığı altında verilen yönergeleri izleyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Öğreticinin sonuna geldiniz. Oluşturduğunuz kaynaklarla işiniz bittiğinde, bunları silin. Böylece artık kullanmadığınız kaynaklar için ödeme yapmazsınız. Mesh saniye başına faturalandırılan sunucusuz bir hizmet olduğundan, bu özellikle önemlidir. Mesh fiyatlandırması hakkında daha fazla bilgi edinmek için https://aka.ms/sfmeshpricing bağlantısına bakın.

Azure'un sağladığı kolaylıklardan biri de, belirli bir kaynak grubuyla ilişkilendirilmiş kaynaklar oluşturduğunuzda, kaynak grubunu silmenin ilişkili tüm kaynakların da silinmesine neden olmasıdır. Bu yolla, bunları birer birer silmek zorunda kalmazsınız.

To-Do uygulamasını oluşturmak amacıyla yeni bir kaynak grubu oluşturduğunuz için, bu kaynak grubunu silerek ilgili tüm kaynakların da silinmesini sağlayabilirsiniz.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Alternatif olarak **sfmeshTutorial1RG** kaynak grubunu [portaldan](../azure-resource-manager/management/manage-resource-groups-portal.md#delete-resource-groups) silebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Azure'da bir Service Fabric Mesh uygulaması yayımlamayı tamamladığınıza göre aşağıdakileri deneyebilirsiniz:

* Hizmetler arası iletişimin farklı bir örneğini görmek için [Oy verme uygulaması örneğini](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) inceleyin.
* Service Fabric Kaynak Modeli hakkında daha fazla bilgi için bkz. [Service Fabric Mesh Kaynak Modeli](service-fabric-mesh-service-fabric-resources.md).
* Service Fabric Mesh hakkında daha fazla bilgi edinmek için [Service Fabric Mesh’e genel bakış](service-fabric-mesh-overview.md) makalesini okuyun.
* [Cloud Shell](../cloud-shell/overview.md) hakkında bilgi edinin
