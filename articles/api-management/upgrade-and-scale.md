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
ms.date: 08/18/2018
ms.author: apimpm
ms.openlocfilehash: 64649c86dbd3c3469247308bfc4dd0ed12e06949
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018230"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Azure API Management örneğini yükseltme ve ölçeklendirme  

Müşteriler, birim ekleyerek ve kaldırarak bir Azure API Management (APıM) örneğini ölçeklendirebilir. Bir **birim** adanmış Azure kaynaklarından oluşur ve ayda BIR dizi API çağrısı olarak ifade edilen belirli bir yük oluşturma kapasitesine sahiptir. Bu sayı, bir çağrı sınırını temsil etmez, ancak kaba kapasite planlamasına izin vermek için maksimum üretilen iş değeri. Gerçek aktarım hızı ve gecikme süresi, eşzamanlı bağlantı sayısı ve hızı, yapılandırılan ilkelerin türü ve sayısı, istek ve yanıt boyutları ve arka uç gecikmesi gibi etkenlere bağlı olarak farklılık gösterir.

Her bir birimin kapasitesi ve fiyatı, birimin bulunduğu **katmana** bağlıdır. Dört katman arasından seçim yapabilirsiniz: **Geliştirici**, **temel**, **Standart**, **Premium**. Bir katman içindeki bir hizmetin kapasitesini artırmanız gerekiyorsa, bir birim eklemeniz gerekir. APıM Örneğinizde seçili olan katman daha fazla birim eklemeye izin vermediğinden, daha üst düzey bir katmana yükseltmeniz gerekir.

Her bir birimin fiyatı ve kullanılabilir Özellikler (örneğin, çok bölgeli dağıtım), APıM örneğiniz için seçtiğiniz katmana bağlıdır. [Fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) makalesi, her katmanda alacağınız birim ve özellik başına fiyatı açıklar. 

>[!NOTE]
>[Fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) makalesi her katmandaki birim kapasitesini yaklaşık olarak gösterir. Daha doğru sayılar almak için API 'leriniz için gerçekçi bir senaryoya bakmanız gerekir. [Azure API Management örneği 'Nin kapasitesine](api-management-capacity.md) bakın.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları izleyerek şunları yapmanız gerekir:

+ Etkin bir Azure aboneliğiniz olmalıdır.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Bir APıM örneğiniz olmalıdır. Daha fazla bilgi için bkz. [Azure API Management örneği oluşturma](get-started-create-service-instance.md).

+ [Azure API Management örneğinin kapasite](api-management-capacity.md)kavramını anlayın.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Yükseltme ve ölçeklendirme  

Dört katman arasından seçim yapabilirsiniz: **Geliştirici**, **temel**, **Standart** ve **Premium**. **Geliştirici** katmanı, hizmeti değerlendirmek için kullanılmalıdır; üretim için kullanılmamalıdır. **Geliştirici** katmanının SLA 'sı yok ve bu katmanı ölçeklendiremezsiniz (birim Ekle/Kaldır). 

**Temel**, **Standart** ve **Premium** SLA 'ya sahip olan ve ölçeklendirilebilen üretim katmanlarıdır. **Temel** katman SLA 'ya sahip olan ve 2 birime kadar ölçeklenebilen, **Standart** katman en fazla dört birime ölçeklendirilebilir olan en ucuz katmandır. **Premium** katmana istediğiniz sayıda birim ekleyebilirsiniz.

**Premium** katman, Istenen sayıda Azure bölgesinde tek bir Azure API Management örneği dağıtmanıza olanak sağlar. İlk olarak bir Azure API Management hizmeti oluşturduğunuzda, örnek yalnızca bir birim içerir ve tek bir Azure bölgesinde bulunur. İlk bölge **birincil** bölge olarak atanır. Ek bölgeler kolayca eklenebilir. Bölge eklerken, ayırmak istediğiniz birim sayısını belirtirsiniz. Örneğin, **birincil** bölgede bir biriminiz ve başka bir bölgedeki beş birim olabilir. Birim sayısını her bölgedeki trafiğe uyarlayabilirsiniz. Daha fazla bilgi için bkz. [azure API Management hizmet örneğini birden çok Azure bölgesine dağıtma](api-management-howto-deploy-multi-region.md).

Herhangi bir katmana ve sürümüne yükseltebilir ve buradan indirgeme yapabilirsiniz. Yükseltme veya eski sürüme düşürme, bazı özellikleri (örneğin, sanal ağlar veya çok bölgeli dağıtım), Premium katmandan standart veya temel sürümüne düşürülerek kaldırabileceğini unutmayın.

> [!NOTE]
> Yükseltmesi veya ölçeği işlemi uygulamak için 15 ila 45 dakika sürebilir. İşiniz bittiğinde size bildirilir.

> [!NOTE]
> **Tüketim** katmanındaki API Management hizmeti trafiğe göre otomatik olarak ölçeklendirilir.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Yükseltmek ve ölçeklendirmek için Azure portal kullanın

![Azure portal 'da APıM 'i ölçeklendirin](./media/upgrade-and-scale/portal-scale.png)

1. [Azure Portal](https://portal.azure.com/)APIM örneğinize gidin.
2. Menüden **ölçek ve fiyatlandırma '** yı seçin.
3. İstediğiniz katmanı seçin.
4. Eklemek istediğiniz **birim** sayısını belirtin. Kaydırıcıyı kullanabilir ya da birim sayısını yazabilirsiniz.  
    **Premium** katmanı seçerseniz, önce bir bölge seçmeniz gerekir.
5. **Kaydet**’e basın.

## <a name="downtime-during-scaling-up-and-down"></a>Ölçeği artırma ve azaltma sırasında kapalı kalma süresi
Ya da geliştirici katmanına ölçeklendirebilirsiniz kapalı kalma süresi olacaktır. Aksi takdirde, kapalı kalma süresi yoktur. 


## <a name="next-steps"></a>Sonraki adımlar

- [Bir Azure API Management hizmeti örneğini birden fazla Azure bölgesine dağıtma](api-management-howto-deploy-multi-region.md)
- [Azure API Management hizmet örneğini otomatik olarak ölçeklendirme](api-management-howto-autoscale.md)
