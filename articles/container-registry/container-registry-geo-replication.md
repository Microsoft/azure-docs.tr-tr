---
title: Kayıt defterini coğrafi olarak çoğaltma
description: Kayıt defterinin çoklu ana bölge çoğaltmalarıyla birden çok bölgeye erişmesini sağlayan coğrafi olarak çoğaltılan bir Azure Container Registry oluşturmaya ve yönetmeye başlayın. Coğrafi çoğaltma, Premium hizmet katmanının bir özelliğidir.
author: stevelas
ms.topic: article
ms.date: 07/21/2020
ms.author: stevelas
ms.openlocfilehash: 3e5b064ec37b855186f633677e2b1a3f615a6736
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783872"
---
# <a name="geo-replication-in-azure-container-registry"></a>Azure Container Registry coğrafi çoğaltma

Yerel bir varlık ya da etkin bir yedekleme isteyen şirketler, birden çok Azure bölgesinden Hizmetleri çalıştırmayı seçin. En iyi yöntem olarak görüntülerin çalıştığı her bölgeye bir kapsayıcı kayıt defteri yerleştirmek, hızlı ve güvenilir görüntü katmanı aktarımları sağlayan ağa yakın işlemlere olanak tanır. Azure kapsayıcı kayıt defterinin, çoklu ana kaynağa sahip bölgesel kayıt defterleri ile birden çok bölgeyi sunan tek bir kayıt defteri olarak işlev görmesini sağlar. 

Coğrafi olarak çoğaltılmış bir kayıt defteri aşağıdaki avantajları sağlar:

* Tek kayıt defteri, resim ve etiket adları birden çok bölgede kullanılabilir
* Ağ, kayıt defteri erişimini kapatma ile bölgesel dağıtımların performansını ve güvenilirliğini geliştirme
* Kapsayıcı konağınız ile aynı veya yakın bölgede yerel, çoğaltılan bir kayıt defterinden görüntü katmanları çekerek veri aktarımı maliyetlerini azaltın
* Birden çok bölgede kayıt defterinin tek noktadan yönetimi
* Bölgesel bir kesinti oluşursa kayıt defteri esnekliği

> [!NOTE]
> Birden fazla Azure Container Registry 'de kapsayıcı görüntülerinin kopyalarını korumanız gerekiyorsa Azure Container Registry de [görüntü içeri aktarmayı](container-registry-import-images.md)destekler. Örneğin, bir DevOps iş akışında, Docker komutlarını kullanmaya gerek kalmadan bir geliştirme kayıt defterindeki bir görüntüyü üretim kayıt defterine aktarabilirsiniz.
>

## <a name="example-use-case"></a>Örnek kullanım örneği
Contoso, ABD, Kanada ve Avrupa 'Da bulunan bir genel varlık Web sitesi çalıştırır. Contoso, yerel ve ağ-kapatma içeriğiyle bu pazarlara hizmet vermek için Batı ABD, Doğu ABD, Kanada Orta ve Batı Avrupa [Azure Kubernetes hizmeti](../aks/index.yml) (aks) kümelerini çalıştırır. Docker görüntüsü olarak dağıtılan Web sitesi uygulaması, tüm bölgelerde aynı kod ve görüntüyü kullanır. Bu bölgeye yerel olan içerik, her bölgede benzersiz olarak sağlanan bir veritabanından alınır. Her bölgesel dağıtımın, yerel veritabanı gibi kaynaklar için benzersiz yapılandırması vardır.

Geliştirme ekibi, Batı ABD veri merkezini kullanarak Seattle WA 'da bulunur.

