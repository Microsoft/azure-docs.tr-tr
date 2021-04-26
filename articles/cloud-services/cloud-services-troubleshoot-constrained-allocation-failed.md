---
title: Azure 'a bir bulut hizmeti (klasik) dağıtımı sırasında ConstrainedAllocationFailed sorunlarını giderme | Microsoft Docs
description: Bu makalede, Azure 'a bir bulut hizmeti (klasik) dağıtımında bir ConstrainedAllocationFailed özel durumunun nasıl çözümleneceği gösterilmektedir.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 4a92246f81ddd10840bb0dcf7edf2b01853fd148
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876607"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Azure 'a bir bulut hizmeti (klasik) dağıtımı sırasında ConstrainedAllocationFailed sorunlarını giderme

Bu makalede, Azure bulut hizmetleri 'nin (klasik) ayırma kısıtlamaları nedeniyle dağıtabeceği ayırma hatalarıyla ilgili sorunları giderebilirsiniz.

Bir bulut hizmetine (klasik) örnekler dağıtırken veya yeni Web veya çalışan rolü örnekleri eklediğinizde Microsoft Azure işlem kaynaklarını ayırır.

Azure abonelik sınırına ulaşmadan önce bile bu işlemler sırasında bazen hatalar alabilirsiniz.

> [!TIP]
> Ayrıca, hizmetlerinizin dağıtımını planlarken bilgiler de yararlı olabilir.

## <a name="symptom"></a>Belirti

Azure portal, bulut hizmetinize (klasik) gidin ve kenar çubuğu 'nda günlükleri görüntülemek için *işlem günlüğü (klasik)* seçeneğini belirleyin.

![Görüntü, Işlem günlüğü (klasik) dikey penceresini gösterir.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

Bulut hizmetinizin (klasik) günlüklerini incelerken aşağıdaki özel durumu görürsünüz:

|Özel durum türü  |Hata İletisi  |
|---------|---------|
|ConstrainedAllocationFailed     |' ' Azure işlemi `{Operation ID}` , kod işlem. ConstrainedAllocationFailed ile başarısız oldu. Ayrıntılar: ayırma başarısız oldu; istekteki kısıtlamalar karşılayamadı. İstenen yeni hizmet dağıtımı bir Benzeşim Grubuna bağlı veya bir Sanal Ağı hedefliyor ya da bu barındırılan hizmetin altında mevcut bir dağıtım var. Bu koşullardan herhangi biri, yeni dağıtımı belirli Azure kaynaklarına kısıtlar. Daha sonra yeniden deneyin veya VM boyutunu veya rol örneği sayısını azaltmayı deneyin. Alternatif olarak, mümkünse, yukarıda belirtilen kısıtlamaları kaldırın veya farklı bir bölgeye dağıtım yapmayı deneyin.|

## <a name="cause"></a>Nedeni

İlk örnek bir bulut hizmetine dağıtıldığında (herhangi bir hazırlama veya üretimde), bu bulut hizmeti bir kümeye sabitlenir.

Zaman içinde, bu kümedeki kaynaklar tam olarak kullanılabilir hale gelebilir. Bir bulut hizmeti (klasik), sabitlenmiş kümede yeterli kaynak olmadığında daha fazla kaynak için bir ayırma isteği yapıyorsa, istek bir ayırma hatasına neden olur. Daha fazla bilgi için bkz. [ayırma hatası yaygın sorunları](cloud-services-allocation-failures.md#common-issues).

## <a name="solution"></a>Çözüm

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