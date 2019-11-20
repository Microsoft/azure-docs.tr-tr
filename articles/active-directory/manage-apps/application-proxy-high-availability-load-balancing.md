---
title: Azure AD Uygulama Ara Sunucusu için yüksek kullanılabilirlik ve yük dengelemesi | Microsoft Docs
description: Trafik dağıtımı, uygulama proxy dağıtımıyla birlikte nasıl kullanılır. Bağlayıcı performansını iyileştirme ve arka uç sunucuları için yük dengelemeyi kullanma ipuçları içerir.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 014fcf37930800858cd70f15c19e3f494d3f3776
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169798"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Uygulama proxy bağlayıcılarınızın ve uygulamalarınızın yüksek kullanılabilirliği ve yük dengelemesi

Bu makalede, trafik dağıtımının uygulama proxy dağıtımıyla nasıl çalıştığı açıklanmaktadır. Şunları tartışacağız:

- Trafiğin kullanıcılar ve bağlayıcılar arasında dağıtılması ve bağlayıcı performansını iyileştirmeye yönelik ipuçları

- Birden çok arka uç sunucusu arasında yük dengelemeye yönelik önerilerle, bağlayıcılar ve arka uç uygulama sunucuları arasında trafik nasıl akar

## <a name="traffic-distribution-across-connectors"></a>Bağlayıcılar genelinde trafik dağıtımı

Bağlayıcılar, yüksek kullanılabilirlik ilkelerine bağlı olarak bağlantılarını oluştururlar. Trafiğin her zaman bağlayıcılar arasında eşit olarak dağıtılması ve oturum benzeşimi olmaması garanti yoktur. Bununla birlikte, kullanım değişiklik gösterir ve istekler uygulama proxy 'Si hizmet örneklerine rastgele gönderilir. Sonuç olarak, trafik genellikle bağlayıcılar arasında neredeyse eşit olarak dağıtılır. Aşağıdaki diyagramda ve adımlarda, kullanıcılar ve bağlayıcılar arasında bağlantıların nasıl kurulduğu gösterilmektedir.

