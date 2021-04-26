---
title: Azure Web uygulaması güvenlik duvarı ile Azure Sentinel kullanma
description: Bu makalede Azure Sentinel ile Azure Web uygulaması güvenlik duvarı (WAF) ile nasıl kullanılacağı gösterilmektedir
services: web-application-firewall
author: TreMansdoerfer
ms.service: web-application-firewall
ms.date: 10/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 3d905dd1e6acab8f9f6d3885c882dd9c32133cb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596428"
---
# <a name="using-azure-sentinel-with-azure-web-application-firewall"></a>Azure Web uygulaması güvenlik duvarı ile Azure Sentinel kullanma

Azure Sentinel ile birleştirilmiş Azure Web uygulaması güvenlik duvarı (WAF), WAF kaynakları için güvenlik bilgileri olay yönetimi sağlayabilir. Azure Sentinel, Log Analytics kullanarak güvenlik analizi sağlar ve bu da WAF verilerinizi kolayca bölebilir ve görüntülemenizi sağlar. Azure Sentinel kullanarak, önceden oluşturulmuş çalışma kitaplarına erişebilir ve bunları kuruluşunuzun ihtiyaçlarına en iyi şekilde karşılayacak şekilde değiştirebilirsiniz. Çalışma kitabı, Azure Content Delivery Network (CDN) üzerinde WAF analizlerini, Azure ön kapısına WAF 'yi ve birkaç abonelik ve çalışma alanı üzerinde Application Gateway WAF 'yi gösterebilir.

## <a name="waf-log-analytics-categories"></a>WAF Log Analytics kategorileri

WAF Log Analytics aşağıdaki kategorilere ayrılmıştır:  

- Alınan tüm WAF eylemleri 
- İlk 40 engellenen istek URI adresleri 
- İlk 50 olay tetikleyicileri,  
- Zaman içindeki iletiler 
- Tam ileti ayrıntıları 
- İletilere göre saldırı olayları  
- Zaman içinde saldırı olayları 
- İzleme KIMLIĞI filtresi 
- İzleme KIMLIĞI iletileri 
- İlk 10 IP adresi saldırıyor 
- IP adreslerinin saldırı iletileri 

## <a name="waf-workbook-examples"></a>WAF çalışma kitabı örnekleri

Aşağıdaki WAF çalışma kitabı örnekleri örnek verileri gösterir:

:::image type="content" source="media//waf-sentinel/waf-actions-filter.png" alt-text="WAF eylemleri filtresi":::

:::image type="content" source="media//waf-sentinel/top-50-event-trigger.png" alt-text="İlk 50 olay":::

:::image type="content" source="media//waf-sentinel/attack-events.png" alt-text="Saldırı olayları":::

:::image type="content" source="media//waf-sentinel/top-10-attacking-ip-address.png" alt-text="İlk 10 IP adresi saldırıyor":::

## <a name="launch-a-waf-workbook"></a>Bir WAF çalışma kitabı başlatın

WAF çalışma kitabı tüm Azure ön kapısı, Application Gateway ve CDN WAFs için geçerlidir. Bu kaynaklardan verileri bağlamadan önce, kaynağınızın Log Analytics 'in etkinleştirilmesi gerekir. 

Her kaynak için Log Analytics 'i etkinleştirmek üzere tek bir Azure ön kapısına, Application Gateway veya CDN kaynağına gidin:

1. **Tanılama ayarları**' nı seçin.
2. **+ Tanılama ayarı Ekle**' yi seçin. 
3. Tanılama ayarı sayfasında:
   1. Bir ad yazın. 
   1. **Log Analytics gönder**' i seçin. 
   1. Günlük hedefi çalışma alanını seçin. 
   1. Çözümlemek istediğiniz günlük türlerini seçin:
      1. Application Gateway: ' ApplicationGatewayAccessLog ' ve ' ApplicationGatewayFirewallLog '
      1. Azure ön kapısı: ' FrontDoorAccessLog ' ve ' FrontDoorFirewallLog '
      1. CDN: ' AzureCdnAccessLog '
   1. **Kaydet**’i seçin.

   :::image type="content" source="media//waf-sentinel/diagnostics-setting.png" alt-text="Tanılama ayarı":::

4. Azure giriş sayfasında, arama çubuğuna **Azure Sentinel** yazın ve **Azure Sentinel** kaynağını seçin. 
2. Zaten etkin bir çalışma alanı seçin veya yeni bir çalışma alanı oluşturun. 
3. Sol taraftaki panelde **yapılandırma** ' nın altında **veri bağlayıcıları**' nı seçin.
4. **Microsoft Web uygulaması güvenlik duvarı 'nı** arayın ve **Microsoft Web uygulaması güvenlik duvarı (WAF)** seçeneğini belirleyin. Sağ alt köşedeki **bağlayıcı sayfasını aç** ' ı seçin.

   :::image type="content" source="media//waf-sentinel/data-connectors.png" alt-text="Veri bağlayıcıları":::

8. Daha önce yapmadıysanız, Günlük Analizi verilerinin olmasını istediğiniz her bir WAF kaynağı için **yapılandırma** bölümündeki yönergeleri izleyin.
6. Tek bir WAF kaynağını yapılandırmayı tamamladıktan sonra, **sonraki adımlar** sekmesini seçin. Önerilen çalışma kitaplarından birini seçin. Bu çalışma kitabı, daha önce etkinleştirilen tüm günlük analitik verilerini kullanır. Artık WAF kaynaklarınız için çalışan bir WAF çalışma kitabı bulunmalıdır.

   :::image type="content" source="media//waf-sentinel/waf-workbooks.png" alt-text="WAF çalışma kitapları":::


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Sentinel hakkında daha fazla bilgi edinin](../sentinel/overview.md)
- [Azure Izleyici çalışma kitapları hakkında daha fazla bilgi edinin](../azure-monitor/visualize/workbooks-overview.md)
