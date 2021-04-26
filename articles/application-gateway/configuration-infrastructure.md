---
title: Azure Application Gateway altyapı yapılandırması
description: Bu makalede, Azure Application Gateway altyapısının nasıl yapılandırılacağı açıklanır.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: f214b0b0751f44ea1357f569fd814a7621af61ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397629"
---
# <a name="application-gateway-infrastructure-configuration"></a>Application Gateway altyapı yapılandırması

Uygulama ağ geçidi altyapısı, sanal ağ, alt ağlar, ağ güvenlik grupları ve Kullanıcı tanımlı yolları içerir.

## <a name="virtual-network-and-dedicated-subnet"></a>Sanal ağ ve ayrılmış alt ağ

Uygulama ağ geçidi, sanal ağınızdaki adanmış bir dağıtımdır. Sanal ağınızda, uygulama ağ geçidi için adanmış bir alt ağ gerekir. Bir alt ağda belirli bir uygulama ağ geçidi dağıtımının birden fazla örneğine sahip olabilirsiniz. Ayrıca, alt ağdaki diğer uygulama ağ geçitlerini da dağıtabilirsiniz. Ancak uygulama ağ geçidi alt ağında başka herhangi bir kaynak dağıtamazsınız. Aynı alt ağda Standard_v2 ve standart Azure Application Gateway karıştırılamaz.

> [!NOTE]
> [Sanal ağ hizmeti uç noktası ilkeleri](../virtual-network/virtual-network-service-endpoint-policies-overview.md) şu anda Application Gateway bir alt ağda desteklenmiyor.

### <a name="size-of-the-subnet"></a>Alt ağın boyutu

Application Gateway, örnek başına bir özel IP adresi ve özel bir ön uç IP yapılandırılmışsa başka bir özel IP adresi kullanır.

Azure, iç kullanım için her alt ağda beş IP adresini de ayırır: ilk dört ve son IP adresi. Örneğin, özel ön uç IP 'si olmayan 15 Application Gateway örnekleri göz önünde bulundurun. Bu alt ağ için en az 20 IP adresiniz olması gerekir: iç kullanım için beş ve Application Gateway örnekleri için 15.

27 Application Gateway örnekleri ve bir özel ön uç IP 'si için IP adresi olan bir alt ağ düşünün. Bu durumda, uygulama ağ geçidi örnekleri için, biri özel ön uç için, diğeri de iç kullanım için beş IP adresi 33 gerekir: 27.

Application Gateway (Standart veya WAF) SKU, en fazla 32 örneği destekleyebilir (32 örnek IP adresi + 1 özel ön uç IP + 5 Azure ayrılmış) – bu nedenle, en az bir alt ağ boyutu/26 önerilir

Application Gateway (Standard_v2 veya WAF_v2 SKU) en fazla 125 örneği destekleyebilir (125 örnek IP adresi + 1 özel ön uç IP + 5 Azure ayrılmış) – bu nedenle en düşük alt ağ boyutu/24 önerilir

## <a name="network-security-groups"></a>Ağ güvenlik grupları