![Kullanıcılar ve bağlayıcılar arasındaki bağlantıları gösteren diyagram](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. İstemci cihazındaki bir Kullanıcı, uygulama proxy 'Si aracılığıyla yayınlanan şirket içi bir uygulamaya erişmeye çalışır.
2. İstek, hangi uygulama proxy hizmeti örneğinin istek için gerekli olacağını belirleyen bir Azure Load Balancer geçer. Her bölge için, isteği kabul etmek için kullanılabilecek onlarca örnek vardır. Bu yöntem, trafiği hizmet örnekleri genelinde eşit bir şekilde dağıtmaya yardımcı olur.
3. İstek [Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/)gönderilir.
4. Service Bus, bağlantının bağlayıcı grubunda mevcut bir bağlayıcıyı daha önce kullanmış olup olmadığını denetler. Bu durumda, bağlantıyı yeniden kullanır. Henüz bağlantıyla eşleştirilmiş bir bağlayıcı yoksa, sinyali almak için rastgele olan kullanılabilir bağlayıcıyı seçer. Bağlayıcı daha sonra Service Bus isteği alır.

   - 2\. adımda istekler farklı uygulama proxy hizmeti örneklerine gider, bu nedenle bağlantıların farklı bağlayıcılarla yapılması daha olasıdır. Sonuç olarak, bağlayıcılar grup içinde neredeyse eşit olarak kullanılır.

   - Bağlantı yalnızca bağlantı kesildiğinde veya 10 dakikalık bir boşta kalma süresi oluşursa yeniden kullanılabilir. Örneğin, bir makine veya bağlayıcı hizmeti yeniden başlatıldığında bağlantı kopmuş olabilir veya bir ağ kesintisi olur.

5. Bağlayıcı, isteği uygulamanın arka uç sunucusuna geçirir. Ardından uygulama, yanıtı bağlayıcıya geri gönderir.
6. Bağlayıcı, isteğin geldiği hizmet örneğine giden bir bağlantı açarak yanıtı tamamlar. Bu bağlantı hemen kapatılır. Varsayılan olarak, her bağlayıcı 200 eşzamanlı giden bağlantı ile sınırlıdır.
7. Yanıt daha sonra hizmet örneğinden istemciye geri geçirilir.
8. Aynı bağlantıdan gelen sonraki istekler, bu bağlantı kesilene veya 10 dakika boşta kaldığında yukarıdaki adımları tekrarlayın.

Uygulama genellikle birçok kaynağa sahiptir ve yüklendiğinde birden çok bağlantı açar. Her bağlantı, bir hizmet örneğine ayrılmek için yukarıdaki adımlardan geçer, bağlantı henüz bir bağlayıcıya eşlenmemişse yeni bir kullanılabilir bağlayıcı seçin.


## <a name="best-practices-for-high-availability-of-connectors"></a>Bağlayıcıların yüksek kullanılabilirliği için en iyi uygulamalar

- Trafiğin yüksek kullanılabilirlik için bağlayıcılar arasında dağıtıldığı şekilde, bir bağlayıcı grubunda her zaman en az iki bağlayıcı olması önemlidir. Bağlayıcılar arasında ek arabellek sağlamak için üç bağlayıcı tercih edilir. Gereken bağlayıcı sayısını öğrenmek için kapasite planlama belgelerini izleyin.

- Tek bir hata noktası oluşmasını önlemek için bağlayıcıları farklı giden bağlantılara yerleştirin. Bağlayıcılar aynı giden bağlantıyı kullanıyorsa, bağlantıyla ilgili bir ağ sorunu, kendisini kullanan tüm bağlayıcıları etkileyebilir.

- Üretim uygulamalarına bağlıyken bağlayıcıları yeniden başlamaya zormaktan kaçının. Bu işlem, trafiğin bağlayıcılar arasında dağıtılmasını olumsuz yönde etkileyebilir. Bağlayıcıları yeniden başlatmak, daha fazla bağlayıcının kullanılamamasına neden olur ve kullanılabilir kalan bağlayıcıya bağlantıları zorlar. Sonuç olarak, ilk olarak bağlayıcıların düzensiz bir kullanımı.

- Bağlayıcılar ve Azure arasındaki giden TLS iletişimlerinde tüm satır içi denetleme biçimlerinden kaçının. Bu tür satır içi denetleme iletişim akışına düşüşe neden olur.

- Bağlayıcılarınız için otomatik güncelleştirmeleri çalıştırmaya devam ettiğinizden emin olun. Uygulama proxy 'Si Bağlayıcı Güncelleştiricisi hizmeti çalışıyorsa, bağlayıcılarınız otomatik olarak güncelleştirir ve en son yükseltilen sürümü alır. Sunucunuzda Bağlayıcı Güncelleştiricisi hizmetini görmüyorsanız, tüm güncelleştirmeleri almak için bağlayıcınızı yeniden yüklemeniz gerekir.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Bağlayıcılar ve arka uç uygulama sunucuları arasındaki trafik akışı

Yüksek kullanılabilirliğe sahip olan başka bir anahtar alanı, bağlayıcılar ve arka uç sunucuları arasındaki bağlantıdır. Bir uygulama Azure AD Uygulama Ara Sunucusu aracılığıyla yayımlandığında, kullanıcılardan uygulamalara olan trafik üç atlama üzerinden akar:

1. Kullanıcı Azure 'da Azure AD Uygulama Ara Sunucusu hizmeti genel uç noktasına bağlanır. Bağlantı, istemcinin kaynak istemci IP adresi (genel) ve uygulama proxy uç noktasının IP adresi arasında oluşturulur.
2. Uygulama proxy Bağlayıcısı, istemcinin HTTP isteğini uygulama proxy hizmetinden çeker.
3. Uygulama proxy Bağlayıcısı, hedef uygulamaya bağlanır. Bağlayıcı, bağlantıyı kurmak için kendi IP adresini kullanır.

![Uygulama proxy 'Si aracılığıyla bir uygulamaya bağlanan kullanıcı Diyagramı](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-Iletilmiş-üst bilgi alanı konuları
Bazı durumlarda (denetim, Yük Dengeleme vb. gibi), dış istemcinin kaynak IP adresini şirket içi ortamla paylaşmak bir gereksinimdir. Azure AD Uygulama Ara Sunucusu Bağlayıcısı, gereksinimi karşılamak için, HTTP isteğine kaynak istemci IP adresi (genel) içeren X-Iletilen üst bilgi alanını ekler. Uygun ağ aygıtı (yük dengeleyici, güvenlik duvarı) veya Web sunucusu ya da arka uç uygulaması daha sonra bilgileri okuyabilir ve kullanabilir.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Birden çok uygulama sunucusu arasında yük dengeleme için en iyi uygulamalar
Uygulama proxy 'Si uygulamasına atanan bağlayıcı grubunun iki veya daha fazla bağlayıcısı varsa ve arka uç Web uygulamasını birden çok sunucuda (sunucu grubu) çalıştırıyorsanız, iyi bir yük dengeleme stratejisi gerekir. İyi bir strateji, sunucuların istemci isteklerini eşit olarak seçmesini ve sunucu grubundaki sunucuların aşırı veya daha fazla kullanımını engeller.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>Senaryo 1: arka uç uygulaması, oturum kalıcılığı gerektirmez
En basit senaryo, arka uç Web uygulamasının oturum sürekliliği (oturum kalıcılığı) gerektirmez. Kullanıcıdan gelen herhangi bir istek, sunucu grubundaki herhangi bir arka uç uygulama örneği tarafından işlenebilir. Bir katman 4 yük dengeleyici kullanabilir ve bunu hiçbir benzeşim olmadan yapılandırabilirsiniz. Bazı seçenekler, Microsoft Ağ Yükü Dengeleme ve Azure Load Balancer veya başka bir satıcıdan yük dengeleyici içerir. Alternatif olarak, hepsini bir kez deneme DNS yapılandırılabilir.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>Senaryo 2: arka uç uygulaması, oturum kalıcılığı gerektiriyor
Bu senaryoda, arka uç Web uygulaması kimliği doğrulanmış oturum sırasında oturum sürekliliği (oturum kalıcılığı) gerektirir. Kullanıcının tüm isteklerinin sunucu grubundaki aynı sunucuda çalışan arka uç uygulama örneği tarafından işlenmesi gerekir.
Bu senaryo daha karmaşık olabilir çünkü istemci genellikle uygulama proxy 'Si hizmetine birden çok bağlantı kurar. Farklı bağlantı istekleri gruptaki farklı bağlayıcılara ve sunuculara gelebilir. Her bağlayıcı bu iletişim için kendi IP adresini kullandığından, yük dengeleyici bağlayıcının IP adresini temel alarak oturumun sürekliliğini güvence altına alamazsınız. Kaynak IP benzeşimi, her ikisi de kullanılamaz.
Senaryo 2 için bazı seçenekler şunlardır:

- 1\. seçenek: oturum kalıcılığını yük dengeleyici tarafından ayarlanan bir oturum tanımlama bilgisine dayandırın. Bu seçenek, yükün arka uç sunucuları arasında eşit olarak yayılmasını sağladığından önerilir. Bu özelliğe sahip bir katman 7 Yük Dengeleyici gerektirir ve HTTP trafiğini işleyebilir ve SSL bağlantısını sonlandırabilirler. Azure Application Gateway (oturum benzeşimi) veya başka bir satıcıdan yük dengeleyici kullanabilirsiniz.

- 2\. seçenek: oturum kalıcılığını X-Iletilmiş-for üst bilgisinde temel alan. Bu seçenek, bu özelliğe sahip bir katman 7 Yük Dengeleyici gerektirir ve HTTP trafiğini işleyebilir ve SSL bağlantısını sonlandırabilirler.  

- Seçenek 3: arka uç uygulamasını, oturum kalıcılığı gerektirecek şekilde yapılandırın.

Arka uç uygulamasının Yük Dengeleme gereksinimlerini anlamak için Yazılım satıcınızın belgelerine bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulama proxy 'Sini etkinleştir](application-proxy-add-on-premises-application.md)
- [Çoklu oturum açmayı etkinleştirme](application-proxy-configure-single-sign-on-with-kcd.md)
- [Koşullu erişimi etkinleştir](application-proxy-integrate-with-sharepoint-server.md)
- [Uygulama proxy 'Si ile karşılaştığınız sorunları giderme](application-proxy-troubleshoot.md)
- [Azure AD mimarisinin yüksek kullanılabilirliği nasıl desteklediğini öğrenin](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)
