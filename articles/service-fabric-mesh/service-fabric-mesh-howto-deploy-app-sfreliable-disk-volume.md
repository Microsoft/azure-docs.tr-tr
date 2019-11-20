---
title: Azure Service Fabric kafes uygulamasında yüksek düzeyde kullanılabilir Service Fabric güvenilir disk birimi kullanma | Microsoft Docs
description: Azure CLı kullanarak kapsayıcının içinde güvenilir disk tabanlı birim Service Fabric bağlayarak durumu bir Azure Service Fabric kafes uygulamasında depolamayı öğrenin.
services: service-fabric-mesh
documentationcenter: .net
author: ashishnegi
manager: raunakpandya
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: 25bd298c412db38ec4d3b7859580d58ac9b151fb
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036153"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Yüksek oranda kullanılabilir Service Fabric Service Fabric bir kafes uygulamasında güvenilir disk tabanlı birim bağlama 
Kapsayıcı uygulamalarla kalıcı durumun yaygın bir yöntemi olan Azure dosya depolama gibi uzak depolamayı veya Azure Cosmos DB gibi veritabanını kullanmaktır. Bu, uzak mağazaya önemli okuma ve yazma gecikme süresi doğurur.

Bu makalede, bir Service Fabric kafes uygulamasının kapsayıcısının içine bir birim bağlayarak yüksek oranda kullanılabilir Service Fabric güvenilir diskte nasıl depolanacağını gösterir.
Service Fabric güvenilir disk, yüksek kullanılabilirlik için Service Fabric kümesi içinde çoğaltılan yazma işlemleri ile yerel okuma birimleri sağlar. Bu, okuma için ağ çağrılarını kaldırır ve yazma işlemleri için ağ gecikmesini azaltır. Kapsayıcı yeniden başlatılır veya başka bir düğüme taşınırsa, yeni kapsayıcı örneği eski bir birimle aynı birimi görür. Bu nedenle, verimli ve yüksek oranda kullanılabilir.

Bu örnekte, sayaç uygulamasının bir tarayıcıda sayaç değerini gösteren bir ASP.NET Core hizmeti vardır.

`counterService` Düzenli aralıklarla bir dosyadaki sayaç değerini okur, artırır ve dosyaya geri yazar. Dosya, Service Fabric güvenilir disk tarafından desteklenen birimde takılı bir klasörde depolanır.

## <a name="prerequisites"></a>Önkoşullar

Bu görevi gerçekleştirmek için Azure Cloud Shell veya yerel bir Azure CLı yüklemesi kullanabilirsiniz. Azure CLI 'yi bu makaleyle birlikte kullanmak için, en azından `az --version` `azure-cli (2.0.43)`döndürdüğünden emin olun.  Bu [yönergeleri](service-fabric-mesh-howto-setup-cli.md)izleyerek Azure SERVICE fabrıc kafes CLI uzantısı modülünü yükler (veya güncelleştirir).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure'da oturum açın ve aboneliğinizi ayarlayın.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Uygulamanın dağıtılacağı kaynak grubunu oluşturun. Aşağıdaki komut, Doğu Birleşik Devletler bir konumda adlı `myResourceGroup` bir kaynak grubu oluşturur. Aşağıdaki komutta kaynak grubu adını değiştirirseniz, bunu izleyen tüm komutlarda değiştirmeyi unutmayın.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Şablonu dağıtma

Aşağıdaki komut, [. sfreliablevolume. Linux. JSON şablonunu](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json)kullanarak bir Linux uygulaması dağıtır. Bir Windows uygulamasını dağıtmak için [Counter. sfreliablevolume. Windows. JSON şablonunu](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json)kullanın. Daha büyük kapsayıcı görüntülerinin dağıtmanın daha uzun sürebildiği farkında olun.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

Ayrıca, komutuyla dağıtım durumunu da görebilirsiniz

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Kaynak türü olarak `Microsoft.ServiceFabricMesh/gateways`bulunan ağ geçidi kaynağının adına dikkat edin. Bu, uygulamanın genel IP adresini almak için kullanılır.

## <a name="open-the-application"></a>Uygulamayı açma

Uygulama başarıyla dağıtıldıktan sonra, uygulamanın ağ geçidi kaynağının IPAddress değerini alın. Yukarıdaki bölümde fark ettiğiniz ağ geçidi adını kullanın.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

Çıkışın, hizmet uç noktası için `ipAddress` genel IP adresi olan bir özelliği olmalıdır. Bir tarayıcıdan açın. Her saniyede güncelleştirilmekte olan sayaç değerinin bulunduğu bir Web sayfası görüntülenir.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Uygulamanın birimi kullanbildiğini doğrulama

Uygulama, klasörünün içindeki `counter.txt` `counter/counterService` birimde adlı bir dosya oluşturur. Bu dosyanın içeriği, Web sayfasında görüntülenmekte olan sayaç değeridir.

## <a name="delete-the-resources"></a>Kaynakları Sil

Artık Azure 'da kullanmadığınız kaynakları sık sık silin. Bu örnekle ilgili kaynakları silmek için, dağıtıldığı kaynak grubu (kaynak grubuyla ilişkili her şeyi siler) aşağıdaki komutla silin:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

- [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter)'Da güvenilir birim disk örnek uygulamasını Service Fabric görüntüleyin.
- Service Fabric Kaynak Modeli hakkında daha fazla bilgi için bkz. [Service Fabric Mesh Kaynak Modeli](service-fabric-mesh-service-fabric-resources.md).
- Service Fabric Mesh hakkında daha fazla bilgi edinmek için [Service Fabric Mesh’e genel bakış](service-fabric-mesh-overview.md) makalesini okuyun.
