---
title: Azure DDoS koruması simülasyonu testi
description: Benzetimler üzerinden test etme hakkında bilgi edinin
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 4e6c6b441684d3877a8b85f40b650b257a5159da
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106080"
---
# <a name="test-through-simulations"></a>Simülasyonlar aracılığıyla test etme

Düzenli benzetimler sunarak hizmetlerinizin bir saldırıya nasıl yanıt vereceğini gösteren varsayımları test etmek iyi bir uygulamadır. Sınama sırasında, hizmetlerinizin veya uygulamalarınızın beklenen şekilde çalışmaya devam etmesini ve kullanıcı deneyiminin kesintiye uğramaması gerektiğini doğrulayın. Hem teknoloji hem de süreç açısından boşlukları belirleyip DDoS yanıt stratejisinden dahil edin. Üretim ortamına etkisini en aza indirmek için, hazırlama ortamlarında veya yoğun olmayan saatlerde bu tür testleri gerçekleştirmenizi öneririz.

Azure müşterilerinin simülasyonlar için DDos Koruması etkinleştirilmiş olan genel uç noktalara trafik üretebileceği bir arabirim oluşturmak üzere, self servis bir trafik oluşturucu olan [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) ile birlikte çalıştık. Simülasyonu kullanarak:

- Azure DDoS Koruması'nın Azure kaynaklarınızı DDoS saldırılarına karşı korumanıza nasıl yardımcı olduğunu doğrulayabilirsiniz.
- DDoS saldırısı altındayken olay yanıt sürecinizi iyileştirebilirsiniz.
- DDoS uyumluluğunu belgeleyebilirsiniz.
- Ağ güvenlik ekiplerinizi eğitebilirsiniz.

> [!NOTE]
> BreakingPoint bulutu yalnızca genel bulut için kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

- Bu öğreticideki adımları tamamlayabilmeniz için önce, korunan genel IP adreslerine sahip bir [Azure DDoS standart koruma planı](manage-ddos-protection.md) oluşturmanız gerekir.
- Önce [BreakingPoint bulutu](http://breakingpoint.cloud/)ile bir hesap oluşturmanız gerekir. 

## <a name="configure-a-ddos-test-attack"></a>DDoS test saldırısı yapılandırma

1. Aşağıdaki değerleri girin veya seçin ve ardından **Testi Başlat**' ı seçin:

    |Ayar        |Değer                                              |
    |---------      |---------                                          |
    |Hedef IP adresi           | Test etmek istediğiniz genel IP adresinden birini girin.                     |
    |Bağlantı Noktası Numarası   | _443_ girin.                       |
    |DDoS profili | Olası değerler şunlardır,,,, `DNS Flood` `NTPv2 Flood` `SSDP Flood` `TCP SYN Flood` `UDP 64B Flood` , `UDP 128B Flood` , `UDP 256B Flood` , `UDP 512B Flood` , `UDP 1024B Flood` , `UDP 1514B Flood` , `UDP Fragmentation` , `UDP Memcached` .|
    |Test boyutu       | Olası değerler şunlardır,,, `100K pps, 50 Mbps and 4 source IPs` `200K pps, 100 Mbps and 8 source IPs` `400K pps, 200Mbps and 16 source IPs` `800K pps, 400 Mbps and 32 source IPs` .                                  |
    |Test süresi | Olası değerler şunlardır,,,, `10 Minutes` `15 Minutes` `20 Minutes` `25 Minutes` `30 Minutes` .|

Şu şekilde görünür:

![DDoS saldırı simülasyonu örneği: BreakingPoint bulutu](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>İzleme ve doğrulama

1. ' Da oturum açın https://portal.azure.com ve aboneliğinize gidin.
1. Saldırıyı sınadığınız genel IP adresini seçin.
1. **İzleme** seçeneğinin altından **Ölçümler**’i seçin.
1. **Ölçüm** Için _DDoS saldırısı altında_ öğesini seçin.

Kaynak saldırıya ulaştıktan sonra, aşağıdaki resimde olduğu gibi değerin **0** ' dan **1**' e değiştiği görmeniz gerekir:

![DDoS saldırı simülasyonu örneği: Portal](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

### <a name="breakingpoint-cloud-api-script"></a>BreakingPoint bulutu API betiği

Bu [API betiği](https://aka.ms/ddosbreakingpoint) , bir kez çalıştırılarak veya sıradan testleri zamanlamak için cron kullanarak DDoS testini otomatikleştirmek için kullanılabilir. Bu, günlüğün düzgün yapılandırıldığını ve algılama ve yanıt yordamlarının etkin olduğunu doğrulamak için kullanışlıdır. Betikler bir Linux işletim sistemi (Ubuntu 18,04 LTS ile test edilmiştir) ve Python 3 gerektirir. Dahil edilen betiği kullanarak veya [BreakingPoint Cloud](http://breakingpoint.cloud/) Web sitesindeki belgeleri kullanarak ÖNKOŞULLARı ve API istemcisini yükler.

## <a name="next-steps"></a>Sonraki adımlar

- [DDoS koruması telemetrisini görüntülemeyi ve yapılandırmayı](telemetry.md)öğrenin.
- [DDoS tanılama günlüğünü görüntülemeyi ve yapılandırmayı](diagnostic-logging.md)öğrenin.
- [DDoS hızlı yanıt verme](ddos-rapid-response.md)hakkında bilgi edinin.
