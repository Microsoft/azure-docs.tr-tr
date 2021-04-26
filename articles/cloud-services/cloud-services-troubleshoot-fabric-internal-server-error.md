---
title: Azure 'a bir bulut hizmeti (klasik) dağıtımında Fabricınternalservererror veya ServiceAllocationFailure sorunlarını giderme | Microsoft Docs
description: Bu makalede, Azure 'a bir bulut hizmeti (klasik) dağıtıldığında bir Fabricınternalservererror veya ServiceAllocationFailure özel durumunun nasıl çözümleneceği gösterilmektedir.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 0883178779179df2e531123b8a500c62d42530e4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877453"
---
# <a name="troubleshoot-fabricinternalservererror-or-serviceallocationfailure-when-deploying-a-cloud-service-classic-to-azure"></a>Azure 'a bir bulut hizmeti (klasik) dağıtımında Fabricınternalservererror veya ServiceAllocationFailure sorunlarını giderme

Bu makalede, bir Azure bulut hizmeti (klasik) dağıtımı sırasında yapı denetleyicisinin ayıramadığı ayırma hatalarının sorunlarını giderebileceksiniz.

Bulut Hizmetine örnekleri dağıtırken veya yeni web veya çalışan rolü örnekleri eklerken Microsoft Azure işlem kaynaklarını ayırır.

Azure abonelik sınırına ulaşmadan önce bile bu işlemler sırasında bazen hatalar alabilirsiniz.

> [!TIP]
> Ayrıca, hizmetlerinizin dağıtımını planlarken bilgiler de yararlı olabilir.

## <a name="symptom"></a>Belirti

Azure portal, bulut hizmetinize (klasik) gidin ve kenar çubuğu 'nda günlükleri görüntülemek için *işlem günlüğü (klasik)* seçeneğini belirleyin.

![Görüntü, Işlem günlüğü (klasik) dikey penceresini gösterir.](./media/cloud-services-troubleshoot-fabric-internal-server-error/cloud-services-troubleshoot-allocation-logs.png)

Bulut hizmetinizin (klasik) günlüklerini incelerken aşağıdaki özel durumu görürsünüz:

|Özel durum  |Hata İletisi  |
|---------|---------|
Fabricınternalservererror     |İşlem, ' InternalError ' hata kodu ile başarısız oldu ve "sunucu bir iç hatayla karşılaştı. Lütfen isteği yeniden deneyin. '.|
|ServiceAllocationFailure     |İşlem, ' InternalError ' hata kodu ile başarısız oldu ve "sunucu bir iç hatayla karşılaştı. Lütfen isteği yeniden deneyin. '.|

## <a name="cause"></a>Nedeni

*Fabricınternalservererror* ve *serviceallocationfailure* , yapı denetleyicisi kümedeki örnekleri ayıramadığında oluşabilecek özel durumlardır. Kök nedeni, bulut hizmetinin **sabitlendiği** veya **sabitlenmemiş** olmasına göre değişir.

