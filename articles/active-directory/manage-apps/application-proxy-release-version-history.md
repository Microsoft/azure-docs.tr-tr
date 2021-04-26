---
title: 'Azure Active Directory Uygulama Ara Sunucusu: sürüm sürümü geçmişi'
description: Bu makalede tüm Azure Active Directory Uygulama Ara Sunucusu sürümleri listelenir ve yeni özellikler ve düzeltilen sorunlar açıklanmaktadır.
services: active-directory
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 02/17/2021
ms.subservice: app-mgmt
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 0244e0ca66f452649d3136eb122d1c2666182ae2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200978"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD Uygulama Ara Sunucusu: sürüm sürümü geçmişi
Bu makalede, yayımlanan Azure Active Directory (Azure AD) uygulama proxy 'sinin sürümleri ve özellikleri listelenmektedir. Azure AD ekibi, uygulama ara sunucusunu düzenli olarak yeni özellikler ve işlevlerle güncelleştirir. Uygulama proxy bağlayıcıları, yeni bir sürüm yayınlandığında otomatik olarak güncelleştirilir. 

En son özelliklere ve hata düzeltmelerine sahip olduğunuzdan emin olmak için otomatik güncelleştirmelerin bağlayıcılarınız için etkinleştirildiğinden emin olmanızı öneririz. Microsoft, en son bağlayıcı sürümü ve bir sürümü için doğrudan destek sağlar.

İlgili kaynakların listesi aşağıda verilmiştir:

