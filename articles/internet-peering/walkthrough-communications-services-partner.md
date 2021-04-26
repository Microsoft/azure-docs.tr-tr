---
title: Iletişim Hizmetleri için Azure Internet eşlemesi izlenecek yol
titleSuffix: Azure
description: Iletişim Hizmetleri için Azure Internet eşlemesi izlenecek yol
services: internet-peering
author: gthareja
ms.service: internet-peering
ms.topic: how-to
ms.date: 03/30/2021
ms.author: gatharej
ms.openlocfilehash: ff6750883a904ff5ddbddd3ddfd1ed82e72aebbc
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106499011"
---
# <a name="azure-internet-peering-for-communications-services-walkthrough"></a>Iletişim Hizmetleri için Azure Internet eşlemesi izlenecek yol

Bu bölümde, Microsoft ile doğrudan bağlantı kurmak için bir Iletişim Hizmetleri sağlayıcısının izlenmesi gereken adımlar açıklanmaktadır.

**Iletişim hizmetleri sağlayıcıları:**  İletişim hizmetleri sağlayıcıları, iletişim hizmetleri (Iletişimler, mesajlaşma, konferans vb.) sunan ve iletişim hizmetleri altyapısını (SBC/SIP Gateway vb.) bütünleştirmek isteyen kuruluşlardır.  Azure Iletişim Hizmetleri ve Microsoft ekipleri ile. 

