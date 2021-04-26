---
title: Uygulama proxy 'Si uygulamasını yapılandırma | Microsoft Docs
description: Birkaç basit adımda uygulama proxy 'Si uygulaması oluşturma ve yapılandırma hakkında bilgi edinin
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/18/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65f1488986dc38bba89a5b9a109a444b5f0d81f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255212"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Uygulama proxy 'Si uygulaması yapılandırma

Bu makale, şirket içi uygulamalarınızı buluta göstermek için Azure AD 'de bir uygulama proxy 'Si uygulamasının nasıl yapılandırılacağını anlamanıza yardımcı olur.

## <a name="recommended-documents"></a>Önerilen belgeler

Yönetici portalı aracılığıyla uygulama proxy 'Si uygulamasının ilk konfigürasyonları ve oluşturulması hakkında bilgi edinmek için [Azure AD uygulama ara sunucusu kullanarak uygulamaları yayımlama](application-proxy-add-on-premises-application.md)' yı izleyin.

Bağlayıcıları yapılandırma hakkında ayrıntılı bilgi için, bkz. [Azure Portal uygulama ara sunucusunu etkinleştirme](application-proxy-add-on-premises-application.md).

Sertifika yükleme ve özel etki alanlarını kullanma hakkında bilgi için bkz. [Azure AD uygulama ara sunucusu özel etki alanları Ile çalışma](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Uygulama oluşturma/URL 'yi ayarlama

[Uygulamaları Azure AD uygulama ara sunucusu kullanarak yayımla](application-proxy-add-on-premises-application.md) ve uygulamayı oluştururken bir hata alıyorsanız, uygulamayı nasıl düzelteceğinizi hakkında bilgi ve öneriler için hata ayrıntılarına bakın. Çoğu hata iletisi önerilen bir çözümü içerir. Yaygın hatalardan kaçınmak için şunları doğrulayın:

- Uygulama proxy 'Si uygulaması oluşturma izni olan bir yöneticiniz
- İç URL benzersizdir
- Dış URL benzersizdir
- URL 'Ler http veya https ile başlar ve bir "/" ile biter
- URL, bir IP adresi değil, bir etki alanı adı olmalıdır

Hata iletisi, uygulamayı oluştururken sağ üst köşede görüntülenmelidir. Hata iletilerini görmek için bildirim simgesini de seçebilirsiniz.

![Azure portal bildirim isteminin nerede bulunacağını gösterir](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Bağlayıcıları/bağlayıcı gruplarını yapılandırma

Bağlayıcılar ve bağlayıcı grupları hakkında uyarı nedeniyle uygulamanızı yapılandırırken güçlük çekiyorsanız, bağlayıcıları indirme hakkında ayrıntılar için bkz. uygulama ara sunucusunu etkinleştirme yönergeleri. Bağlayıcılar hakkında daha fazla bilgi edinmek istiyorsanız [Bağlayıcılar belgelerine](application-proxy-connectors.md)bakın.

Bağlayıcılar devre dışı ise, bu, hizmete ulaşamadıkları anlamına gelir. Bu, genellikle gerekli tüm bağlantı noktalarının açık olmaması nedeniyle oluşur. Gerekli bağlantı noktalarının listesini görmek için, uygulama proxy 'Si sağlama belgelerinin önkoşul bölümüne bakın.

## <a name="upload-certificates-for-custom-domains"></a>Özel etki alanları için sertifika yükleme

Özel etki alanları, dış URL 'nizin etki alanını belirtmenize olanak tanır. Özel etki alanlarını kullanmak için, bu etki alanı için sertifikayı karşıya yüklemeniz gerekir. Özel etki alanları ve Sertifikalar kullanma hakkında daha fazla bilgi için bkz. [Azure AD uygulama ara sunucusu özel etki alanları Ile çalışma](application-proxy-configure-custom-domain.md).

Sertifikanızı karşıya yüklerken sorunlarla karşılaşdıysanız, sertifikadaki sorun hakkında daha fazla bilgi için portaldaki hata iletilerine bakın. Ortak sertifika sorunları şunlardır:

- Süre dolma sertifikası
- Sertifika kendinden imzalı
- Sertifikada özel anahtar eksik

Hata iletisi, sertifikayı karşıya yüklemeye çalıştığınızda sağ üst köşede görüntülenir. Hata iletilerini görmek için bildirim simgesini de seçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD Uygulama Ara Sunucusu ile uygulama yayımlama](application-proxy-add-on-premises-application.md)
