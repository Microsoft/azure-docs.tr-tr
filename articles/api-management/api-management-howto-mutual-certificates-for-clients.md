---
title: API Management-Azure API Management istemci sertifikası kimlik doğrulaması kullanarak API 'Leri güvenli hale getirme | Microsoft Docs
description: İstemci sertifikalarını kullanarak API 'lere erişimi güvenli hale getirme hakkında bilgi edinin
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 263f8495b9dbb0a1c5b3c54301b4b4deab425e31
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072356"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API Management 'de istemci sertifikası kimlik doğrulaması kullanarak API 'Leri güvenli hale getirme

API Management, istemci sertifikaları kullanarak API 'lere (yani API Management istemci) erişimi güvenli hale getirme yeteneği sağlar. İlke ifadelerini kullanarak, gelen sertifikayı doğrulayabilir ve istenen değerlere göre sertifika özelliklerini denetleyebilirsiniz.

İstemci sertifikaları kullanarak bir API 'nin arka uç hizmetine erişimin güvenliğini sağlama hakkında bilgi için (örneğin, arka uca API Management), bkz. [istemci sertifikası kimlik doğrulaması kullanarak arka uç hizmetlerini güvenli hale](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) getirme

> [!IMPORTANT]
> Tüketim katmanında istemci sertifikalarını almak ve doğrulamak için, önce aşağıda gösterildiği gibi "özel etki alanları" dikey penceresinde "istemci sertifikası ıste" ayarını açmanız gerekir.

![İstemci sertifikası iste](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Veren ve Subject denetleniyor

Aşağıdaki ilkeler bir istemci sertifikasının vereni ve konusunu denetlemek için yapılandırılabilir:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Yerine sertifika iptal listesi kullanımını `context.Request.Certificate.VerifyNoRevocation()` denetlemeyi devre dışı bırakmak için. `context.Request.Certificate.Verify()`
> İstemci sertifikası kendinden imzalanmışsa, `context.Request.Certificate.Verify()` ve ' `context.Request.Certificate.VerifyNoRevocation()` nin çalışması için API Management, kök (veya ara) CA sertifikalarının [yüklenmiş](api-management-howto-ca-certificates.md) olması gerekir.

## <a name="checking-the-thumbprint"></a>Parmak izi denetleniyor

Aşağıdaki ilkeler bir istemci sertifikasının parmak izini denetlemek için yapılandırılabilir:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Yerine sertifika iptal listesi kullanımını `context.Request.Certificate.VerifyNoRevocation()` denetlemeyi devre dışı bırakmak için. `context.Request.Certificate.Verify()`
> İstemci sertifikası kendinden imzalanmışsa, `context.Request.Certificate.Verify()` ve ' `context.Request.Certificate.VerifyNoRevocation()` nin çalışması için API Management, kök (veya ara) CA sertifikalarının [yüklenmiş](api-management-howto-ca-certificates.md) olması gerekir.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>API Management yüklenen sertifikalara yönelik parmak izi denetleniyor

Aşağıdaki örnek, API Management yüklenen sertifikalara karşı bir istemci sertifikasının parmak izini nasıl denet, gösterir:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Yerine sertifika iptal listesi kullanımını `context.Request.Certificate.VerifyNoRevocation()` denetlemeyi devre dışı bırakmak için. `context.Request.Certificate.Verify()`
> İstemci sertifikası kendinden imzalanmışsa, `context.Request.Certificate.Verify()` ve ' `context.Request.Certificate.VerifyNoRevocation()` nin çalışması için API Management, kök (veya ara) CA sertifikalarının [yüklenmiş](api-management-howto-ca-certificates.md) olması gerekir.

> [!TIP]
> Bu [makalede](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) açıklanan istemci sertifikası kilitlenmesi sorunu, tek bir şekilde kendi kendine bildirimde bulunabilir. Örneğin, istekler `403 Forbidden` `context.Request.Certificate` dondurduktan sonra istek zaman aşımından sonra durum koduna neden olur `null`. Bu sorun genellikle yaklaşık `POST` 60 `PUT` KB veya daha büyük içerik uzunluğuna sahip olan istekleri etkiler.
> Bu sorunun oluşmasını engellemek için, "özel etki alanları" dikey penceresindeki istenen ana bilgisayar adları için "istemci sertifikası anlaş" ayarını aşağıda gösterildiği gibi etkinleştirin. Bu özellik, tüketim katmanında kullanılamaz.

![İstemci sertifikası için anlaş](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Sonraki adımlar

-   [İstemci sertifikası kimlik doğrulaması kullanarak arka uç hizmetlerini güvenli hale getirme](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Sertifikaları karşıya yükleme](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
