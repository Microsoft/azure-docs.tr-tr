---
title: Azure VPN ağ geçidi ölçümler üzerinde uyarılar ayarlayın
description: VPN ağ geçidi ölçümler üzerinde uyarılar yapılandırma adımları
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: d57663f683ba4e2107ec6813a19fac7b2dcdd26a
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605225"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>VPN ağ geçidi ölçümler üzerinde uyarılar ayarlayın

Bu makalede Azure VPN ağ geçidi ölçümler ile ilgili uyarılar ayarlamanıza yardımcı olur. Azure İzleyici, Azure kaynakları için uyarıları ayarlama olanağı sağlar. Sanal ağ geçitleri "VPN" türü için uyarılar ayarlayabilirsiniz.


|**Ölçüm**   | **Birim** | **Ayrıntı düzeyi** | **Açıklama** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bayt/sn  | 5 dakika| Ortalama ağ geçidine siteden siteye bağlantılarda tüm birleştirilmiş bant genişliği kullanımı.     |
|**P2SBandwidth**| Bayt/sn  | 1 dakika  | Ortalama ağ geçidi tüm noktadan siteye bağlantıları birleştirilmiş bant genişliği kullanımı.    |
|**P2SConnectionCount**| Count  | 1 dakika  | Ağ geçidinde noktadan siteye bağlantıları sayısı.   |
|**TunnelAverageBandwidth** | Bayt/sn    | 5 dakika  | Ortalama ağ geçidi üzerinde oluşturulan tünelleri bant genişliği kullanımı. |
|**TunnelEgressBytes** | Bayt | 5 dakika | Giden trafiği ağ geçidi üzerinde oluşturulan tünelinde.   |
|**TunnelEgressPackets** | Count | 5 dakika | Ağ geçidi üzerinde oluşturulan tünelinde giden paketlerin sayısı.   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 dakika | Giden paketlerin sayısı tünelinde trafik seçicisini uyumsuzluğu nedeniyle bırakıldı. |
|**TunnelIngressBytes** | Bayt | 5 dakika | Ağ geçidi üzerinde oluşturulmuş tüneller gelen trafiği.   |
|**TunnelIngressPackets** | Count | 5 dakika | Ağ geçidi üzerinde oluşturulan tünelinde gelen paket sayısı.   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 dakika | Gelen paket sayısını tünelinde trafik seçicisini uyumsuzluğu nedeniyle bırakıldı. |


## <a name="setup"></a>Azure portalını kullanarak ölçümlere göre Azure İzleyici uyarıları ayarlama

Aşağıdaki örnek adımlarda, bir ağ geçidi için bir uyarı oluşturacak:

- **Ölçüm:** TunnelAverageBandwidth
- **Koşul:** Bant genişliği > 10 bayt / saniye
- **Penceresi:** 5 dakika
- **Uyarı eylemi:** Email



1. Sanal ağ geçidi kaynağına gidin ve seçin **uyarılar** gelen **izleme** sekmesi. Ardından yeni bir uyarı kuralı oluşturmak veya mevcut bir uyarı kuralı düzenleyin.

   ![Bir uyarı kuralı oluşturmak için seçim](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "oluştur")

2. VPN ağ geçidiniz kaynağı seçin.

   ![Seçme düğmesi ve VPN ağ geçidi kaynakları listesinden](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "seçin")

3. Uyarı için yapılandırmak için bir ölçüm seçin.

   ![Ölçüm listesinde ölçümün seçili](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "seçin")
4. Sinyal mantığını yapılandırma. Ona üç bileşeni vardır:

    a. **Boyutlar**: Ölçüm, boyutları değiştiyse, böylece yalnızca bu boyut veri uyarı değerlendirir belirli boyut değerleri seçebilirsiniz. Bu isteğe bağlıdır.

    b. **Koşul**: Bu ölçüm değeri değerlendirilecek işlemdir.

    c. **Zaman**: Ölçüm verilerinin ayrıntı düzeyi ve uyarı değerlendirmek için süreyi belirtin.

   ![Sinyal mantığını yapılandırma ayrıntıları](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "seçin")

5. Yapılandırılmış kurallara görüntülemek için seçin **uyarı kurallarını yönet**.

   ![Uyarı kuralları yönetmek için düğme](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "seçin")

## <a name="next-steps"></a>Sonraki adımlar

Uyarılar tünel tanılama günlüklerini yapılandırmak için bkz [VPN ağ geçidi tanılama günlükleri ile ilgili uyarılar ayarlamak](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
