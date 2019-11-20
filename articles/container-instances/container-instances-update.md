---
title: Azure Container Instances kapsayıcıları güncelleştirme
description: Azure Container Instances kapsayıcı gruplarınızdaki çalışan kapsayıcıları güncelleştirme hakkında bilgi edinin.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 09/03/2019
ms.author: danlep
ms.openlocfilehash: 3103fe7fbf7dcd587f43b673ef53f32893908ecb
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307706"
---
# <a name="update-containers-in-azure-container-instances"></a>Azure Container Instances kapsayıcıları güncelleştirme

Kapsayıcı örneklerinizin normal işlemleri sırasında, bir [kapsayıcı grubundaki](container-instances-container-groups.md)çalışan kapsayıcıları güncelleştirmek için gerekli olduğunu fark edebilirsiniz. Örneğin, görüntü sürümünü güncelleştirmek, bir DNS adını değiştirmek, ortam değişkenlerini güncelleştirmek veya uygulaması kilitlenen bir kapsayıcının durumunu yenilemek isteyebilirsiniz.

> [!NOTE]
> Sonlandırılan veya silinen kapsayıcı grupları güncelleştirilemiyor. Bir kapsayıcı grubu sonlandırıldıktan sonra (başarılı veya başarısız durumda) veya silinmişse, grup yeni olarak dağıtılmalıdır.

## <a name="update-a-container-group"></a>Kapsayıcı grubunu güncelleştirme

Mevcut bir grubu en az bir Modified özelliği ile yeniden dağıtarak çalışan bir kapsayıcı grubundaki kapsayıcıları güncelleştirin. Bir kapsayıcı grubunu güncelleştirdiğinizde gruptaki tüm çalışan kapsayıcılar, genellikle aynı temel kapsayıcı ana bilgisayarında yerinde yeniden başlatılır.

Oluşturma komutunu vererek mevcut bir kapsayıcı grubunu yeniden dağıtın (veya Azure portal kullanın) ve var olan bir grubun adını belirtin. Yeniden dağıtımı tetiklemek için Create komutunu verdiğinizde ve kalan özellikleri değişmeden bırakarak (veya varsayılan değerleri kullanmaya devam et) grubun en az bir geçerli özelliğini değiştirin. Tüm kapsayıcı grubu özellikleri yeniden dağıtım için geçerli değildir. Desteklenmeyen özelliklerin listesi için [silme gerektiren özelliklere](#properties-that-require-container-delete) bakın.

Aşağıdaki Azure CLı örneği, bir kapsayıcı grubunu yeni bir DNS ad etiketi ile güncelleştirir. Grubun DNS ad etiketi özelliği güncelleştirilabilecek bir tane olduğundan, kapsayıcı grubu yeniden dağıtılır ve kapsayıcıları yeniden başlatılır.

DNS ad etiketi *MyApplication-hazırlama*ile ilk dağıtım:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Kapsayıcı grubunu yeni bir DNS adı etiketiyle güncelleştirin, *MyApplication*ve kalan özellikleri değiştirmeden bırakın:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Güncelleştirme avantajları

Mevcut bir kapsayıcı grubunu güncelleştirmenin birincil avantajı daha hızlı dağıtımdır. Var olan bir kapsayıcı grubunu yeniden dağıttığınızda, kapsayıcı görüntüsü katmanları önceki dağıtım tarafından önbelleğe alınmış olanlardan alınır. Yeni dağıtımlarla yapıldığı gibi tüm görüntü katmanlarını kayıt defterinden yeni bir şekilde çekmek yerine yalnızca değiştirilen Katmanlar (varsa) çekilerek.

Windows Server Core gibi daha büyük kapsayıcı görüntülerini temel alan uygulamalar, silme ve yeni dağıtım yerine güncelleştirme yaptığınızda dağıtım hızında önemli bir geliştirme görülebilirler.

## <a name="limitations"></a>Sınırlamalar

Bir kapsayıcı grubunun tüm özellikleri güncelleştirmeleri desteklemez. Bir kapsayıcı grubunun bazı özelliklerini değiştirmek için, önce grubu silmeniz ve sonra yeniden dağıtmanız gerekir. Ayrıntılar için bkz. [kapsayıcı silme gerektiren özellikler](#properties-that-require-container-delete).

Kapsayıcı grubunu güncelleştirdiğinizde bir kapsayıcı grubundaki tüm kapsayıcılar yeniden başlatılır. Çok kapsayıcılı bir gruptaki belirli bir kapsayıcının güncelleştirmesini veya yerinde yeniden başlatılmasını gerçekleştiremezsiniz.

Bir kapsayıcının IP adresi genellikle güncelleştirmeler arasında değişiklik görmez, ancak aynı kalmayacağından garanti edilmez. Kapsayıcı grubu aynı temel ana bilgisayara dağıtıldığı sürece kapsayıcı grubu IP adresini korur. Nadir olarak Azure Container Instances, ancak aynı ana bilgisayara yeniden dağıtmaya yönelik her çabaya rağmen, farklı bir konağa yeniden dağıtım yapılmasına neden olabilecek bazı Azure iç olayları vardır. Bu sorunu azaltmak için, kapsayıcı örneklerinizin her zaman bir DNS ad etiketi kullanın.

Sonlandırılan veya silinen kapsayıcı grupları güncelleştirilemiyor. Bir kapsayıcı grubu durdurulduğunda ( *sonlandırılmış* durumdaysa) veya silinmişse, grup yeni olarak dağıtılır.

## <a name="properties-that-require-container-delete"></a>Kapsayıcı silme gerektiren özellikler

Daha önce belirtildiği gibi, tüm kapsayıcı grubu özellikleri güncelleştirilemeyebilir. Örneğin, bağlantı noktalarını değiştirmek veya bir kapsayıcının ilkesini yeniden başlatmak için, önce kapsayıcı grubunu silip yeniden oluşturmanız gerekir.

Bu özellikler yeniden dağıtım öncesinde kapsayıcı grubu silmeyi gerektirir:

* İşletim sistemi türü
* CPU
* Bellek
* Yeniden başlatma ilkesi
* Bağlantı Noktaları

Bir kapsayıcı grubunu silip yeniden oluşturduğunuzda, "yeniden dağıtılır" ancak yeni oluşturulur. Tüm görüntü katmanları, önceki bir dağıtım tarafından önbelleğe alınanlardan değil, kayıt defterinden alınır. Kapsayıcının IP adresi, farklı bir temel konağa dağıtılmadığı için de değişebilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede birkaç kez bahsedildiği **kapsayıcı grubu**. Azure Container Instances içindeki her kapsayıcı bir kapsayıcı grubuna dağıtılır ve kapsayıcı grupları birden fazla kapsayıcı içerebilir.

[Azure Container Instances’taki kapsayıcı grupları](container-instances-container-groups.md)

[Çok kapsayıcılı bir grup dağıtın](container-instances-multi-container-group.md)

[Azure Container Instances kapsayıcıları el ile durdurma veya başlatma](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
