---
title: Azure Container Registry coğrafi çoğaltma
description: Kayıt defterinin çoklu ana bölge çoğaltmalarıyla birden çok bölgeye erişmesini sağlayan coğrafi olarak çoğaltılan bir Azure Container Registry oluşturmaya ve yönetmeye başlayın.
services: container-registry
author: stevelas
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 08/16/2019
ms.author: stevelas
ms.openlocfilehash: cddd55d3dfc2609b7a32a276e106e152f0868b32
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931646"
---
# <a name="geo-replication-in-azure-container-registry"></a>Azure Container Registry’de coğrafi çoğaltma

Yerel bir varlık ya da etkin bir yedekleme isteyen şirketler, birden çok Azure bölgesinden Hizmetleri çalıştırmayı seçin. En iyi uygulama olarak, resimlerin çalıştırıldığı her bölgeye bir kapsayıcı kayıt defteri yerleştirmek, ağ kapatma işlemlerine izin vererek, hızlı ve güvenilir görüntü katmanı aktarımlarını etkinleştirir. Coğrafi çoğaltma, Azure Container Registry 'nin tek bir kayıt defteri olarak çalışmasını sağlar ve çoklu ana bölge kayıt defterlerine sahip birden çok bölgeye hizmet verebilir. 

Coğrafi olarak çoğaltılan bir kayıt defteri aşağıdaki avantajları sağlar:

* Tek kayıt defteri/görüntü/etiket adları birden çok bölgede kullanılabilir
* Ağ-bölgesel dağıtımlardan kayıt defteri erişimini kapat
* Başka çıkış ücretleri yoktur, çünkü görüntü, kapsayıcı konağınız ile aynı bölgedeki yerel, çoğaltılan bir kayıt defterinden alınır
* Birden çok bölgede bir kayıt defterinin tek yönetimi

> [!NOTE]
> Birden fazla Azure Container Registry 'de kapsayıcı görüntülerinin kopyalarını korumanız gerekiyorsa Azure Container Registry de [görüntü içeri aktarmayı](container-registry-import-images.md)destekler. Örneğin, bir DevOps iş akışında, Docker komutlarını kullanmaya gerek kalmadan bir geliştirme kayıt defterindeki bir görüntüyü üretim kayıt defterine aktarabilirsiniz.
>

## <a name="example-use-case"></a>Örnek kullanım örneği
Contoso, ABD, Kanada ve Avrupa 'Da bulunan bir genel varlık Web sitesi çalıştırır. Contoso, yerel ve ağ-kapatma içeriğiyle bu pazarlara hizmet vermek için Batı ABD, Doğu ABD, Kanada Orta ve Batı Avrupa [Azure Kubernetes hizmeti](/azure/aks/) (aks) kümelerini çalıştırır. Docker görüntüsü olarak dağıtılan Web sitesi uygulaması, tüm bölgelerde aynı kod ve görüntüyü kullanır. Bu bölgeye yerel olan içerik, her bölgede benzersiz olarak sağlanan bir veritabanından alınır. Her bölgesel dağıtımın, yerel veritabanı gibi kaynaklar için benzersiz yapılandırması vardır.

Geliştirme ekibi, Batı ABD veri merkezini kullanarak Seattle WA 'da bulunur.

