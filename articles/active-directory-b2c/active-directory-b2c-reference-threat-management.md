---
title: Azure Active Directory B2C kaynakları ve verileri tehditlere karşı yönetme
description: Azure Active Directory B2C hizmet reddi saldırıları ve parola saldırıları için algılama ve risk azaltma teknikleri hakkında bilgi edinin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: eae82fbd56782faf5b15479c13fe530e189a0e3e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256899"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Azure Active Directory B2C kaynakları ve verileri tehditlere karşı yönetme

Azure Active Directory B2C (Azure AD B2C), kaynaklarınız ve verileriniz için tehditlere karşı korumanıza yardımcı olabilecek yerleşik özelliklere sahiptir. Bu tehditler, hizmet reddi saldırıları ve parola saldırıları içerir. Hizmet reddi saldırıları, kaynakları amaçlanan kullanıcılar için kullanılamaz hale getirir. Parola saldırıları kaynaklara yetkisiz erişime yol açabilir.

## <a name="denial-of-service-attacks"></a>Hizmet reddi saldırıları

SYN tanımlama bilgilerini kullanarak SYN savunma saldırılarına karşı savunma Azure AD B2C. Azure AD B2C Ayrıca ücretler ve bağlantılar için sınırları kullanarak hizmet reddi saldırılarına karşı koruma sağlar.

## <a name="password-attacks"></a>Parola saldırıları

Kullanıcılar tarafından ayarlanan parolaların makul bir şekilde karmaşık olması gerekir. Azure AD B2C, parola saldırıları için yerinde risk azaltma tekniklerini içerir. Hafifletme, deneme yanılma, parola saldırılarını ve sözlük parola saldırılarını algılamayı içerir. Çeşitli sinyalleri kullanarak isteklerin bütünlüğünü analiz Azure AD B2C. Azure AD B2C, Kullanıcı korsanlarından ve botlardan hedeflenen kullanıcıları akıllıca ayırt etmek için tasarlanmıştır.

Azure AD B2C, hesapları kilitlemek için gelişmiş bir strateji kullanır. Hesaplar, isteğin IP 'si ve girilen parolalar temelinde kilitlenir. Kilitleme süresi de bir saldırı olma olasılığını temel alarak artar. Parolanın süresi 10 kez denendikten sonra (varsayılan deneme eşiği), tek dakikalık bir kilitleme meydana gelir. Hesabın kilidi açıldıktan sonra bir sonraki oturum açma işlemi başarısız olursa (yani, kilitleme süresi dolduktan sonra hesap hizmet tarafından otomatik olarak açıldıktan sonra), bir dakikalık kilit oluşur ve her başarısız oturum açma işlemi için devam eder. Aynı parolanın tekrar tekrar girilmesi birden çok başarısız oturum açma sayısı olarak sayılır.

İlk 10 kilitleme dönemi bir dakika uzunluğundadır. Sonraki 10 kilitleme dönemi biraz daha uzundur ve her 10 kilitleme süresinden sonra süre içinde artar. Hesap kilitlenmediğinde, başarılı bir oturum açma işleminden sonra kilitleme sayacı sıfıra sıfırlanır. Kilitleme süreleri en fazla beş saate kadar sürebilir.

## <a name="manage-password-protection-settings"></a>Parola koruma ayarlarını yönetme

Kilitleme eşiği dahil parola koruma ayarlarını yönetmek için:

1. [Azure portalda](https://portal.azure.com) oturum açma
1. Azure AD B2C kiracınızı içeren dizini seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C**seçin.
1. **Güvenlik**altında **kimlik doğrulama yöntemleri (Önizleme)** öğesini seçin ve **parola koruması**' nı seçin.
1. İstediğiniz parola koruma ayarlarınızı girip **Kaydet**' i seçin.

    ![Azure AD ayarlarındaki Azure portal parola koruma sayfası](media/active-directory-b2c-reference-threat-management/portal-02-password-protection.png)
    <br />***Parola koruma** ayarlarında kilitleme eşiğini 5 olarak ayarlama*.

## <a name="view-locked-out-accounts"></a>Kilitli hesapları görüntüleme

Kilitli hesaplar hakkında bilgi edinmek için Active Directory [oturum açma etkinliği raporunu](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)kontrol edebilirsiniz. **Durum**' un altında, **hata**' ı seçin. Kilitli bir hesabı `50053` göstermek için **oturum açma hata kodu** ile oturum açma girişimleri başarısız oldu:

![Kilitli hesabı gösteren Azure AD oturum açma raporunun bölümü](media/active-directory-b2c-reference-threat-management/portal-01-locked-account.png)

Azure Active Directory oturum açma etkinliği raporunu görüntüleme hakkında bilgi edinmek için bkz. [oturum açma etkinlik raporu hata kodları](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