![Birden çok kayıt defterlerine iletme](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Birden çok kayıt defterlerine iletme*

Contoso, coğrafi çoğaltma özelliklerini kullanmadan önce Batı Avrupa ek bir kayıt defteriyle Batı ABD içinde ABD tabanlı kayıt defterine sahipti. Bu farklı bölgelere hizmeti sağlamak için, geliştirme ekibi görüntüleri iki farklı kayıt defterine gönderdi.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Birden çok kayıt defterlerinden çekme](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Birden çok kayıt defterlerinden çekme*

Birden çok kayıt defterlerinin tipik sorunları şunlardır:

* Doğu ABD, Batı ABD ve Kanada Orta kümeleri, bu uzak kapsayıcının her biri için çıkış ücretlerini, Batı ABD veri merkezlerinden çekme görüntüleri barındırarak, tüm Batı ABD kayıt defterinden çeker.
* Geliştirme ekibinin görüntüleri Batı ABD ve Batı Avrupa kayıt defterlerine itmesi gerekir.
* Geliştirme ekibinin her bölgesel dağıtımı, yerel kayıt defterine başvuran görüntü adlarıyla yapılandırması ve koruması gerekir.
* Her bölge için kayıt defteri erişiminin yapılandırılması gerekir.

## <a name="benefits-of-geo-replication"></a>Coğrafi çoğaltmanın avantajları

![Coğrafi olarak çoğaltılan bir kayıt defterinden çekme](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Azure Container Registry coğrafi çoğaltma özelliğini kullanarak bu avantajlar gerçekleştirilir:

* Tüm bölgelerde tek bir kayıt defterini yönetme: `contoso.azurecr.io`
* Tüm bölgeler aynı görüntü URL 'sini kullandığı için tek bir görüntü dağıtımı yapılandırmasını yönetin: `contoso.azurecr.io/public/products/web:1.2`
* Tek bir kayıt defterine göndererek ACR, Coğrafi çoğaltmayı yönetir. ACR yalnızca benzersiz katmanları çoğaltır, bölgeler arasında veri aktarımını azaltır. 
* Belirli çoğaltmalarda olayları bilgilendirmek için bölgesel [Web kancalarını](container-registry-webhook.md) yapılandırın.
* Bölgesel kesintiler için dayanıklı olan yüksek oranda kullanılabilir bir kayıt defteri sağlayın.

Azure Container Registry Ayrıca, Azure bölgesinde dayanıklı ve yüksek oranda kullanılabilir bir Azure Container Registry oluşturmak için [kullanılabilirlik bölgelerini](zone-redundancy.md) destekler. Bölge içindeki artıklık için kullanılabilirlik bölgelerinin birleşimi ve birden çok bölgede coğrafi çoğaltma, bir kayıt defterinin güvenilirliğini ve performansını geliştirir.

## <a name="configure-geo-replication"></a>Coğrafi çoğaltmayı yapılandırma

Coğrafi çoğaltmanın yapılandırılması, bir haritadaki bölgelere tıklanması kadar kolaydır. Azure CLı 'de [az ACR çoğaltma](/cli/azure/acr/replication) komutları dahil olmak üzere Coğrafi çoğaltmayı yönetebilir veya bir [Azure Resource Manager şablonuyla](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication)coğrafi çoğaltma için etkinleştirilmiş bir kayıt defteri dağıtabilirsiniz.

Coğrafi çoğaltma, [Premium kayıt defterlerinin](container-registry-skus.md)bir özelliğidir. Kayıt defteriniz henüz Premium değilse, [Azure Portal](https://portal.azure.com)temel ve standart 'den Premium 'a geçiş yapabilirsiniz:

![Azure portal hizmet katmanlarını değiştirme](media/container-registry-skus/update-registry-sku.png)

Premium kayıt defteriniz için Coğrafi çoğaltmayı yapılandırmak üzere, konumundaki Azure portal oturum açın https://portal.azure.com .

Azure Container Registry gidin ve **çoğaltmalar**' ı seçin:

![Azure portalı kapsayıcı kayıt defteri kullanıcı arabirimindeki Çoğaltmalar](media/container-registry-geo-replication/registry-services.png)

Geçerli tüm Azure bölgelerini gösteren bir harita görüntülenir:

 ![Azure portalındaki bölge haritası](media/container-registry-geo-replication/registry-geo-map.png)

* Mavi altıgenler geçerli çoğaltmaları temsil eder
* Yeşil altıgenler olası çoğaltma bölgelerini temsil eder
* Gri altıgenler, henüz çoğaltma için kullanılamayan Azure bölgelerini temsil eder

Bir çoğaltma yapılandırmak için yeşil bir altıon seçin, sonra **Oluştur**' u seçin.

 ![Azure portalında çoğaltma oluşturmaya yönelik kullanıcı arabirimi](media/container-registry-geo-replication/create-replication.png)

Ek çoğaltmalar yapılandırmak için, diğer bölgelerin yeşil altılarını seçin ve ardından **Oluştur**' a tıklayın.

ACR, yapılandırılmış çoğaltmalar genelinde görüntüleri eşitlemeye başlar. Tamamlandıktan sonra Portal, *Ready* olarak yansıtır. Portaldaki çoğaltma durumu otomatik olarak güncelleştirmez. Güncelleştirilmiş durumu görmek için Yenile düğmesini kullanın.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Coğrafi olarak çoğaltılan kayıt defteri kullanma konuları

* Coğrafi olarak çoğaltılan bir kayıt defterindeki her bölge, ayarlandıktan sonra bağımsızdır. Azure Container Registry SLA 'Lar, coğrafi olarak çoğaltılan her bölge için geçerlidir.
* Coğrafi olarak çoğaltılan bir kayıt defterinden görüntü gönderdiğinizde veya çektiğinizde, arka planda Azure Traffic Manager, isteği ağ gecikmesi açısından en yakın bölgede bulunan kayıt defterine gönderir.
* En yakın bölgeye bir görüntü veya etiket güncelleştirmesi gönderdikten sonra, Azure Container Registry bildirimlerin ve katmanların seçtiğiniz kalan bölgelere çoğaltılması biraz zaman alır. Daha büyük resimler daha küçük olanlara çoğaltılmak için daha uzun sürer. Görüntüler ve Etiketler, son tutarlılık modeliyle çoğaltma bölgeleri arasında eşitlenir.
* Coğrafi olarak çoğaltılan bir kayıt defterine gönderim güncelleştirmelerine bağlı olan iş akışlarını yönetmek için, [Web kancalarını](container-registry-webhook.md) anında iletme olaylarına yanıt verecek şekilde yapılandırmanızı öneririz. Coğrafi olarak çoğaltılan bölgelerde gerçekleştirilen anında iletme olaylarını izlemek için coğrafi olarak çoğaltılan bir kayıt defteri içinde bölgesel Web kancaları oluşturabilirsiniz.
* İçerik katmanlarını temsil eden bloblara hizmeti sağlamak için Azure Container Registry veri uç noktalarını kullanır. Kayıt defterinizin coğrafi olarak çoğaltılan bölgelerindeki her birinde kayıt defteriniz için [adanmış veri uç noktalarını](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) etkinleştirebilirsiniz. Bu uç noktalar sıkı kapsamlı güvenlik duvarı erişim kuralları yapılandırmasına izin verir. Sorun giderme amacıyla, çoğaltılan verileri koruyarak [bir çoğaltma için isteğe bağlı yönlendirmeyi devre dışı](#temporarily-disable-routing-to-replication) bırakabilirsiniz.
* Bir sanal ağdaki özel uç noktaları kullanarak kayıt defteriniz için [özel bir bağlantı](container-registry-private-link.md) yapılandırırsanız, coğrafi olarak çoğaltılan bölgelerin her birinde ayrılmış veri uç noktaları varsayılan olarak etkinleştirilir. 

## <a name="delete-a-replica"></a>Bir çoğaltmayı sil

Kayıt defteriniz için bir çoğaltma yapılandırdıktan sonra, artık gerekmiyorsa, istediğiniz zaman silebilirsiniz. Azure CLı 'deki [az ACR çoğaltma Delete](/cli/azure/acr/replication#az_acr_replication_delete) komutu gibi Azure Portal veya diğer araçları kullanarak bir çoğaltmayı silin.

Azure portal bir çoğaltmayı silmek için:

1. Azure Container Registry gidin ve **çoğaltmalar**' ı seçin.
1. Bir çoğaltmanın adını seçin ve **Sil**' i seçin. Çoğaltmayı silmek istediğinizi onaylayın.

Azure CLı kullanarak Doğu ABD bölgesindeki bir *myregistry* çoğaltmasını silmek için:

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>Coğrafi çoğaltma fiyatlandırması

Coğrafi çoğaltma, Azure Container Registry [Premium hizmet katmanının](container-registry-skus.md) bir özelliğidir. İstediğiniz bölgelere bir kayıt defteri çoğalttığınızda, her bölge için Premium kayıt defteri ücretlerine tabi olursunuz.

Yukarıdaki örnekte, contoso iki kayıt, Doğu ABD, Kanada Orta ve Batı Avrupa çoğaltmaları ekleyerek bir yukarı Birleşik olarak birleştirilmiş. Contoso, ek yapılandırma veya yönetim olmadan ayda dört kat Premium ödemenizi ister. Her bölge artık görüntülerini yerel olarak çeker, performansı, Batı ABD ağ çıkış ücretleri olmadan Kanada ve Doğu ABD artırır.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Coğrafi olarak çoğaltılan kayıt defterlerine gönderim işlemleri sorunlarını giderme
 
Bir görüntüyü coğrafi olarak çoğaltılan bir kayıt defterine ileten bir Docker istemcisi, tüm görüntü katmanlarını ve bildirimini tek bir çoğaltılan bölgeye gönderemeyebilir. Azure Traffic Manager kayıt defteri isteklerini ağa en yakın çoğaltılan kayıt defterine yönlendirtiğinden bu durum oluşabilir. Kayıt defterinde *yakın* iki çoğaltma bölgesi varsa, görüntü katmanları ve bildirim iki siteye dağıtılabilir ve bildirim doğrulandığında gönderme işlemi başarısız olur. Bu sorun, bazı Linux konaklarındaki kayıt defteri DNS adının çözümlenme yöntemi nedeniyle oluşur. Bu sorun, istemci tarafı DNS önbelleği sağlayan Windows üzerinde oluşmaz.
 
Bu sorun oluşursa, bir çözüm, Linux ana bilgisayarına gibi bir istemci tarafı DNS önbelleğinin uygulanmasından biridir `dnsmasq` . Bu, kayıt defteri adının tutarlı bir şekilde çözümlendiğinden emin olmanıza yardımcı olur. Azure 'da bir kayıt defterine göndermek için bir Linux VM kullanıyorsanız, bkz. [Azure 'Da Linux sanal makineleri Için DNS ad çözümleme seçenekleri](../virtual-machines/linux/azure-dns.md)seçenekleri.

Görüntüleri gönderirken en yakın çoğaltma ile DNS çözümlemesini iyileştirmek için, çekme işlemlerinin kaynağıyla aynı Azure bölgelerinde coğrafi olarak çoğaltılan bir kayıt defteri veya Azure dışında çalışırken en yakın bölgeyi yapılandırın.

### <a name="temporarily-disable-routing-to-replication"></a>Çoğaltmaya yönlendirmeyi geçici olarak devre dışı bırak

Coğrafi olarak çoğaltılan bir kayıt defteriyle ilgili sorunları gidermek için, bir veya daha fazla çoğaltma için Traffic Manager yönlendirmeyi geçici olarak devre dışı bırakmak isteyebilirsiniz. Azure CLı sürüm 2,8 ' den başlayarak, `--region-endpoint-enabled` çoğaltılan bölge oluştururken veya güncelleştirdiğinizde bir seçenek (Önizleme) yapılandırabilirsiniz. ' A bir çoğaltma seçeneğini belirlediğinizde `--region-endpoint-enabled` `false` , Traffic Manager artık Docker Push veya çekme isteklerini bu bölgeye yönlendirmez. Varsayılan olarak, tüm çoğaltmalar için yönlendirme etkindir ve tüm çoğaltmalar genelinde veri eşitlemesi yönlendirmenin etkin veya devre dışı olup olmadığını meydana getirebilir.

Mevcut bir çoğaltmaya yönlendirmeyi devre dışı bırakmak için öncelikle kayıt defterindeki çoğaltmaları listelemek üzere [az ACR çoğaltma listesini][az-acr-replication-list] çalıştırın. Ardından, [az ACR çoğaltma güncelleştirmesini][az-acr-replication-update] çalıştırın ve `--region-endpoint-enabled false` belirli bir çoğaltma için ayarlayın. Örneğin, *myregistry* içinde *westus* çoğaltması için ayarı yapılandırmak için:

```azurecli
# Show names of existing replications
az acr replication list --registry --output table

# Disable routing to replication
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled false
```

Yönlendirmeyi bir çoğaltmaya geri yüklemek için:

```azurecli
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled true
```

## <a name="next-steps"></a>Sonraki adımlar

[Azure Container Registry coğrafi çoğaltma](container-registry-tutorial-prepare-registry.md)olmak üzere üç parçalı öğretici serisine göz atın. Coğrafi olarak çoğaltılan bir kayıt defteri oluşturma, kapsayıcı oluşturma ve daha sonra `docker push` kapsayıcı örnekleri için birden çok bölgesel Web Apps tek bir komutla dağıtma adımları.

> [!div class="nextstepaction"]
> [Azure Container Registry coğrafi çoğaltma](container-registry-tutorial-prepare-registry.md)

[az-acr-replication-list]: /cli/azure/acr/replication#az_acr_replication_list
[az-acr-replication-update]: /cli/azure/acr/replication#az_acr_replication_update
