---
title: Kapsayıcı grubunu Güncelleştir
description: Azure Container Instances kapsayıcı gruplarınızdaki çalışan kapsayıcıları güncelleştirme hakkında bilgi edinin.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: cbb2e830490d2591645b8156ee830856da0f9049
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786972"
---
# <a name="update-containers-in-azure-container-instances"></a>Azure Container Instances’taki kapsayıcıları güncelleştirme

Kapsayıcı örneklerinizin normal işlemleri sırasında, bir [kapsayıcı grubundaki](./container-instances-container-groups.md)çalışan kapsayıcıları güncelleştirmek için gerekli olduğunu fark edebilirsiniz. Örneğin, bir görüntü sürümü, DNS adı veya ortam değişkeni gibi bir özelliği güncelleştirmek ya da uygulaması kilitlenen bir kapsayıcıdaki bir özelliği yenilemek isteyebilirsiniz.

Mevcut bir grubu en az bir Modified özelliği ile yeniden dağıtarak çalışan bir kapsayıcı grubundaki kapsayıcıları güncelleştirin. Bir kapsayıcı grubunu güncelleştirdiğinizde gruptaki tüm çalışan kapsayıcılar, genellikle aynı temel kapsayıcı ana bilgisayarında yerinde yeniden başlatılır.

> [!NOTE]
> Sonlandırılan veya silinen kapsayıcı grupları güncelleştirilemiyor. Bir kapsayıcı grubu sonlandırıldıktan sonra (başarılı veya başarısız durumda) veya silinmişse, grup yeni olarak dağıtılmalıdır. Diğer [sınırlamalara](#limitations)bakın.

## <a name="update-a-container-group"></a>Kapsayıcı grubunu güncelleştirme

Var olan bir kapsayıcı grubunu güncelleştirmek için:

* Oluşturma komutunu verme (veya Azure portal kullanın) ve var olan bir grubun adını belirtin 
* Yeniden dağıtırken güncelleştirmeyi destekleyen grubun en az bir özelliğini değiştirin veya ekleyin. Bazı özellikler [güncelleştirmeleri desteklemez](#properties-that-require-container-delete).
* Diğer özellikleri daha önce belirttiğiniz değerlerle ayarlayın. Bir özellik için değer ayarlamazsanız, varsayılan değerine döner.

> [!TIP]
> [YAML dosyası](./container-instances-container-groups.md#deployment) bir kapsayıcı grubunun dağıtım yapılandırmasını sürdürmenize yardımcı olur ve güncelleştirilmiş bir grubu dağıtmak için bir başlangıç noktası sağlar. Grubu oluşturmak için farklı bir yöntem kullandıysanız, [az Container Export][az-container-export]kullanarak yapılandırmayı YAML 'ye dışarı aktarabilirsiniz. 

### <a name="example"></a>Örnek

Aşağıdaki Azure CLı örneği, bir kapsayıcı grubunu yeni bir DNS ad etiketi ile güncelleştirir. Grubun DNS ad etiketi özelliği güncelleştirilabilecek bir tane olduğundan, kapsayıcı grubu yeniden dağıtılır ve kapsayıcıları yeniden başlatılır.

DNS ad etiketi *MyApplication-hazırlama* ile ilk dağıtım:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Kapsayıcı grubunu yeni bir DNS adı etiketiyle güncelleştirin, *MyApplication* ve kalan özellikleri daha önce kullanılan değerlerle ayarlayın:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Güncelleştirme avantajları

Mevcut bir kapsayıcı grubunu güncelleştirmenin birincil avantajı daha hızlı dağıtımdır. Var olan bir kapsayıcı grubunu yeniden dağıttığınızda, kapsayıcı görüntüsü katmanları önceki dağıtım tarafından önbelleğe alınmış olanlardan alınır. Yeni dağıtımlarla yapıldığı gibi tüm görüntü katmanlarını kayıt defterinden yeni bir şekilde çekmek yerine yalnızca değiştirilen Katmanlar (varsa) çekilerek.

Windows Server Core gibi daha büyük kapsayıcı görüntülerini temel alan uygulamalar, silme ve yeni dağıtım yerine güncelleştirme yaptığınızda dağıtım hızında önemli bir geliştirme görülebilirler.

## <a name="limitations"></a>Sınırlamalar

* Bir kapsayıcı grubunun tüm özellikleri güncelleştirmeleri desteklemez. Bir kapsayıcı grubunun bazı özelliklerini değiştirmek için, önce grubu silmeniz ve sonra yeniden dağıtmanız gerekir. Bkz. [kapsayıcı silme gerektiren özellikler](#properties-that-require-container-delete).
* Kapsayıcı grubunu güncelleştirdiğinizde bir kapsayıcı grubundaki tüm kapsayıcılar yeniden başlatılır. Çok kapsayıcılı bir gruptaki belirli bir kapsayıcının güncelleştirmesini veya yerinde yeniden başlatılmasını gerçekleştiremezsiniz.
* Bir kapsayıcı grubunun IP adresi genellikle güncelleştirmeler arasında tutulur, ancak aynı kalmayacakları garanti edilmez. Kapsayıcı grubu aynı temel ana bilgisayara dağıtıldığı sürece kapsayıcı grubu IP adresini korur. Nadir olsa da, farklı bir konağa yeniden dağıtım yapılmasına neden olabilecek bazı Azure iç olayları vardır. Bu sorunu azaltmak için, kapsayıcı örneklerinizin bir DNS ad etiketi kullanmanızı öneririz.
* Sonlandırılan veya silinen kapsayıcı grupları güncelleştirilemiyor. Bir kapsayıcı grubu durdurulduğunda ( *sonlandırılmış* durumdaysa) veya silinirse, grup yeni olarak dağıtılır.

## <a name="properties-that-require-container-delete"></a>Kapsayıcı silme gerektiren özellikler

Tüm kapsayıcı grubu özellikleri güncelleştirilemiyor. Örneğin, bir kapsayıcının yeniden başlatma ilkesini değiştirmek için, önce kapsayıcı grubunu silip yeniden oluşturmanız gerekir.

Bu özelliklerde yapılan değişiklikler yeniden dağıtım öncesinde kapsayıcı grubu silmeyi gerektirir:

* İşletim sistemi türü
* CPU, bellek veya GPU kaynakları
* Yeniden başlatma ilkesi
* Ağ profili

Bir kapsayıcı grubunu silip yeniden oluşturduğunuzda, "yeniden dağıtılır" ancak yeni oluşturulur. Tüm görüntü katmanları, önceki bir dağıtım tarafından önbelleğe alınanlardan değil, kayıt defterinden alınır. Kapsayıcının IP adresi, farklı bir temel konağa dağıtılmadığı için de değişebilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede birkaç kez bahsedildiği **kapsayıcı grubu**. Azure Container Instances içindeki her kapsayıcı bir kapsayıcı grubuna dağıtılır ve kapsayıcı grupları birden fazla kapsayıcı içerebilir.

[Azure Container Instances’taki kapsayıcı grupları](./container-instances-container-groups.md)

[Birden çok kapsayıcılı grup dağıtma](container-instances-multi-container-group.md)

[Azure Container Instances’taki kapsayıcıları el ile durdurma veya başlatma](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az_container_export
