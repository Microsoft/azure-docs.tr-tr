---
title: 'Azure VPN Gateway: paket yakalamaları yapılandırma'
description: VPN ağ geçitleri üzerinde kullanabileceğiniz paket yakalama işlevleri hakkında bilgi edinin.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 41c36d302605bb619899131a8ace649b0f1439b2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151848"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>VPN ağ geçitleri için paket yakalamaları yapılandırma

Bağlantı ve performansla ilgili sorunlar genellikle daha karmaşıktır ve sorunun nedenini azaltmak için önemli miktarda zaman ve çaba alır. Paket yakalama özelliği büyük ölçüde sorunun kapsamını ağın müşteri tarafında, ağın Azure tarafında veya arasında bir yerde olup olmadığı gibi, ağın belirli bölümleriyle daraltma süresini azaltmaya yardımcı olur. Sorun daraltıldıktan sonra, hata ayıklama ve düzeltici eylem gerçekleştirmek çok daha verimlidir.

Paket yakalama için bazı yaygın olarak kullanılabilecek araçlar vardır. Bununla birlikte, bu araçları kullanarak ilgili paket yakalamalarını almak genellikle yüksek hacimli trafik senaryolarıyla çalışırken özellikle çok daha az zaman alır. Bir VPN ağ geçidi paket yakalaması tarafından sunulan filtreleme özellikleri, önemli bir farklılık haline gelir. Yaygın olarak kullanılabilir paket yakalama araçlarına ek olarak bir VPN Gateway paket yakalaması kullanabilirsiniz.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>VPN Gateway paket yakalama filtreleme özellikleri

VPN Gateway paket yakalamaları, ağ geçidinde veya müşteri gereksinimlerine bağlı olarak belirli bir bağlantıda çalıştırılabilir. Aynı anda birden çok tünelde paket yakalamalarını çalıştırabilirsiniz. Bir VPN ağ geçidi üzerinde filtreleme ile birlikte tek veya çift yönlü trafiği, ıKE ve ESP trafiğini ve iç paketleri yakalayabilirsiniz.

5 tanımlama grubu filtreleri (kaynak alt ağ, hedef alt ağ, kaynak bağlantı noktası, hedef bağlantı noktası, protokol) ve TCP bayrakları (SYN, ACK, FIN, URG, PSH, RST), yüksek hacimli bir trafikte sorunları yalıtmak için yararlıdır.

## <a name="setup-packet-capture-using-powershell"></a>PowerShell kullanarak paket yakalamayı ayarlama

Paket yakalamalarını başlatmak ve durdurmak için PowerShell komutları için aşağıdaki örneklere bakın. Parametre seçenekleri hakkında daha fazla bilgi için (örneğin, Filtreler oluşturma), bu PowerShell [belgesine](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)bakın.

### <a name="start-packet-capture-for-a-vpn-gateway"></a>VPN ağ geçidi için paket yakalamayı Başlat

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

İsteğe bağlı parametre **filtreverileri** filtre uygulamak için kullanılabilir.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>VPN ağ geçidi için paket yakalamayı durdur

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>VPN Ağ Geçidi bağlantısı için paket yakalamayı Başlat

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

İsteğe bağlı parametre **filtreverileri** filtre uygulamak için kullanılabilir.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>VPN ağ geçidi bağlantısında paket yakalamayı durdur

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular

- Paket yakalamalarını çalıştırmak performansı etkileyebilir. Gerekli olmadığında paket yakalamayı durdurmayı unutmayın.
- Önerilen minimum paket yakalama süresi 600 saniyedir. Yol üzerindeki birden çok bileşen arasındaki eşitleme sorunları nedeniyle, daha kısa bir paket yakalama süresi, verilerin tamamını sağlamayabilir.
- Paket yakalama veri dosyaları PCAP veya ETL biçimlerinde oluşturulur. Verileri anlamak için Netmon Parser gerekebilir.

## <a name="next-steps"></a>Sonraki adımlar

VPN Gateway hakkında daha fazla bilgi için bkz. [VPN Gateway hakkında](vpn-gateway-about-vpngateways.md)