birden çok kayıt defterlerine ![](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Birden çok kayıt defterlerine iletme*

Contoso, coğrafi çoğaltma özelliklerini kullanmadan önce Batı Avrupa ek bir kayıt defteriyle Batı ABD içinde ABD tabanlı kayıt defterine sahipti. Bu farklı bölgelere hizmeti sağlamak için, geliştirme ekibi görüntüleri iki farklı kayıt defterine gönderdi.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![birden çok kayıt defterlerinden çekiliyor](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Birden çok kayıt defterlerinden çekme*

Birden çok kayıt defterlerinin tipik sorunları şunlardır:

* Doğu ABD, Batı ABD ve Kanada Orta kümeleri, bu uzak kapsayıcının her biri için çıkış ücretlerini, Batı ABD veri merkezlerinden çekme görüntüleri barındırarak, tüm Batı ABD kayıt defterinden çeker.
* Geliştirme ekibinin görüntüleri Batı ABD ve Batı Avrupa kayıt defterlerine itmesi gerekir.
* Geliştirme ekibinin her bölgesel dağıtımı, yerel kayıt defterine başvuran görüntü adlarıyla yapılandırması ve koruması gerekir.
* Her bölge için kayıt defteri erişiminin yapılandırılması gerekir.

## <a name="benefits-of-geo-replication"></a>Coğrafi çoğaltmanın avantajları

![Coğrafi olarak çoğaltılan bir kayıt defterinden çekme](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Azure Container Registry coğrafi çoğaltma özelliğini kullanarak bu avantajlar gerçekleştirilir:

* Tüm bölgelerde tek bir kayıt defterini yönetme: `contoso.azurecr.io`
* Tüm bölgeler aynı görüntü URL 'sini kullandığı için tek bir görüntü dağıtımı yapılandırmasını yönetme: `contoso.azurecr.io/public/products/web:1.2`
* Tek bir kayıt defterine göndererek ACR, Coğrafi çoğaltmayı yönetir. Bölgesel [Web kancalarını](container-registry-webhook.md) belirli çoğaltmalarda olayları bilgilendirmek üzere yapılandırabilirsiniz.

## <a name="configure-geo-replication"></a>Coğrafi çoğaltmayı yapılandırma

Coğrafi çoğaltmanın yapılandırılması, bir haritadaki bölgelere tıklanması kadar kolaydır. Azure CLı 'de [az ACR çoğaltma](/cli/azure/acr/replication) komutları dahil olmak üzere Coğrafi çoğaltmayı yönetebilir veya bir [Azure Resource Manager şablonuyla](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication)coğrafi çoğaltma için etkinleştirilmiş bir kayıt defteri dağıtabilirsiniz.

Coğrafi çoğaltma yalnızca [Premium kayıt defterlerinin](container-registry-skus.md) bir özelliğidir. Kayıt defteriniz henüz Premium değilse, [Azure Portal](https://portal.azure.com)temel ve standart 'den Premium 'a geçiş yapabilirsiniz:

![Azure portal SKU 'Ları değiştirme](media/container-registry-skus/update-registry-sku.png)

Premium kayıt defteriniz için Coğrafi çoğaltmayı yapılandırmak üzere https://portal.azure.comAzure portal oturum açın.

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

ACR, yapılandırılmış çoğaltmalar genelinde görüntüleri eşitlemeye başlar. Tamamlandıktan sonra Portal, *Ready*olarak yansıtır. Portaldaki çoğaltma durumu otomatik olarak güncelleştirmez. Güncelleştirilmiş durumu görmek için Yenile düğmesini kullanın.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Coğrafi olarak çoğaltılan kayıt defteri kullanma konuları

* Coğrafi olarak çoğaltılan bir kayıt defterindeki her bölge, ayarlandıktan sonra bağımsızdır. Azure Container Registry SLA 'Lar, coğrafi olarak çoğaltılan her bölge için geçerlidir.
* Coğrafi olarak çoğaltılan bir kayıt defterinden görüntü gönderdiğinizde veya çektiğinizde, arka planda Azure Traffic Manager, isteği size en yakın bölgede bulunan kayıt defterine gönderir.
* En yakın bölgeye bir görüntü veya etiket güncelleştirmesi gönderdikten sonra, Azure Container Registry bildirimlerin ve katmanların seçtiğiniz kalan bölgelere çoğaltılması biraz zaman alır. Daha büyük resimler daha küçük olanlara çoğaltılmak için daha uzun sürer. Görüntüler ve Etiketler, son tutarlılık modeliyle çoğaltma bölgeleri arasında eşitlenir.
* Coğrafi olarak çoğaltılan bir çekme güncelleştirmelerine bağlı olan iş akışlarını yönetmek için, [Web kancalarını](container-registry-webhook.md) anında iletme olaylarına yanıt verecek şekilde yapılandırmanızı öneririz. Coğrafi olarak çoğaltılan bölgelerde gerçekleştirilen anında iletme olaylarını izlemek için coğrafi olarak çoğaltılan bir kayıt defteri içinde bölgesel Web kancaları oluşturabilirsiniz.

## <a name="delete-a-replica"></a>Bir çoğaltmayı silme

Kayıt defteriniz için bir çoğaltma yapılandırdıktan sonra, artık gerekmiyorsa, istediğiniz zaman silebilirsiniz. Azure CLı 'deki [az ACR çoğaltma Delete](/cli/azure/acr/replication#az-acr-replication-delete) komutu gibi Azure Portal veya diğer araçları kullanarak bir çoğaltmayı silin.

Azure portal bir çoğaltmayı silmek için:

1. Azure Container Registry gidin ve **çoğaltmalar**' ı seçin.
1. Bir çoğaltmanın adını seçin ve **Sil**' i seçin. Çoğaltmayı silmek istediğinizi onaylayın.

> [!NOTE]
> Kayıt defterinin *giriş bölgesindeki* kayıt defteri çoğaltmasını silemezsiniz, yani kayıt defterini oluşturduğunuz konum. Yalnızca kayıt defterinin kendisini silerek giriş çoğaltmasını silebilirsiniz.

## <a name="geo-replication-pricing"></a>Coğrafi çoğaltma fiyatlandırması

Coğrafi çoğaltma, Azure Container Registry [PREMIUM SKU](container-registry-skus.md) 'sunun bir özelliğidir. İstediğiniz bölgelere bir kayıt defteri çoğalttığınızda, her bölge için Premium kayıt defteri ücretlerine tabi olursunuz.

Yukarıdaki örnekte, contoso iki kayıt, Doğu ABD, Kanada Orta ve Batı Avrupa çoğaltmaları ekleyerek bir yukarı Birleşik olarak birleştirilmiş. Contoso, ek yapılandırma veya yönetim olmadan ayda dört kat Premium ödemenizi ister. Her bölge artık görüntülerini yerel olarak çeker, performansı, Batı ABD ağ çıkış ücretleri olmadan Kanada ve Doğu ABD artırır.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Coğrafi olarak çoğaltılan kayıt defterlerine gönderim işlemleri sorunlarını giderme
 
Bir görüntüyü coğrafi olarak çoğaltılan bir kayıt defterine ileten bir Docker istemcisi, tüm görüntü katmanlarını ve bildirimini tek bir çoğaltılan bölgeye gönderemeyebilir. Azure Traffic Manager kayıt defteri isteklerini ağa en yakın çoğaltılan kayıt defterine yönlendirtiğinden bu durum oluşabilir. Kayıt defterinde *yakın* iki çoğaltma bölgesi varsa, görüntü katmanları ve bildirim iki siteye dağıtılabilir ve bildirim doğrulandığında gönderme işlemi başarısız olur. Bu sorun, bazı Linux konaklarındaki kayıt defteri DNS adının çözümlenme yöntemi nedeniyle oluşur. Bu sorun, istemci tarafı DNS önbelleği sağlayan Windows üzerinde oluşmaz.
 
Bu sorun oluşursa, bir çözüm, Linux ana bilgisayarında `dnsmasq` gibi bir istemci tarafı DNS önbelleğinin uygulanmasından biridir. Bu, kayıt defteri adının tutarlı bir şekilde çözümlendiğinden emin olmanıza yardımcı olur. Azure 'da bir kayıt defterine göndermek için bir Linux VM kullanıyorsanız, bkz. [Azure 'Da Linux sanal makineleri Için DNS ad çözümleme seçenekleri](../virtual-machines/linux/azure-dns.md)seçenekleri.

Görüntüleri gönderirken en yakın çoğaltma ile DNS çözümlemesini iyileştirmek için, çekme işlemlerinin kaynağıyla aynı Azure bölgelerinde coğrafi olarak çoğaltılan bir kayıt defteri veya Azure dışında çalışırken en yakın bölgeyi yapılandırın.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Container Registry coğrafi çoğaltma](container-registry-tutorial-prepare-registry.md)olmak üzere üç parçalı öğretici serisine göz atın. Coğrafi olarak çoğaltılan bir kayıt defteri oluşturma, kapsayıcı oluşturma ve ardından kapsayıcı örnekleri için birden çok bölgesel Web Apps ile tek bir `docker push` komutuyla dağıtma adımları.

> [!div class="nextstepaction"]
> [Azure Container Registry coğrafi çoğaltma](container-registry-tutorial-prepare-registry.md)