Ağ güvenlik grupları (NSG 'ler) Application Gateway desteklenir. Ancak bazı kısıtlamalar vardır:

- Application Gateway v1 SKU 'SU için 65503-65534 TCP bağlantı noktalarında gelen Internet trafiğine ve **Gatewaymanager** hizmet **etiketi ile hedef** alt ağa sahip v2 SKU 'su için TCP bağlantı noktaları 65200-65535 ' ye izin vermelisiniz. Bu bağlantı noktası aralığı, Azure altyapı iletişimi için gereklidir. Bu bağlantı noktaları Azure sertifikaları tarafından korunur (kilitlidir). Bu ağ geçitlerinin müşterileri de dahil olmak üzere dış varlıklar bu uç noktalar üzerinde iletişim kuramaz.

- Giden Internet bağlantısı engellenmiyor. NSG 'deki varsayılan giden kurallar Internet bağlantısına izin verir. Şunları yapmanızı öneririz:

  - Varsayılan giden kurallarını kaldırmayın.
  - Giden bağlantıları reddeden diğer çıkış kuralları oluşturmayın.

- Hedef alt ağa sahip **AzureLoadBalancer** etiketindeki trafiğe izin **verilmesi gerekir.**

### <a name="allow-access-to-a-few-source-ips"></a>Birkaç kaynak IP 'ye erişime izin ver

Bu senaryo için Application Gateway alt ağında NSG 'leri kullanın. Aşağıdaki kısıtlamaları alt ağa bu öncelik sırasına göre yerleştirin:

1. Kaynak IP veya IP aralığından gelen trafiğe, gelen erişim bağlantı noktası (örneğin, HTTP erişimi için bağlantı noktası 80) olarak tüm Application Gateway alt ağ adres aralığı ve hedef bağlantı noktası olarak izin verin.
2. Application Gateway v1 SKU 'SU için 65503-65534 olarak **kaynak ve hedef** bağlantı **noktası olarak kaynak** kaynaklı gelen isteklere ve [arka uç sistem durumu iletişimi](./application-gateway-diagnostics.md)için v2 SKU 'su için 65200-65535 bağlantı noktalarına izin verin. Bu bağlantı noktası aralığı, Azure altyapı iletişimi için gereklidir. Bu bağlantı noktaları Azure sertifikaları tarafından korunur (kilitlidir). Uygun sertifikalar yerine, dış varlıklar bu uç noktalar üzerinde değişiklik başlatamaz.
3. [Ağ güvenlik grubundaki](../virtual-network/network-security-groups-overview.md)gelen Azure Load Balancer Araştırmaları (*AzureLoadBalancer* Tag) ve gelen sanal ağ trafiğine (*VirtualNetwork* etiketi) izin verin.
4. Engelle-All kuralını kullanarak diğer tüm gelen trafiği engelleyin.
5. Tüm hedefler için Internet 'e giden trafiğe izin verin.

## <a name="supported-user-defined-routes"></a>Desteklenen Kullanıcı tanımlı yollar 

> [!IMPORTANT]
> Application Gateway alt ağında UDRs kullanmak, [arka uç sistem durumu görünümündeki](./application-gateway-diagnostics.md#back-end-health) sistem durumunun **Bilinmeyen** olarak görünmesine neden olabilir. Ayrıca, Application Gateway günlüklerin ve ölçümlerin oluşturulmasına neden olabilir. Arka uç sistem durumunu, günlükleri ve ölçümleri görüntüleyebilmeniz için Application Gateway alt ağında UDRs 'yi kullanmanızı öneririz.

- **v1**

   V1 SKU 'SU için, Kullanıcı tanımlı yollar (UDRs), uçtan uca istek/yanıt iletişimini değiştirmediği sürece Application Gateway alt ağında desteklenir. Örneğin, paket incelemesi için bir güvenlik duvarı gereci işaret etmek üzere Application Gateway alt ağında bir UDR ayarlayabilirsiniz. Ancak, inceleme sonrasında paketin amaçlanan hedefe ulaşabildiğinizden emin olmanız gerekir. Bunun başarısız olması, sistem durumu araştırmasına veya trafik yönlendirme davranışına yol açabilir. Bu, Azure ExpressRoute veya sanal ağdaki VPN ağ geçitleri tarafından yayılan, öğrenilen yolları veya varsayılan 0.0.0.0/0 yollarını içerir. 0.0.0.0/0 ' ın şirket içinde yeniden yönlendirilmesi gereken (Zorlamalı tünel) her senaryo v1 için desteklenmez.

- **v2**

   V2 SKU 'SU için desteklenen ve desteklenmeyen senaryolar vardır:

   **v2 desteklenen senaryolar**
   > [!WARNING]
   > Yol tablosunun yanlış yapılandırması, Application Gateway v2 'de simetrik yönlendirmeye neden olabilir. Tüm yönetim/denetim düzlemi trafiğinin bir Sanal Gereç üzerinden değil, doğrudan Internet 'e gönderildiğinden emin olun. Günlüğe kaydetme ve ölçümler de etkilenebilir.


  **Senaryo 1**: sınır ağ GEÇIDI Protokolü (BGP) yol yaymayı Application Gateway alt ağa devre dışı bırakmak için UDR

   Bazen varsayılan ağ geçidi yolu (0.0.0.0/0) Application Gateway sanal ağla ilişkili ExpressRoute veya VPN Gateway 'ler aracılığıyla tanıtılabilir. Bu, Internet 'e doğrudan yol gerektiren yönetim düzlemi trafiğini keser. Bu tür senaryolarda, BGP yol yaymayı devre dışı bırakmak için bir UDR kullanılabilir. 

   BGP yol yaymayı devre dışı bırakmak için aşağıdaki adımları kullanın:

   1. Azure 'da bir yol tablosu kaynağı oluşturun.
   2. **Sanal ağ geçidi yol yayma** parametresini devre dışı bırakın. 
   3. Yol tablosunu uygun alt ağla ilişkilendirin. 

   Bu senaryo için UDR 'nin etkinleştirilmesi, mevcut tüm kurulumları bozmamalıdır.

  **2. senaryo**: Internet 'e 0.0.0.0/0 öğesini yönlendirmek için UDR

   0.0.0.0/0 trafiğini doğrudan Internet 'e göndermek için bir UDR oluşturabilirsiniz. 

  **Senaryo 3**: Kubernetes kullanan ile Azure Kubernetes hizmeti için UDR

  Azure Kubernetes hizmeti (AKS) ve Application Gateway giriş denetleyicisi (AGIC) ile Kubernetes kullanan kullanıyorsanız, Application Gateway olan ve alt öğe için gönderilen trafiğin doğru düğüme yönlendirilmesini sağlamak için bir yol tablosu gerekir. Azure CNı kullanıyorsanız bu gerekli değildir. 

  Kubernetes kullanan 'in çalışmasına izin vermek için yol tablosunu kullanmak üzere aşağıdaki adımları izleyin:

  1. AKS tarafından oluşturulan kaynak grubuna gidin (kaynak grubunun adı "MC_" ile başlamalıdır)
  2. Bu kaynak grubunda AKS tarafından oluşturulan yol tablosunu bulun. Yol tablosu aşağıdaki bilgilerle doldurulmalıdır:
     - Adres ön eki, AKS 'te erişmek istediğiniz nesnelerin IP aralığı olmalıdır. 
     - Sonraki atlama türü Sanal Gereç olmalıdır. 
     - Sonraki atlama adresi, pods 'yi barındıran düğümün IP adresi olmalıdır.
  3. Bu yol tablosunu Application Gateway alt ağıyla ilişkilendirin. 
    
  **v2 desteklenmeyen senaryolar**

  **Senaryo 1**: sanal gereçler için UDR

  0.0.0.0/0 ' ın herhangi bir Sanal Gereç aracılığıyla yeniden yönlendirilmesi gereken herhangi bir senaryo, bir hub/bağlı sanal ağ veya şirket içi (Zorlamalı tünel) v2 için desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Ön uç IP adresi yapılandırması hakkında bilgi edinin](configuration-front-end-ip.md).