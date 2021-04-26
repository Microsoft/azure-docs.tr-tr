---
title: Azure Spring Cloud VNET 'i kendi kendine tanılama
description: VNET 'te çalışan Azure Spring Cloud 'daki sorunları kendi kendine tanılamayı ve çözmeyi öğrenin.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5407213b62902326d53b73e42ee3af1ba9b11524
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878774"
---
# <a name="self-diagnose-running-azure-spring-cloud-in-vnet"></a>VNET 'te Azure Spring Cloud çalıştıran kendi kendine tanılama
Azure yay bulutu Tanılaması, yapılandırma olmadan sanal ağlarda çalışan etkileşimli sorun giderme uygulamalarını destekler. Azure yay Bulut Tanılaması sorunları tanımlar ve sorun gidermeye ve çözmeye yardımcı olan bilgileri size rehberlik eder.

## <a name="navigate-to-the-diagnostics-page"></a>Tanılama sayfasına gidin
Aşağıdaki yordam, ağa bağlı uygulamalar için tanılamayı başlatır.
1. Azure portalında oturum açın.
1. Azure yay bulutuna genel bakış sayfasına gidin.
1. Sol gezinti bölmesindeki menüden **Tanıla ve sorunları çöz** ' ü seçin.
1. **Ağ oluşturma** üçüncü kategorisini seçin.

   ![Kendi kendine tanılama başlığı](media/spring-cloud-self-diagnose-vnet/self-diagostic-title.png)

## <a name="view-a-diagnostic-report"></a>Tanılama raporunu görüntüle
**Ağ** kategorisi ' ne tıkladıktan sonra, sanal ağ Ile Ilişkili Azure yay bulutuna özgü ağlarla ilgili iki sorunu görebilirsiniz: **DNS çözümü** ve **gerekli giden trafik**.

   ![Kendi kendine tanılama seçenekleri](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-req-outbound-options.png)

Tanılama raporunu görüntülemek için hedef sorununuzu seçin. Tanılama özeti görüntülenir, örneğin: 

* *Kaynak kaldırıldı.*
* *Kaynak kendi sanal ağınıza dağıtılmadı*.

Bazı sonuçlar ilgili belgeleri içerir. Farklı alt ağlar sonuçları ayrı olarak görüntüler.

## <a name="dns-resolution"></a>DNS çözümlemesi 
**DNS çözümlemesi**' ni seçerseniz, sonuçlar uygulamalarda DNS sorunları olup olmadığını gösterir.  Sağlıklı uygulamalar aşağıdaki gibi listelenmiştir:

* *DNS sorunları, ' subnet01 ' alt ağında hiçbir sorun olmadan çözüldü*.
* *DNS sorunları, ' subnet02 ' alt ağında hiçbir sorun olmadan çözüldü*.

Aşağıdaki tanılama raporu örneği, uygulamanın sistem durumunun bilinmediğini gösterir. Raporlama zaman dilimi, sistem durumunun bildirildiği saati içermez.  Bağlam bitiş saatinin *2021-03-03T04:20:00Z* olduğunu varsayın. **DNS çözümleme tablosu Işlemelerinde** en son zaman damgası *2021-03-03T03:39:00Z*, önceki gün. Engellenen bir ağ nedeniyle sistem durumu denetimi günlüğü gönderilmemiş olabilir. 

Bilinmeyen sistem durumu sonuçları ilgili belgeleri içerir.  Aşağı açılan ekranı görmek için sol açılı Köşeli Ayraca tıklayabilirsiniz.

   ![DNS bilinmiyor](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-unknown.png)

Özel DNS bölge kayıt kümesini yanlış yapılandırdıysanız, şöyle bir sonuç alacaksınız: `Failed to resolve the Private DNS in subnet xxx` . 

Açılan **DNS çözümleme tablosu Işleyicmelerinde** , yapılandırmadan emin olduğunuz ayrıntı iletisi bilgilerini bulacaksınız.

## <a name="required-outbound-traffic"></a>Gerekli giden trafik 

**Gerekli giden trafik**' i seçerseniz, sonuçlar uygulamalarda giden trafik sorunları olup olmadığını gösterir.  Sağlıklı uygulamalar aşağıdaki gibi listelenmiştir:

* * Gerekli giden trafik ' subnet01 ' alt ağında hiçbir sorun olmadan çözüldü.
* * Gerekli giden trafik ' subnet02 ' alt ağında hiçbir sorun olmadan çözüldü.

NSG veya güvenlik duvarı kuralları tarafından herhangi bir alt ağ engellenirse ve günlüğü engellediğiniz takdirde, aşağıdaki hatalarla ilgili bilgi bulacaksınız. [Müşteri sorumlulukları](spring-cloud-vnet-customer-responsibilities.md)olup olmadığınızı kontrol edebilirsiniz.
    
   ![Uç nokta başarısız oldu](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-failed.png)

`Required Outbound Traffic Table Renderings`30 dakika içinde bir veri yoksa, sonuç olur `health status unknown` . Ağınız engelleniyor veya günlük hizmeti kapalı olabilir.

   ![Tanılama uç noktası bilinmiyor](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-unknown.png)

## <a name="see-also"></a>Ayrıca bkz.
* [Azure yay bulutunu kendi kendine tanılama](spring-cloud-howto-self-diagnose-solve.md)