| Kaynak                                         | Ayrıntılar                                                      |
| ------------------------------------------------ | ------------------------------------------------------------ |
| Uygulama proxy 'Sini etkinleştirme                  | Uygulama proxy 'Sini etkinleştirme ve bir bağlayıcıyı yükleme ve kaydetme önkoşulları, bu [öğreticide](application-proxy-add-on-premises-application.md)açıklanmıştır. |
| Azure AD Uygulama Ara Sunucusu bağlayıcılarını anlama | [Bağlayıcı yönetimi](application-proxy-connectors.md) ve bağlayıcılar [otomatik yükseltme](application-proxy-connectors.md#automatic-updates)hakkında daha fazla bilgi edinin. |
| Azure AD Uygulama Ara Sunucusu Bağlayıcısı Indirme    | [En son bağlayıcıyı indirin](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download). |

## <a name="1519750"></a>1.5.1975.0

### <a name="release-status"></a>Yayın durumu

22 Temmuz 2020: bu sürümü indirmek için yayımlanmıştır yalnızca indirme sayfası aracılığıyla yüklenebilir. Bu sürümün otomatik yükseltme sürümü daha sonra yayımlanacak.

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler
-   Azure Kamu bulut ortamları için geliştirilmiş destek. Azure Kamu Bulutu için bağlayıcının düzgün şekilde yüklenmesiyle ilgili adımlar için [önkoşulları](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) ve [yükleme adımlarını](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud)gözden geçirin.
- Uygulama proxy 'Si ile Uzak Masaüstü Hizmetleri Web istemcisinin kullanılmasına yönelik destek. Daha fazla bilgi için bkz. [Azure AD uygulama ara sunucusu Uzak Masaüstü yayımlama](application-proxy-integrate-with-remote-desktop-services.md) .
- İyileştirilmiş WebSocket uzantı anlaşmaları. 
- Bölgeye göre bağlayıcı grupları ve uygulama proxy 'Si bulut hizmetleri arasında iyileştirilmiş yönlendirme desteği. Daha fazla ayrıntı için bkz. [Azure Active Directory uygulama ara sunucusu trafik akışını iyileştirme](application-proxy-network-topology.md) . 

### <a name="fixed-issues"></a>Düzeltilen sorunlar
- Küçük harfli dizeler zorlayan bir WebSocket sorunu düzeltildi.
- Bağlayıcıların zaman zaman yanıt vermemesine neden olan bir sorun düzeltildi.

## <a name="1516260"></a>1.5.1626.0

### <a name="release-status"></a>Yayın durumu

17 Temmuz 2020: indirme için yayınlandı. Bu sürüm, yükleme için yalnızca indirme sayfası aracılığıyla kullanılabilir. Bu sürümün otomatik yükseltme sürümü daha sonra yayımlanacak.

### <a name="fixed-issues"></a>Düzeltilen sorunlar
- Önceki sürümde bulunan bellek sızıntısı sorunu çözüldü
- WebSocket desteği için genel iyileştirmeler

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>Yayın durumu

07 Nisan 2020: bu sürümü indirmek için yayımlandı yalnızca indirme sayfası aracılığıyla yüklenebilir. Bu sürümün otomatik yükseltme sürümü daha sonra yayımlanacak.

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler
-   Bağlayıcılar yalnızca tüm bağlantılar için TLS 1,2 kullanır. Daha fazla ayrıntı için bkz. [bağlayıcı önkoşulları](application-proxy-add-on-premises-application.md#prerequisites) .
- Bağlayıcı ile Azure hizmetleri arasında iyileştirilmiş sinyal. Bu, bağlayıcı ile Azure hizmetleri arasındaki WCF iletişimine yönelik güvenilir oturumları ve WebSocket iletişimleri için DNS önbelleğe alma geliştirmelerini destekler.
- Bağlayıcı ile arka uç uygulaması arasında bir ara sunucu yapılandırma desteği. Daha fazla bilgi için bkz. [mevcut şirket içi proxy sunucularıyla çalışma](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="fixed-issues"></a>Düzeltilen sorunlar
- Bağlayıcıdan Azure hizmetlerine yönelik iletişimler için 8080 numaralı bağlantı noktasına geri dönülüyor.
- WebSocket iletişimleri için hata ayıklama izlemeleri eklendi. 
- Arka uç uygulama tanımlama bilgilerinde ayarlandığında, SameSite özniteliği koruma çözüldü.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Yayın durumu

20 Eylül 2018: indirme için yayınlandı

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler

- QlikSense uygulaması için WebSocket desteği eklendi. Uygulama proxy 'Si ile QlikSense tümleştirme hakkında daha fazla bilgi edinmek için bu [izlenecek yolu](application-proxy-qlik.md)inceleyin. 
- Bir giden proxy 'nin yapılandırmasını kolaylaştırmak için Yükleme Sihirbazı geliştirildi. 
- Bağlayıcılar için varsayılan protokol olan TLS 1,2 ' i ayarlayın. 
- Yeni bir End-User Lisans Sözleşmesi (EULA) eklendi.  

### <a name="fixed-issues"></a>Düzeltilen sorunlar

- Bağlayıcının bazı bellek sızıntılarına neden olan bir hata düzeltildi.
- Bağlayıcı zaman aşımı sorunları için hata düzeltmesini içeren Azure Service Bus sürümü güncelleştirildi.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Yayın durumu

19 Ocak 2018: indirme için yayınlandı

### <a name="fixed-issues"></a>Düzeltilen sorunlar

- Tanımlama bilgisinde etki alanı çevirisi gerektiren özel etki alanları için destek eklendi.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Yayın durumu 

25 Mayıs 2017: indirme için yayınlandı 

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler 

Bağlayıcıların giden bağlantı sınırları üzerinde geliştirilmiş denetim. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Yayın durumu

15 Nisan 2017: indirme için yayımlandı

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler

- Daha az gerekli bağlantı noktalarıyla basit ekleme ve yönetme. Uygulama proxy 'Si artık yalnızca iki standart giden bağlantı noktasının açılmasını gerektirir: 443 ve 80. Uygulama proxy 'Si yalnızca giden bağlantıları kullanmaya devam eder, bu nedenle yine de bir DMZ içinde herhangi bir bileşene ihtiyacınız yoktur. Ayrıntılar için bkz. [yapılandırma Belgelerimiz](application-proxy-add-on-premises-application.md).  
- Dış proxy 'niz veya güvenlik duvarınız tarafından destekleniyorsa, artık ağınızı IP aralığı yerine DNS ile açabilirsiniz. Uygulama Proxy hizmetleri, yalnızca *. msappproxy.net ve *. servicebus.windows.net bağlantıları gerektirir.


## <a name="earlier-versions"></a>Önceki sürümler

1.5.36.0 ' den önceki bir uygulama proxy Bağlayıcısı sürümü kullanıyorsanız, en son tam olarak desteklenen özelliklere sahip olduğunuzdan emin olmak için en son sürüme güncelleştirin.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure AD uygulama ara sunucusu aracılığıyla şirket içi uygulamalara uzaktan erişim](application-proxy.md)hakkında daha fazla bilgi edinin.
- Uygulama proxy 'Sini kullanmaya başlamak için bkz. [öğretici: uygulama proxy 'si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md).
