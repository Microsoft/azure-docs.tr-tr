---
title: En iyi kayıt defteri uygulamalar
description: Bu en iyi yöntemleri izleyerek Azure kapsayıcı kayıt defterinizi nasıl verimli bir şekilde kullanabileceğinizi öğrenin.
ms.topic: article
ms.date: 01/07/2021
ms.openlocfilehash: 0811cc4a5bffc21ffba19e64a3887eab6bc36fbb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784146"
---
# <a name="best-practices-for-azure-container-registry"></a>Azure Container Registry için en iyi yöntemler

Bu en iyi yöntemleri izleyerek kapsayıcı görüntülerini ve diğer yapıtları depolamak ve dağıtmak için Azure 'da özel kayıt defterinizin performansını ve ekonomik kullanımını en üst düzeye çıkarmanıza yardımcı olabilirsiniz.

Kayıt defteri kavramlarıyla ilgili arka plan için bkz. kayıt [defterleri, depolar ve görüntüler hakkında](container-registry-concepts.md). Ayrıca bkz. kayıt defterinizde resimleri etiketlendirme ve sürüm görüntülerinin yer aldığı stratejiler için [kapsayıcı görüntülerini etiketleme ve oluşturma önerilerine](container-registry-image-tag-version.md) bakın. 

## <a name="network-close-deployment"></a>Yakın ağ dağıtımı

Kapsayıcı kayıt defterinizi, kapsayıcıları dağıttığınız Azure bölgesinde oluşturun. Kayıt defterinizin ağ açısından kapsayıcı konaklarınıza yakın bir bölgeye yerleştirilmesi hem gecikmeyi hem de maliyeti düşürmeye yardımcı olabilir.

Yakın ağ dağıtımı, özel kapsayıcı kayıt defteri kullanmanın birincil nedenlerinden biridir. Docker görüntülerinin kademeli dağıtıma imkan tanıyan etkili bir [katman yapısı](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) vardır. Bununla birlikte, yeni düğümlerin belirli bir görüntü için gereken tüm katmanları çekmesi gerekir. Bu ilk `docker pull` hızla büyüyerek birkaç gigabayta ulaşabilir. Dağıtımınıza yakın özel bir kayıt defterinin olması bu ağ gecikmesini en aza indirir.
Buna ek olarak, Azure dahil tüm genel bulutlar ağ çıkışı ücretleri uygular. Bir veri merkezinden diğerine görüntü çekmek, gecikmeye ek olarak ağ çıkışı ücretlerine yol açar.

## <a name="geo-replicate-multi-region-deployments"></a>Çok bölgeli dağıtımları coğrafi olarak çoğaltma

Birden çok bölgeye kapsayıcı dağıtıyorsanız Azure Container Registry'nin [coğrafi çoğaltma](container-registry-geo-replication.md) özelliğini kullanın. İster yerel veri merkezlerinden küresel müşterilere hizmet sunuyor olun ister geliştirme takımınız farklı konumlarda çalışıyor olsun, kayıt defterinizi coğrafi olarak çoğaltarak kayıt defteri yönetimini basitleştirebilir ve gecikmeyi en aza indirebilirsiniz. Ayrıca, resimlerin ne zaman gönderildiği gibi belirli çoğaltmalarda olayları bilgilendirmek için bölgesel [Web kancalarını](container-registry-webhook.md) yapılandırabilirsiniz.

Coğrafi çoğaltma, [Premium](container-registry-skus.md) kayıt defterleri ile kullanılabilir. Coğrafi çoğaltma özelliğini kullanmayı öğrenmek için [Azure Container Registry’de coğrafi çoğaltma](container-registry-tutorial-prepare-registry.md) başlıklı üç bölümlü öğreticiye bakın.

## <a name="maximize-pull-performance"></a>Çekme performansını en üst düzeye çıkarma

Görüntülerin dağıtımlarınıza yakın şekilde eklenmesinin yanı sıra, görüntülerinizin özellikleri, çekme performansını etkileyebilir.