- [**Sabitlenmiş değil:** Yeni bir bulut hizmetinin ilk dağıtımından oluşan başarısızlık](#not-pinned-to-a-cluster)
- [**Sabitlenmiş:** Mevcut bulut hizmetlerinden alınan sorunlar](#pinned-to-a-cluster)

> [!NOTE]
> İlk örnek bir bulut hizmetine dağıtıldığında (herhangi bir hazırlama veya üretimde), bu bulut hizmeti bir kümeye sabitlenir.
>
> Zaman içinde, bu kaynak havuzundaki kaynaklar tam olarak kullanılabilir hale gelebilir. Bir bulut hizmeti, sabitlenmiş kaynak havuzunda yeterli kaynak olmadığında ek kaynaklar için bir ayırma isteği yapıyorsa, istek bir [ayırma hatasına](cloud-services-allocation-failures.md)neden olur.

## <a name="solution"></a>Çözüm

Aşağıdaki senaryolarda ayırma hatalarıyla ilgili yönergeleri izleyin.

### <a name="not-pinned-to-a-cluster"></a>Bir kümeye sabitlenmedi

Bir bulut hizmeti (klasik) ilk kez dağıttığınızda, küme henüz seçilmemiştir, bu nedenle bulut hizmeti *sabitlenmez*. Azure 'un bir dağıtım hatası olabilir, nedeni:

- Bölgede kullanılamayan belirli bir boyutu seçtiniz.
- Farklı roller genelinde gereken boyutların birleşimi bölgede kullanılamaz.

Bu senaryoda bir ayırma hatası yaşarsanız, önerilen eylem, bölgedeki kullanılabilir boyutları denet, daha önce belirttiğiniz boyutu değiştirecek.

1. [Bulut hizmeti (klasik) ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services) sayfasındaki bir bölgede kullanılabilir olan boyutları kontrol edebilirsiniz.

    > [!NOTE]
    > *Ürünler* sayfası kullanılabilir kapasiteyi göstermez. Yeni bir ayırma için, Azure 'un bölgede en uygun kümeyi bu noktada seçebilmelidir.

1. Bölgenizde farklı bir [Ürün boyutu](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) belirtmek için bulut hizmetiniz (klasik) hizmet tanım dosyasını güncelleştirin.

### <a name="pinned-to-a-cluster"></a>Bir kümeye sabitlenmiş

Mevcut bulut hizmetleri bir kümeye *sabitlenmiştir* . Bulut hizmeti (klasik) için başka dağıtımlar da aynı kümede olur.

Bu senaryoda bir ayırma hatası yaşarsanız, önerilen eylem kursu yeni bir bulut hizmetine (klasik) yeniden dağıtmanız (ve *CNAME*'i güncelleştirmeniz).

> [!TIP]
> Platformun o bölgedeki tüm kümelerden seçmesine olanak tanıdığı için büyük ihtimalle en başarılı çözüm bu olacaktır.

> [!NOTE]
> Bu çözümün sıfır kapalı kalma süresi olması gerekir.

1. İş yükünü yeni bir bulut hizmetine (klasik) dağıtın.
    - Daha fazla yönerge için bkz. [bulut hizmeti oluşturma ve dağıtma (klasik)](cloud-services-how-to-create-deploy-portal.md) Kılavuzu.

    > [!WARNING]
    > Bu dağıtım yuvasıyla ilişkili IP adresini kaybetmek istemiyorsanız, çözüm 3 ' ü kullanabilirsiniz [-IP adresini saklayın](cloud-services-allocation-failures.md#solutions).

1. *CNAME* veya *bir* kaydı, trafiği yeni bulut hizmetine (klasik) işaret etmek üzere güncelleştirin.
    - Daha fazla yönerge için bkz. [Azure bulut hizmeti için özel etki alanı adı yapılandırma (klasik)](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) Kılavuzu.

1. Sıfır trafik eski siteye gittikten sonra eski bulut hizmetini (klasik) silebilirsiniz.
    - Daha fazla yönerge için [dağıtımları silme ve bulut hizmeti (klasik)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) kılavuzuna bakın.
    - Bulut hizmetinizdeki (klasik) ağ trafiğini görmek için bkz. [bulut hizmeti 'Ne giriş (klasik) izleme](cloud-services-how-to-monitor.md).

Bkz. [bulut hizmeti (klasik) ayırma hatalarında sorun giderme | ](cloud-services-allocation-failures.md#common-issues) Daha fazla düzeltme adımları için Microsoft docs.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla ayırma hatası çözümü ve arka plan bilgileri için:

> [!div class="nextstepaction"]
> [Ayırma arızaları-bulut hizmeti (klasik)](cloud-services-allocation-failures.md)

Azure sorununuz bu makalede giderilmemişse [MSDN ve Stack Overflow](https://azure.microsoft.com/support/forums/)Azure forumlarını ziyaret edin. Sorununuzu bu forumlara gönderebilir veya [ @AzureSupport Twitter 'da](https://twitter.com/AzureSupport)ilan edebilirsiniz. Ayrıca, bir Azure destek isteği gönderebilirsiniz. Destek isteği göndermek için [Azure desteği](https://azure.microsoft.com/support/options/) sayfasında *Destek Al*' ı seçin.
