---
title: Uygulama proxy'si uygulaması yapılandırma | Microsoft Docs
description: Oluşturun ve birkaç basit adımda bir uygulama proxy'si uygulaması yapılandırma hakkında bilgi edinin
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7aaf2eb282bc3fd0b9f3853ce493c479a3d3c3a9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807849"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Uygulama proxy'si uygulaması yapılandırma

Bu makalede, şirket içi uygulamaları bulutta kullanıma sunmak için bir uygulama proxy'si uygulamasını Azure AD'ye nasıl anlamanıza yardımcı olur.

## <a name="recommended-documents"></a>Önerilen belgeler

İlk Yapılandırma ve Yönetim Portalı üzerinden bir uygulama proxy'si uygulaması oluşturma hakkında bilgi edinmek için izleyin [Azure AD uygulama ara sunucusu kullanarak uygulama yayımlama](application-proxy-add-on-premises-application.md).

Bağlayıcı yapılandırma hakkında daha fazla bilgi için bkz [Azure portalında uygulama ara sunucusunu etkinleştirme](application-proxy-add-on-premises-application.md).

Sertifikaları karşıya yükleme ve özel etki alanlarını kullanma hakkında daha fazla bilgi için bkz: [Azure AD uygulama proxy'sinde özel etki alanları ile çalışma](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Uygulama/ayarı URL'ler oluşturma

Adımları takip ediyorsanız [Azure AD uygulama ara sunucusu kullanarak uygulama yayımlama](application-proxy-add-on-premises-application.md) belgeleri ve bu bilgi için hata ayrıntılarına ve düzeltmeye ilişkin öneriler görürsünüz alınırken bir hata uygulaması oluşturma uygulama. Çoğu hata iletileri, önerilen düzeltmeyi içerir. Sık karşılaşılan hataları önlemek için aşağıdakileri doğrulayın:

- Uygulama proxy'si uygulaması oluşturmak için izne sahip bir yöneticisiniz
- İç URL benzersizdir
- Dış URL benzersizdir
- URL'leri http veya https ile başlayamaz ve bitemez bir "/"
- URL bir etki alanı adı veya IP adresi olmalıdır

Uygulamayı oluştururken sağ üst köşedeki hata iletisi görüntülenmelidir. Hata iletilerini görmek için bildirim simgesini de seçebilirsiniz.

![Uyarı istemi, Azure portalında nerede bulacağını gösterir](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Bağlayıcıların/bağlayıcı gruplarını yapılandırma

Bağlayıcı grupları ve bağlayıcılar hakkında uyarı nedeniyle uygulamanızı yapılandırma ile ilgili sorunlar yaşıyorsanız, bağlayıcı indirme hakkında ayrıntılar için uygulama proxy'sini etkinleştirme hakkında yönergeler bakın. Bağlayıcılar hakkında daha fazla bilgi edinmek istiyorsanız bkz [bağlayıcılar belgelerine](application-proxy-connectors.md).

Bağlayıcılarınızı devre dışı ise bu hizmete bağlanamıyoruz anlamına gelir. Bu durum, genellikle tüm gereken bağlantı noktalarını açık olmadığından olur. Gerekli bağlantı noktalarının bir listesini görmek için etkinleştirme uygulama proxy'si belgelerin Önkoşullar bölümüne bakın.

## <a name="upload-certificates-for-custom-domains"></a>Özel etki alanları için sertifikaları karşıya yükle

Özel etki alanlarına etki alanı, dış URL'leri belirtmenizi sağlar. Özel etki alanlarını kullanmak için bu etki alanı için sertifika karşıya gerekir. Özel etki alanları ve sertifikalar hakkında daha fazla bilgi için bkz: [Azure AD uygulama proxy'sinde özel etki alanları ile çalışma](application-proxy-configure-custom-domain.md).

Sertifikanızı karşıya yüklemeyi sorunlarla karşılaşıyorsanız, sertifikayla ilgili bir sorun hakkında daha fazla bilgi için portaldaki hata iletilerini arayın. Ortak sertifika sorunları şunlardır:

- Süresi dolan sertifika
- Otomatik olarak imzalanan sertifika
- Sertifika özel anahtar eksik

Sertifikayı karşıya yüklemek çalışırken sağ üst köşedeki hata iletisini görüntüler. Hata iletilerini görmek için bildirim simgesini de seçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD uygulama ara sunucusu kullanarak uygulama yayımlama](application-proxy-add-on-premises-application.md)