* **Görüntü boyutu** -gereksiz [katmanları](container-registry-concepts.md#manifest) kaldırarak veya katmanların boyutunu azaltarak görüntülerinizin boyutlarını küçültün. Görüntü boyutunu azaltmanın bir yolu, yalnızca gerekli çalışma zamanı bileşenlerini dahil etmek için [Multi-Stage Docker derleme](https://docs.docker.com/develop/develop-images/multistage-build/) yaklaşımını kullanmaktır. 

  Ayrıca, resminizin daha hafif bir temel işletim sistemi görüntüsü içerip içermediğini denetleyin. Belirli temel görüntüleri önbelleğe alan Azure Container Instances gibi bir dağıtım ortamı kullanırsanız, bir görüntü katmanını önbelleğe alınmış görüntülerden birine takabilmeniz gerekip gerekmediğini kontrol edin. 
* **Katman sayısı** -kullanılan katman sayısını dengeleyin. Çok az sayıda varsa, konakta katman yeniden kullanımı ve önbelleğe alma özelliğinden faydalanmazsınız. Çok fazla ve dağıtım ortamınız çekme ve sıkıştırmayı daha fazla zaman harcadığını. Beş ila 10 katman idealdir.

Ayrıca, performans ihtiyaçlarınızı karşılayan bir Azure Container Registry [hizmet katmanını](container-registry-skus.md) da seçin. Premium katmanı, yüksek hacimli dağıtımlarınız olduğunda, eşzamanlı okuma ve yazma işlemlerinin en büyük bant genişliğini ve en yüksek oranını sağlar.

## <a name="repository-namespaces"></a>Depo ad alanları

Depo ad alanlarını kullanarak, kuruluşunuzda birden çok grup arasında tek bir kayıt defterinin paylaşılmasına izin verebilirsiniz. Kayıt defterleri farklı dağıtımlarla ve takımlarla paylaşılabilir. İç içe ad alanlarını destekleyen Azure Container Registry, grup yalıtımı imkanı sunar. Ancak kayıt defteri, tüm depoları bağımsız olarak değil, bağımsız olarak yönetir.

Örneğin, aşağıdaki kapsayıcı görüntüsü etiketlerini göz önünde bulundurun. Şirket genelinde kullanılan resimler `aspnetcore` kök ad alanına yerleştirilir, ancak ürünlere ve pazarlama gruplarına ait kapsayıcı görüntülerinin her biri kendi ad alanlarını kullanır.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Ayrılmış kaynak grubu

Kapsayıcı kayıt defterleri birden çok kapsayıcı ana bilgisayar genelinde kullanılan kaynaklar olduğundan, kayıt defteri kendi kaynak grubunda yer almalıdır.

[Azure Container Instances](../container-instances/container-instances-overview.md)gibi belirli bir ana bilgisayar türüyle denemeler yapabilirsiniz, ancak işiniz bittiğinde kapsayıcı örneğini silmek isteyeceksiniz. Bununla birlikte, Azure Container Registry’ye gönderdiğiniz görüntü koleksiyonunu korumak da isteyebilirsiniz. Kayıt defterinizi kendi kaynak grubuna yerleştirerek, kapsayıcı örneğinin kaynak grubunu silerken yanlışlıkla kayıt defterindeki görüntü koleksiyonunu da silme riskini en aza indirirsiniz.

## <a name="authentication-and-authorization"></a>Kimlik doğrulaması ve yetkilendirme

Bir Azure kapsayıcı kayıt defteri ile kimlik doğrulama için iki ana senaryo vardır: bireysel kimlik doğrulama ve hizmet (veya "gözetimsiz") kimlik doğrulaması. Aşağıdaki tabloda, bu senaryolara kısa bir genel bakış ve her biri için önerilen kimlik doğrulama yöntemi sağlanmıştır.

| Tür | Örnek senaryo | Önerilen metot |
|---|---|---|
| Bireysel kimlik | Geliştirme makinesinden görüntü çeken veya gönderen bir geliştirici. | [az acr login](/cli/azure/acr#az_acr_login) |
| Gözetimsiz kimlik/hizmet kimliği | Kullanıcıyla doğrudan ilgili olmayan derleme ve dağıtım işlem hatları. | [Hizmet sorumlusu](container-registry-authentication.md#service-principal) |

Bu ve diğer Azure Container Registry kimlik doğrulama senaryoları hakkında ayrıntılı bilgi için bkz. [Azure Container Registry Ile kimlik](container-registry-authentication.md)doğrulama.

Azure Container Registry, farklı kimliklere görevler ve ayrıcalıklar dağıtmak için kuruluşunuzdaki güvenlik uygulamalarını destekler. [Rol tabanlı erişim denetimini](container-registry-roles.md)kullanarak farklı kullanıcılara, hizmet sorumlularına veya farklı kayıt defteri işlemleri gerçekleştiren diğer kimliklere uygun izinleri atayın. Örneğin, bir derleme ardışık düzeninde kullanılan bir hizmet sorumlusuna gönderim izinleri atayın ve dağıtım için kullanılan farklı bir kimliğe çekme izinleri atayın. Belirli depolara hassas, zaman sınırlı erişim için [belirteçler](container-registry-repository-scoped-permissions.md) oluşturun.

## <a name="manage-registry-size"></a>Kayıt defteri boyutunu yönetme      

Her [kapsayıcı kayıt defteri hizmet katmanının][container-registry-skus] depolama kısıtlamaları tipik bir senaryoya göre hizalanmaya yöneliktir: başlangıç için **temel** , çoğu üretim uygulaması için **Standart** ve hiper ölçekli performans ve [coğrafi çoğaltma][container-registry-geo-replication]için **Premium** . Kayıt defterinizin kullanım ömrü boyunca kullanılmayan içerikleri düzenli olarak silerek boyutunu yönetmeniz gerekir.

Kayıt defterinizin geçerli boyutunu görüntülemek için [az ACR Show-Usage][az-acr-show-usage] Azure CLI komutunu kullanın:

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

```output
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

Azure portal Kayıt defterinize **genel bakış** bölümünde kullanılan geçerli depolamayı da bulabilirsiniz:

![Azure portalındaki kayıt defteri kullanım bilgileri][registry-overview-quotas]

### <a name="delete-image-data"></a>Görüntü verilerini sil

Azure Container Registry, kapsayıcı Kayıt defterinizden görüntü verilerini silmeye yönelik çeşitli yöntemleri destekler. Resimleri etiketle veya bildirim özetine göre silebilir veya bir depoyu tümüyle silebilirsiniz.

Etiketsiz (bazen "Dangling" veya "yalnız bırakılmış" olarak adlandırılır) görüntü verilerini kayıt defterinizden silme hakkında daha fazla bilgi için, bkz. [Azure Container Registry kapsayıcı görüntülerini silme](container-registry-delete.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure Container Registry, farklı yetenekler sağlayan birkaç katmanda (SKU olarak da bilinir) kullanılabilir. Kullanılabilir hizmet katmanları hakkında daha fazla bilgi için bkz. [Azure Container Registry hizmet katmanları](container-registry-skus.md).

Kapsayıcı kayıt defterlerinin güvenlik duruşunu artırmaya yönelik öneriler için bkz. [Azure Container Registry Için Azure Güvenlik temeli](security-baseline.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-show-usage]: /cli/azure/acr#az_acr_show_usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md