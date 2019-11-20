---
title: 'Azure AD Uygulama Ara Sunucusu: Sürüm sürümü geçmişi | Microsoft Docs'
description: Bu makalede tüm Azure AD Uygulama Ara Sunucusu sürümleri listelenir ve yeni özellikler ve düzeltilen sorunlar açıklanmaktadır
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9ecd193282ed9b7333df44689530b4d057ad7f8
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693894"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD Uygulama Ara Sunucusu: Sürüm yayınlama geçmişi
Bu makalede, yayımlanan Azure Active Directory (Azure AD) uygulama proxy 'sinin sürümleri ve özellikleri listelenmektedir. Azure AD ekibi, uygulama ara sunucusunu düzenli olarak yeni özellikler ve işlevlerle güncelleştirir. Uygulama proxy bağlayıcıları, yeni bir sürüm yayınlandığında otomatik olarak güncelleştirilir. 

En son özelliklere ve hata düzeltmelerine sahip olduğunuzdan emin olmak için otomatik güncelleştirmelerin bağlayıcılarınız için etkinleştirildiğinden emin olmanızı öneririz. Microsoft, önceki sürüm bağlayıcı sürümü ve bir sürümü için doğrudan destek sağlar.

İlgili kaynakların listesi aşağıda verilmiştir:

Resource |  Ayrıntılar
--------- | --------- |
Uygulama proxy 'Sini etkinleştirme | Uygulama proxy 'Sini etkinleştirme ve bir bağlayıcıyı yükleme ve kaydetme önkoşulları, bu [öğreticide](application-proxy-add-on-premises-application.md)açıklanmıştır.
Azure AD uygulama ara sunucusu bağlayıcıları anlama | [Bağlayıcı yönetimi](application-proxy-connectors.md) ve bağlayıcılar [otomatik yükseltme](application-proxy-connectors.md#automatic-updates)hakkında daha fazla bilgi edinin.
Azure AD Uygulama Ara Sunucusu Bağlayıcısı Indirme |  [En son bağlayıcıyı indirin](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Yayın durumu

20 Eylül 2018: İndirilmek üzere yayınlandı

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler

- QlikSense uygulaması için WebSocket desteği eklendi. Uygulama proxy 'Si ile QlikSense tümleştirme hakkında daha fazla bilgi edinmek için bu [izlenecek yolu](application-proxy-qlik.md)inceleyin. 
- Bir giden proxy 'nin yapılandırmasını kolaylaştırmak için Yükleme Sihirbazı geliştirildi. 
- Bağlayıcılar için varsayılan protokol olan TLS 1,2 ' i ayarlayın. 
- Yeni bir Son Kullanıcı Lisans Sözleşmesi (EULA) eklendi.  

### <a name="fixed-issues"></a>Düzeltilen sorunlar

- Bağlayıcının bazı bellek sızıntılarına neden olan bir hata düzeltildi.
- Bağlayıcı zaman aşımı sorunları için hata düzeltmesini içeren Azure Service Bus sürümü güncelleştirildi.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Yayın durumu

19 Ocak 2018: İndirilmek üzere yayınlandı

### <a name="fixed-issues"></a>Düzeltilen sorunlar

- Tanımlama bilgisinde etki alanı çevirisi gerektiren özel etki alanları için destek eklendi.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Yayın durumu 

25 Mayıs 2017: İndirilmek üzere yayınlandı 

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler 

Bağlayıcıların giden bağlantı sınırları üzerinde geliştirilmiş denetim. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Yayın durumu

15 Nisan 2017: İndirilmek üzere yayınlandı

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler

- Daha az gerekli bağlantı noktalarıyla basit ekleme ve yönetme. Uygulama proxy 'Si artık yalnızca iki standart giden bağlantı noktasının açılmasını gerektirir: 443 ve 80. Uygulama proxy 'Si yalnızca giden bağlantıları kullanmaya devam eder, bu nedenle yine de bir DMZ içinde herhangi bir bileşene ihtiyacınız yoktur. Ayrıntılar için bkz. [yapılandırma Belgelerimiz](application-proxy-add-on-premises-application.md).  
- Dış proxy 'niz veya güvenlik duvarınız tarafından destekleniyorsa, artık ağınızı IP aralığı yerine DNS ile açabilirsiniz. Uygulama Proxy hizmetleri, yalnızca *. msappproxy.net ve *. servicebus.windows.net bağlantıları gerektirir.


## <a name="earlier-versions"></a>Önceki sürümler

1\.5.36.0 ' den önceki bir uygulama proxy Bağlayıcısı sürümü kullanıyorsanız, en son tam olarak desteklenen özelliklere sahip olduğunuzdan emin olmak için en son sürüme güncelleştirin.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure AD uygulama ara sunucusu aracılığıyla şirket içi uygulamalara uzaktan erişim](application-proxy.md)hakkında daha fazla bilgi edinin.
- Uygulama proxy 'sini kullanmaya başlamak için bkz [. Öğretici: Uygulama proxy 'Si](application-proxy-add-on-premises-application.md)aracılığıyla uzaktan erişim için şirket içi bir uygulama ekleyin.
