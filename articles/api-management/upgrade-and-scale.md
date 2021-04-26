---
title: Azure API Management örneğini yükseltme ve ölçeklendirme | Microsoft Docs
description: Bu konu, bir Azure API Management örneğinin nasıl yükseltileceğini ve ölçeklendirebileceğinizi açıklamaktadır.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: bd36434bbfe435a53567c46728610627f99f987f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98127796"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Azure API Management örneğini yükseltme ve ölçeklendirme  

Müşteriler birimleri ekleyerek ve kaldırarak bir Azure API Management örneğini ölçeklendirebilirler. Bir **birim** adanmış Azure kaynaklarından oluşur ve ayda BIR dizi API çağrısı olarak ifade edilen belirli bir yük oluşturma kapasitesine sahiptir. Bu sayı, bir çağrı sınırını temsil etmez, ancak kaba kapasite planlamasına izin vermek için maksimum üretilen iş değeri. Gerçek aktarım hızı ve gecikme süresi, eşzamanlı bağlantı sayısı ve hızı, yapılandırılan ilkelerin türü ve sayısı, istek ve yanıt boyutları ve arka uç gecikmesi gibi etkenlere bağlı olarak farklılık gösterir.

Her bir birimin kapasitesi ve fiyatı, birimin bulunduğu **katmana** bağlıdır. Dört katman arasından seçim yapabilirsiniz: **Geliştirici**, **temel**, **Standart**, **Premium**. Bir katman içindeki bir hizmetin kapasitesini artırmanız gerekiyorsa, bir birim eklemeniz gerekir. API Management Örneğinizde seçili olan katman daha fazla birim eklemeye izin vermediğinden, daha üst düzey bir katmana yükseltmeniz gerekir.

Her bir birimin fiyatı ve kullanılabilir Özellikler (örneğin, çok bölgeli dağıtım) API Management örneğiniz için seçtiğiniz katmana bağlıdır. [Fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) makalesi, her katmanda alacağınız birim ve özellik başına fiyatı açıklar. 

>[!NOTE]
>[Fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) makalesi her katmandaki birim kapasitesini yaklaşık olarak gösterir. Daha doğru sayılar almak için API 'leriniz için gerçekçi bir senaryoya bakmanız gerekir. [Azure API Management örneği 'Nin kapasitesine](api-management-capacity.md) bakın.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları izleyerek şunları yapmanız gerekir:

+ Etkin bir Azure aboneliğine sahip olma.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Bir API Management örneğine sahiptir. Daha fazla bilgi için bkz. [Azure API Management örneği oluşturma](get-started-create-service-instance.md).

+ [Azure API Management örneğinin kapasite](api-management-capacity.md)kavramını anlayın.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Yükseltme ve ölçeklendirme  

Dört katman arasından seçim yapabilirsiniz: **Geliştirici**, **temel**,  **Standart** ve **Premium**. **Geliştirici** katmanı, hizmeti değerlendirmek için kullanılmalıdır; üretim için kullanılmamalıdır. **Geliştirici** katmanının SLA 'sı yok ve bu katmanı ölçeklendiremezsiniz (birim Ekle/Kaldır). 

**Temel**, **Standart** ve **Premium** , SLA 'ya sahip olan ve ölçeklendirilebilen üretim katmanlarıdır. **Temel** katman, SLA ile birlikte en fazla iki birime ölçeklendirilebilir ve **Standart** katman, en fazla dört birime ölçeklenuygulanabilir. **Premium** katmana istediğiniz sayıda birim ekleyebilirsiniz.

**Premium** katman, Istenen sayıda Azure bölgesinde tek bir Azure API Management örneği dağıtmanıza olanak sağlar. İlk olarak bir Azure API Management hizmeti oluşturduğunuzda, örnek yalnızca bir birim içerir ve tek bir Azure bölgesinde bulunur. İlk bölge **birincil** bölge olarak atanır. Ek bölgeler kolayca eklenebilir. Bölge eklerken, ayırmak istediğiniz birim sayısını belirtirsiniz. Örneğin, **birincil** bölgede bir biriminiz ve başka bir bölgedeki beş birim olabilir. Birim sayısını her bölgedeki trafiğe uyarlayabilirsiniz. Daha fazla bilgi için bkz. [azure API Management hizmet örneğini birden çok Azure bölgesine dağıtma](api-management-howto-deploy-multi-region.md).

Herhangi bir katmana ve sürümüne yükseltebilir ve buradan indirgeme yapabilirsiniz. Yükseltme veya eski sürüme düşürme, bazı özellikleri (örneğin, sanal ağlar veya çok bölgeli dağıtım), Premium katmandan standart veya temel sürümüne düşürülerek kaldırabilir.

> [!NOTE]
> Yükseltme veya ölçeklendirme işleminin uygulanması 15 ila 45 dakika sürebilir. İşiniz bittiğinde size bildirilir.

> [!NOTE]
> **Tüketim** katmanındaki API Management hizmeti trafiğe göre otomatik olarak ölçeklendirilir.

## <a name="scale-your-api-management-service"></a>API Management hizmetinizi ölçeklendirin

![API Management hizmeti Azure portal ölçeklendirin](./media/upgrade-and-scale/portal-scale.png)

1. [Azure portal](https://portal.azure.com/)API Management hizmetinize gidin.
2. Menüden **konumlar** ' ı seçin.
3. Ölçeklendirmek istediğiniz konuma sahip satıra tıklayın.
4. Yeni **birim** sayısını belirtin-kaydırıcıyı kullanın veya numarayı yazın.
5. **Uygula**’ya tıklayın.

## <a name="change-your-api-management-service-tier"></a>API Management hizmet katmanınızı değiştirin

1. [Azure portal](https://portal.azure.com/)API Management hizmetinize gidin.
2. Menüdeki **fiyatlandırma katmanına** tıklayın.
3. Açılan listeden istenen hizmet katmanını seçin. Değişiklikten sonra API Management hizmetinizin ölçeğini belirtmek için kaydırıcıyı kullanın.
4. **Kaydet**’e tıklayın.

## <a name="downtime-during-scaling-up-and-down"></a>Ölçeği artırma ve azaltma sırasında kapalı kalma süresi
Ya da geliştirici katmanına ölçeklendirebilirsiniz kapalı kalma süresi olacaktır. Aksi takdirde, kapalı kalma süresi yoktur. 

## <a name="compute-isolation"></a>İşlem yalıtımı
Güvenlik gereksinimleriniz [işlem yalıtımı](../azure-government/azure-secure-isolation-guidance.md#compute-isolation)Içeriyorsa, **yalıtılmış** fiyatlandırma katmanını kullanabilirsiniz. Bu katman, bir API Management hizmet örneğinin işlem kaynaklarının tüm fiziksel ana bilgisayarı kullanmasını sağlar ve örneğin ABD Savunma etki düzeyi 5 (IL5) iş yüklerinin desteklenmesi için gereken yalıtım düzeyini sağlar. Yalıtılmış katmana erişim sağlamak için lütfen [bir destek bileti oluşturun](../azure-portal/supportability/how-to-create-azure-support-request.md). 



## <a name="next-steps"></a>Sonraki adımlar

- [Bir Azure API Management hizmeti örneğini birden fazla Azure bölgesine dağıtma](api-management-howto-deploy-multi-region.md)
- [Azure API Management hizmet örneğini otomatik olarak ölçeklendirme](api-management-howto-autoscale.md)
- [API Management maliyetlerini planlayın ve yönetin](plan-manage-costs.md)
- [API Management sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md#api-management-limits)