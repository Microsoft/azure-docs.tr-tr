---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760657"
---
Bu makalede, yaygın oturum açma hatalarını işlemeye yönelik farklı hata türleri ve önerilerin bir özeti verilmektedir.

## <a name="msal-error-handling-basics"></a>MSAL hata işleme temelleri

Microsoft kimlik doğrulama kitaplığı 'ndaki (MSAL) özel durumlar, uygulama geliştiricilerinin son kullanıcılara görüntüleme için değil, sorun gidermesi için tasarlanmıştır. Özel durum iletileri yerelleştirilmemiş.

Özel durumları ve hataları işlerken, özel durum türünü ve hata kodunu kullanarak özel durumlar arasında ayrım yapabilirsiniz.  Hata kodlarının bir listesi için bkz. [Azure AD kimlik doğrulaması ve yetkilendirme hata kodları](../articles/active-directory/develop/reference-aadsts-error-codes.md).

Oturum açma deneyimi sırasında, yarışmalar, koşullu erişim (MFA, cihaz yönetimi, konum tabanlı kısıtlamalar), belirteç verme ve teminat ve Kullanıcı özellikleri hakkında hatalarla karşılaşabilirsiniz.

Aşağıdaki bölümde, uygulamanız için hata işleme hakkında daha fazla ayrıntı sağlanmaktadır.