Azure Internet eşlemesi, Microsoft ile tüm uç sitelerinde (pop konumları) doğrudan bağlantı kurmak için Iletişim Hizmetleri sağlayıcılarını destekler. Tüm ortak kenarlar listesi, [Peeringdb](https://www.peeringdb.com/net/694)'de bulunabilir.

Azure Internet eşlemesi, yüksek kaliteli ve performans merkezli hizmetleri sağlamak için Iletişim Hizmetleri için yüksek düzeyde güvenilir ve QoS (hizmet kalitesi) özellikli bir bağlantı sağlar.

## <a name="technical-requirements"></a>Teknik gereksinimler
Iletişim Hizmetleri için doğrudan bağlantı kurmaya yönelik teknik gereksinimler şunlardır:
-   Eş, ortak olması gereken kendi otonom sistem numarası (ASN) sağlamalıdır.
-   Eşin yerel artıklık sağlamak için her bir Interconnect konumunda yedekli bağlantısı (PNı) OLMALıDıR.
-   Bölgenin/Metro 'daki site hatalarının oluşması durumunda yük devretme sağlamak için eşin coğrafi artıklık olması gerekır.
-   Eşler yüksek kullanılabilirlik ve daha hızlı yakınsama sağlamak için BGP oturumlarına etkin-etkin olarak sahip OLMALıDıR ve birincil ve yedekleme olarak sağlanmamalıdır.
-   Eşin, eşdüzey eşleme yönlendiricileri için 1:1 oranının eşleme devrelerine sahip olması gerekir ve hiçbir hız sınırlaması uygulanmaz.
-   Eş, eşin iletişim hizmeti uç noktaları (ör. SBC) tarafından kullanılan kendi ortak yönlendirilebilir IPv4 adresi alanını sağlamalıdır ve duyurmalıdır. 
-   Eş, tanıtılan her alt ağda hangi trafik ve uç noktaların barındırdığı hakkında ayrıntılı bilgi SAĞLAMALıDıR. 
-   Eşin alt ikinci yol yakınsama işlemini kolaylaştırmak için çift yönlü Iletme algılaması (BFD) üzerinden BGP çalıştırması gerekır.
-   Tüm iletişim altyapısı önekleri Azure portal kaydedilir ve topluluk dizesi 8075:8007 ile tanıtıldığında.
-   Eş durum bilgisi olan bir güvenlik duvarı çalıştıran bir cihazda eşleme sonlandırılmamalıdır. 
-   Microsoft, tüm Interconnect bağlantılarını GECIKME (bağlantı demeti) olarak varsayılan olarak yapılandırır, bu nedenle eş bağlantı bağlantılarında, eşin LACP (bağlantı toplama Denetim Protokolü) desteklemesi gerekır.

## <a name="establishing-direct-interconnect-with-microsoft-for-communications-services"></a>Iletişim Hizmetleri için Microsoft ile doğrudan bağlantı kurma.

Azure Internet eşlemesini kullanarak doğrudan bağlantı kurmak için lütfen aşağıdaki adımları izleyin:

**1. eşdüzey genel ASN 'yi Azure aboneliğine ilişkilendirin:**

Büyük/küçük harf eşi, Azure aboneliğine zaten ortak ASN ile ilişkili, lütfen bu adımı yoksayın.

[Portalı kullanarak eşdüzey ASN 'yi Azure aboneliğine ilişkilendirme-Azure | Microsoft Docs](https://docs.microsoft.com/azure/internet-peering/howto-subscription-association-portal)

Sonraki adım, eşleme hizmeti için doğrudan eşleme bağlantısı oluşturmaktır.

> [!NOTE]
> ASN ilişkilendirmesi onaylandıktan sonra peeringservices@microsoft.com aboneliğinizi iletişim hizmetleriyle ilişkilendirmek IÇIN ASN ve ABONELIK Kimliğinizle bize e-posta gönderin. 

**2. eşleme hizmeti için doğrudan eşleme bağlantısı oluşturun:**

[Portalı kullanarak doğrudan eşleme oluşturmak veya değiştirmek](https://docs.microsoft.com/azure/internet-peering/howto-direct-portal) için yönergeleri izleyin

Yüksek kullanılabilirlik gereksinimini karşıladığından emin olun.

Lütfen "eşleme oluşturma" sayfasında aşağıdaki seçenekleri seçtiğinizden emin olun:

Eşleme türü:   **doğrudan**

Microsoft ağı:  **8075**

SKU:        **Premium ücretsiz**


"Doğrudan eşleme bağlantısı sayfası" altında aşağıdaki seçenekleri belirleyin:

Oturum adresi sağlayıcısı:   **Microsoft**

Eşleme Hizmetleri için kullanın:   **etkin**

> [!NOTE] 
> Eşleme Hizmetleri için etkinleştirme seçerken aşağıdaki iletiyi yoksayın.
> *HARITALAR sağlayıcısı hakkında iletişim kurmadığınız müddetçe etkinleştirmeyin peering@microsoft.com .*


  **2a. eşleme Hizmetleri için var olan doğrudan eşleme bağlantısını kullanın**

Eşleme hizmetini desteklemek için kullanmak istediğiniz mevcut bir doğrudan eşlemeye sahipseniz Azure portal etkinleştirebilirsiniz.
1.  [Portalı kullanarak eski bir doğrudan eşlemeyi Azure kaynağına dönüştürmek](https://docs.microsoft.com/azure/internet-peering/howto-legacy-direct-portal)için yönergeleri izleyin.
Gerektiğinde, yüksek kullanılabilirlik gereksinimini karşılamak için ek devreler sıralayın.

2.  Portalı kullanarak doğrudan eşleme üzerinde [eşleme hizmetini etkinleştirmek](https://docs.microsoft.com/azure/internet-peering/howto-peering-service-portal) için adımları izleyin.




**3. en Iyileştirilmiş yönlendirme için ön ekleri kaydedin**

Iletişim Hizmetleri altyapı önekleriniz için en iyi duruma getirilmiş yönlendirme için, tüm ön eklerinizi eşleme birbirine bağlı olarak kaydetmeniz gerekir.
[Azure eşleme hizmetini kaydetme-Azure portal | Microsoft Docs](https://docs.microsoft.com/azure/peering-service/azure-portal)

Ön ek anahtarı Iletişim hizmeti Iş ortakları için otomatik olarak doldurulur, bu nedenle iş ortağının kaydolmak için herhangi bir önek anahtarı kullanması gerekir. 

Lütfen kayıtlı ön eklerin, bölge için oluşturulan doğrudan bağlantılar üzerinden duyurulmakta olduğundan emin olun.


## <a name="faqs"></a>SSS:

**Ç.**  Iletişim Hizmetlerim için daha küçük alt ağlarım (</24). Daha küçük alt ağların de yönlendirilmesini sağlayabilir miyim?

**A.**  Evet, Microsoft Azure eşleme hizmeti aynı zamanda daha küçük ön ek yönlendirmeyi destekler. Lütfen yönlendirme için daha küçük ön ekleri kaydetdiğinizden ve aynı bağlantı, birbirine bağlı olarak duyurulduğu emin olun.

**Ç.**  Bu birbirine bağlı olarak hangi Microsoft rotaları alınır?

**A.** Microsoft, bu birbirine bağlı olarak Microsoft 'un tüm ortak hizmet öneklerini duyurur. Bu, yalnızca Iletişimden değil, ancak diğer bulut hizmetlerine aynı Interconnect 'ten erişilebildiğinden emin olur.

**Ç.**  Önek sınırını, Microsoft 'un duyurduğunu kaç tane rota ayarlayacağım?

**A.** Microsoft, Internet 'te kabaca 280 ön ek duyurur ve gelecekte% 10-15 oranında artabilir. Bu nedenle, 400-500 için güvenli bir limit, "en fazla önek sayısı" olarak ayarlamak iyi olabilir

**Ç.** Microsoft, eşdüzey ön ekleri Internet 'e yeniden duyuracaktır mi?

**A.** Hayır.

**Ç.** Bu hizmet için bir ücret var mı?

**A.** Hayır, ancak eşin site çapraz bağlantı maliyetlerini taşıması bekleniyor.

**Ç.** Bir bağlantı için en düşük bağlantı hızı nedir?

**A.** 10Gbps.

**Ç.** Eş bir SLA 'ya mi bağladınız?

**A.** Evet, kullanım %40 ' a ulaştığında 45-60 gün GECIKME genişletmede başlamalıdır.

**Ç.** Geçerli doğrudan eşleme veya Express Route üzerinden bu hizmetin avantajı nedir?

**A.** Ücretsiz kapatma ve yolun tamamı, Microsoft WAN üzerinden sesli trafik için en iyi duruma getirilmiştir ve yakınsama, BFD ile alt saniye için ayarlanmıştır.

**Ç.** Ekleme işleminin tamamlanması nasıl sürer?

**A.** Zaman, sitelerin sayısına ve konumlarına göre değişken olur ve eş var olan özel eşlemeleri geçiriyorsanız veya yeni kablolama kurarak saat olur. Taşıyıcı 3 + hafta için plan sağlamalıdır.

**Ç.** Ekleme için API 'Leri kullanabilir miyim?

**A.** Şu anda API desteği yoktur ve Web portalı aracılığıyla yapılandırma yapılmalıdır. 
