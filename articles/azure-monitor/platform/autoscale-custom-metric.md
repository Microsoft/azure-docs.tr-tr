---
title: Özel ölçüm kullanarak Azure 'da otomatik ölçeklendirme
description: Azure 'da kaynağı özel ölçüm ile ölçeklendirmenin nasıl yapılacağını öğrenin.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: f83c68dd2f2ddbb73974a2ea4e91e746308945c6
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958107"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Azure 'da özel ölçüm ile otomatik ölçeklendirmeyi kullanmaya başlama
Bu makalede, Azure portal bir özel ölçüm tarafından kaynağınızın nasıl ölçekleneceğini açıklanmaktadır.

Azure Izleyici otomatik ölçeklendirme yalnızca [Sanal Makine Ölçek Kümeleri](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)ve [API Management Hizmetleri](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)için geçerlidir.

## <a name="lets-get-started"></a>Başlamanıza izin verir
Bu makalede, Application Insights 'ın yapılandırıldığı bir Web uygulamasına sahip olduğunuz varsayılır. Henüz bir hesabınız yoksa, [ASP.NET Web siteniz için Application Insights ayarlayabilirsiniz][1]

- [Azure Portal][2] açın
- Sol gezinti bölmesinde Azure Izleyici simgesine tıklayın.
  Azure Izleyici 'yi başlatmak ![][3]
- Otomatik ölçeklendirmenin geçerli olduğu tüm kaynakları görüntülemek için otomatik ölçeklendirme ayarı ' na tıklayın ![Azure izleyici 'de otomatik ölçeklendirmeyi bulun][4]
- Azure Izleyici 'de ' otomatik ölçeklendirme ' dikey penceresini açın ve ölçeklendirmek istediğiniz kaynağı seçin
  > Unutmayın: aşağıdaki adımlarda, Application Insights 'ın yapılandırıldığı bir Web uygulamasıyla ilişkili bir App Service planı kullanılır.
- Kaynak için ölçek ayarı dikey penceresinde, geçerli örnek sayısı ' nın 1 olduğuna dikkat edin. ' Otomatik ölçeklendirmeyi etkinleştir ' seçeneğine tıklayın.
  Yeni Web uygulaması için ölçek ayarı ![][5]
- Ölçek ayarı için bir ad girin ve "kural ekle" seçeneğine tıklayın. Sağ taraftaki bağlam bölmesi olarak açılan ölçek kuralı seçeneklerine dikkat edin. Varsayılan olarak, kaynağın CPU yüzdesi %70 ' ü aşarsa, örnek sayınız 1 ' i ölçeklendirme seçeneğini ayarlar. En üstteki ölçüm kaynağını "Application Insights" olarak değiştirin, ' kaynak ' açılan menüsünde App Insights kaynağını seçin ve ardından ölçeklendirmek istediğiniz özel ölçümü seçin.
  Özel ölçüme göre ölçeği ![][6]
- Yukarıdaki adıma benzer şekilde, özel ölçüm bir eşiğin altındaysa ölçek sayısını 1 ' e ölçeklendirecek bir ölçek kuralı ekleyin.
  ![CPU][7] göre ölçeklendirin
- Örnek sınırlarını ayarlayın. Örneğin, özel ölçüm dalgalanmalarına bağlı olarak 2-5 örnek arasında ölçeklendirme yapmak istiyorsanız, ' en az ' değerini ' 2 ', ' en yüksek ' değerini ' 5 ' ve ' default ' olarak ' 2 ' olarak ayarlayın
  > Note: kaynak ölçümlerini okurken bir sorun olması ve geçerli kapasitenin varsayılan kapasitenin altında olduğundan emin olmak için, otomatik ölçeklendirme, kaynak kullanılabilirliğini sağlamak üzere varsayılan değere ölçeklendirecektir. Geçerli kapasite varsayılan kapasiteden daha yüksekse, otomatik ölçeklendirme bu şekilde ölçeklenmez.
- ' Kaydet 'e tıklayın

Tebrikler. Artık ölçek ayarınızı, Web uygulamanızı özel bir ölçüme göre otomatik olarak ölçeklendirmek için başarıyla oluşturdunuz.

> Note: bir VMSS veya bulut hizmeti rolü ile çalışmaya başlamak için aynı adımlar geçerlidir.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png

