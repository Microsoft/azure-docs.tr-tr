---
title: Azure VPN Gateway tanılama günlüğü olayları üzerinde uyarı ayarlama
description: VPN Gateway tanılama günlüğü olaylarında uyarı yapılandırma adımları
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: d914c020553bace7ea5ab8898ac4093fea30e6c9
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307001"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>VPN Gateway tanılama günlüğü olayları üzerinde uyarı ayarlama

Bu makale Azure Log Analytics kullanarak Azure VPN Gateway tanılama günlüğü olaylarını temel alan uyarılar ayarlamanıza yardımcı olur. 

Aşağıdaki Günlükler Azure 'da kullanılabilir:

|***Name*** | ***Açıklama*** |
|---        | ---               |
|GatewayDiagnosticLog | Ağ Geçidi yapılandırma olayları, birincil değişiklikler ve bakım olayları için tanılama günlüklerini içerir |
|Tüneldiagnosticlog | Tünel durumu değişikliği olaylarını içerir. Tünel bağlantısı/bağlantı kesme olayları, uygunsa durum değişikliği için özetlenen bir nedene sahiptir |
|RouteDiagnosticLog | Ağ geçidinde gerçekleşen statik rotalara ve BGP olaylarına yapılan değişiklikleri günlüğe kaydeder |
|Ikediagnosticlog | Ağ geçidinde ıKE denetim iletilerini ve olaylarını günlüğe kaydeder |
|P2SDiagnosticLog | Ağ geçidinde Noktadan siteye denetim iletilerini ve olaylarını günlüğe kaydeder |

## <a name="setup"></a>Uyarıları ayarlama

Aşağıdaki örnek adımlar, siteden siteye VPN tünelini içeren bir bağlantı kesme olayı için uyarı oluşturur:


1. Azure portal, **tüm hizmetler** altında **Log Analytics** araması yapın ve **Log Analytics çalışma alanları**' nı seçin.

   ![Log Analytics çalışma alanlarına gitmek Için seçimler](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Oluştur")

2. **Log Analytics** sayfasında **Oluştur** ' u seçin.

   ![Oluştur düğmesi içeren Log Analytics sayfası](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Seç")

3. **Yeni oluştur** ' u seçin ve ayrıntıları girin.

   ![Log Analytics çalışma alanı oluşturma ayrıntıları](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Seç")

4. **Tanılama ayarlarını** **İzle** > dikey penceresinde VPN ağ geçidinizi bulun.

   ![Tanılama AYARLARıNDA VPN ağ geçidini bulma seçimleri](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Seç")

5. Tanılamayı açmak için, ağ geçidine çift tıklayın ve ardından **tanılamayı aç**' ı seçin.

   ![Tanılamayı açmak Için seçimler](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Seç")

6. Ayrıntıları girin ve Log Analytics ve **Tunneldiagnosticlog** ' a **Gönder** ' in seçildiğinden emin olun. Adım 3 ' te oluşturduğunuz Log Analytics çalışma alanını seçin.

   ![Seçili onay kutuları](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Seç")

7. Sanal ağ geçidi kaynağına genel bakış ' a gidin ve **izleme** sekmesinden **Uyarılar** ' ı seçin. Sonra yeni bir uyarı kuralı oluşturun veya var olan bir uyarı kuralını düzenleyin.

   ![Yeni bir uyarı kuralı oluşturmaya yönelik seçimler](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Seç")

   ![Noktadan siteye](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Seç")
8. Log Analytics çalışma alanını ve kaynağı seçin.

   ![Çalışma alanı ve kaynak seçimleri](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Seç")

9. **Koşul Ekle**altında, sinyal mantığı olarak **özel günlük araması** ' nı seçin.

   ![Özel günlük araması Için seçimler](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Seç")

10. **Arama sorgusu** metin kutusuna aşağıdaki sorguyu girin. < > Ve TimeGenerated değerlerini uygun şekilde değiştirin.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Eşik değerini 0 olarak ayarlayın ve **bitti**' yi seçin.

    Bir ![sorgu girme ve eşik seçme](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Seç")

11. **Kural oluştur** sayfasında, **eylem grupları** bölümünde **Yeni oluştur** ' u seçin. Ayrıntıları girin ve **Tamam**' ı seçin.

    ![Yeni bir eylem grubu Için Ayrıntılar](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Seç")

12. **Kural oluştur** sayfasında, **eylem grubu adı** bölümünde doğru adın göründüğünden emin olmak için, ayrıntıları **Özelleştir** ' in ayrıntılarını girin. Kuralı oluşturmak için **Uyarı kuralı oluştur** ' u seçin.

    ![Kural oluşturmaya yönelik seçimler](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Seç")

## <a name="next-steps"></a>Sonraki adımlar

Tünel ölçümlerinde uyarıları yapılandırmak için bkz. [VPN Gateway ölçümleri üzerinde uyarıları ayarlama](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
